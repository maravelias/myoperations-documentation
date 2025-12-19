# Component: pgAdmin 4

## Purpose
Browser-based GUI for managing the Postgres cluster running inside the local stack. Pre-provisioned with a connection to the `postgres` service so new developers can inspect schemas immediately.

## Host & Network Access
- Host URL: `http://localhost:5050`
- Container DNS: `pgadmin`
- Static IP: `172.30.0.19`
- Published from container port 80 → host 5050.

## Credentials
- Email: `admin@local.com`
- Password: `admin`
- Stored via environment variables `PGADMIN_DEFAULT_EMAIL` and `PGADMIN_DEFAULT_PASSWORD` in the compose file.

## Configuration Artifacts
- `local-dev/pgadmin/servers.json` is mounted read-only to `/pgadmin4/servers.json`. It defines the "PostgreSQL Server" entry pointing to `172.30.0.10` with SSL disabled for local use.
- To add more servers, edit `servers.json`, then restart pgAdmin (`docker compose ... up -d pgadmin`).

## Persistence
- Named volume `pgadmin-data` keeps user settings, saved queries, and connection history between restarts.

## Operations
- Logs: `docker compose -f local-dev/docker-compose.yml logs -f pgadmin`
- Health: relies on Docker restart policy; no explicit healthcheck, so treat container status as readiness.

## Customization Tips
- To change the admin password, update the environment variable and delete the `pgadmin-data` volume (existing volume keeps the old hash).
- For HTTPS, front pgAdmin with the included Nginx service or configure SSL inside pgAdmin (out of scope for default local dev).

## Troubleshooting
- If the pre-provisioned server disappears, remove `pgadmin-data` to rehydrate from `servers.json`.
- 404/403 errors usually indicate the container restarted mid-session; refresh after verifying `docker ps`.
