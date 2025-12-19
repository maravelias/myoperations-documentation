# Component: Postgres 17 with pgvector

## Purpose
Primary relational database for the MyOperations application plus backing store for supporting services (Keycloak schema lives here). Image `pgvector/pgvector:pg17` gives PostgreSQL 17 with the pgvector extension preloaded for AI/search features.

## Host & Network Access
- Host port: `localhost:5432`
- Container DNS: `postgres`
- Static IP on `myoperations-network`: `172.30.0.10`
- Compose service name: `postgres`

## Credentials & Databases
| Database  | User      | Password  | Notes                              |
|-----------|-----------|-----------|------------------------------------|
| operations| postgres  | P@ssw0rd  | Main application schema            |
| keycloak  | keycloak  | keycloak  | Created via init script for Keycloak|

Initialization scripts live under `local-dev/postgres/init/`:
- `01_pgvector.sql` enables the extension on the `operations` database.
- `20-keycloak.sql` provisions the `keycloak` database and user.

## Persistence & Volumes
- Named volume `postgres-data` mapped to `/var/lib/postgresql/data` keeps WAL + data files between restarts.
- Never bind-mount host paths with secrets; rely on this Docker-managed volume.

## Health & Operations
- Healthcheck: `pg_isready -U postgres -d operations` (interval 10s, retries 5).
- Common commands:
  ```bash
  docker compose -f local-dev/docker-compose.yml logs -f postgres
  docker exec -it myoperations-postgres psql -U postgres -d operations
  docker volume inspect myoperations-local-stack_postgres-data
  ```

## Customization Tips
- Override ports by editing `local-dev/docker-compose.yml`, but keep docs in sync.
- Additional schemas/users: add SQL scripts under `local-dev/postgres/init/NN-name.sql`; they run alphabetically on first container start.
- If you change credentials, also update dependent services (Keycloak env vars, `pgadmin/servers.json`, etc.).

## Troubleshooting
- Connection refused: ensure host port 5432 is not taken; run `docker ps` to confirm container status.
- Authentication errors: reset passwords via `psql` or recreate the stack (removing `postgres-data`).
- Re-import init scripts: remove the named volume to force clean initialization (destructive).
