# Component: Keycloak 26.x

## Purpose
Identity and Access Management (IAM) for the local MyOperations ecosystem. Ships with the `MyOperations` realm, sample users, and custom login theme so frontend/backends can test OAuth/OIDC flows without external dependencies.

## Host & Network Access
- Host URL: `http://localhost:5080`
- Container port 8080 → host 5080
- DNS: `keycloak`
- Static IP: `172.30.0.11`

## Credentials & Realm
- Admin console: `admin` / `admin`
- Realm import file: `local-dev/keycloak/MyOperations-realm.json` (mounted read-only and applied via `start-dev --import-realm`).
- Sample users:
  - `admin` / `admin` → role `SYSADM`
  - `sr1` / `password` → role `SR`
  - `pm1` / `password` → role `PM`
- OAuth clients:
  - `myoperations-frontend` (public)
  - `myoperations-backend` (confidential; secret `changeme-in-prod`)

## Database Configuration
- Uses the primary Postgres service via env vars `KC_DB_*`.
- JDBC target: `postgres:5432`, database `keycloak`, username/password `keycloak`.
- All Keycloak data persists in the shared `postgres-data` volume.

## Custom Theme
- Folder: `local-dev/keycloak/themes/myoperations`
- Notable files: `login/theme.properties`, `login/resources/css/myoperations.css`, `login/resources/messages/messages_en.properties`, `login/resources/login.ftl`, `login/resources/template.ftl`, `login/resources/img/logo.png`.
- To apply edits, change files then restart Keycloak (`docker compose -f local-dev/docker-compose.yml up -d keycloak`).

## Health & Operations
- Healthcheck uses a TCP open test on `127.0.0.1:8080` with 10 retries.
- Startup (first run) can take 1–3 minutes while importing the realm and building caches.
- Logs: `docker compose -f local-dev/docker-compose.yml logs -f keycloak`

## Customization Tips
- Update redirect URIs by editing the realm JSON or using the admin console; export changes back to `MyOperations-realm.json` for reproducible setups.
- If you change the Keycloak host port, update application redirect URLs and Keycloak links in docs immediately.
- For persistent DB changes, back up the `postgres-data` volume or dump the `keycloak` schema before destructive resets.

## Troubleshooting
- Realm import skipped: ensure you removed the previous Postgres data (`postgres-data` volume). Keycloak imports only on a clean database.
- Styling cache issues: hard-refresh your browser or open a private tab after theme changes.
- Invalid_grant errors typically point to mismatched client secrets; confirm `myoperations-backend` secret in both Keycloak and your app.
