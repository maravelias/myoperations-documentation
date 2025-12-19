# Component: Sonar DB (PostgreSQL 17)

## Purpose
Dedicated PostgreSQL instance used exclusively by SonarQube so code-quality metadata stays isolated from the primary application database.

## Host & Network Access
- Internal service only; no host port is exposed.
- Container DNS: `sonar-db`
- Static IP: `172.30.0.12`

## Credentials
- Database: `sonar`
- User: `sonar`
- Password: `sonar`
- Managed via compose environment variables.

## Persistence
- Named volume `sonar-db-data` mounted at `/var/lib/postgresql/data` keeps all SonarQube metadata.

## Health & Dependencies
- Healthcheck: `pg_isready -U sonar -d sonar`.
- `sonarqube` service declares a `depends_on` that waits for this healthcheck to succeed before booting the application container.

## Operations
- Inspect logs: `docker compose -f local-dev/docker-compose.yml logs -f sonar-db`
- Connect for debugging (from SonarQube container or host via `docker exec`):
  ```bash
  docker exec -it myoperations-sonar-db psql -U sonar -d sonar
  ```

## Customization Tips
- If you need to expose the DB for BI tooling, add a temporary `ports` stanza (e.g., `5433:5432`) but remove it before committing to keep the surface area small.
- Scaling beyond single-node Postgres is out of scope for local dev; keep this lightweight instance untouched unless SonarQube upgrades demand changes.

## Troubleshooting
- If SonarQube logs show `FATAL: password authentication failed`, confirm the env vars match and the volume wasn’t seeded with older credentials. Deleting `sonar-db-data` forces a clean init.
- Stuck in `Restarting`: check disk space and ensure the host has >2 GB free for the Postgres data files.
