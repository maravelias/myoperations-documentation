# Component: SonarQube Community

## Purpose
Runs static analysis and code quality gates for the MyOperations application. Uses the official Community Edition image pinned to `sonarqube:25.10.0.114319-community` so behavior stays predictable.

## Host & Network Access
- Host URL: `http://localhost:9000`
- Container DNS: `sonarqube`
- Static IP: `172.30.0.13`
- Depends on the `sonar-db` service being healthy before startup.

## Credentials & First Login
- Default credentials: `admin` / `admin`
- On first login you must set a new password; document it for your local team but never commit it.
- Generate tokens via **My Account → Security → Generate Token** for CI usage.

## Database Configuration
- JDBC URL: `jdbc:postgresql://sonar-db:5432/sonar`
- Username/password: `sonar` / `sonar`
- Configured via environment variables in `local-dev/docker-compose.yml`.

## Persistence
- `sonar-data` → `/opt/sonarqube/data` (Elasticsearch index + config)
- `sonar-extensions` → `/opt/sonarqube/extensions` (plugins)
- Back up these named volumes if you experiment with plugins so you can roll back quickly.

## Operations
- Logs: `docker compose -f local-dev/docker-compose.yml logs -f sonarqube`
- Healthcheck: not declared; rely on the container status and the built-in `/api/system/health` endpoint.
- Restart after config tweaks: `docker compose -f local-dev/docker-compose.yml up -d sonarqube`

## Local Scanning Options
- Maven/Gradle plugins (preferred) pointing to `http://localhost:9000`.
- Dockerized CLI example:
  ```bash
  docker run --rm \
    -e SONAR_HOST_URL=http://host.docker.internal:9000 \
    -e SONAR_LOGIN=<token> \
    -v "$(pwd)":/usr/src \
    sonarsource/sonar-scanner-cli
  ```

## Customization Tips
- Pin plugin versions when adding them under `sonar-extensions` to avoid silent upgrades.
- If you need LTS instead of the pinned feature release, update the compose file *and* this documentation in tandem.

## Troubleshooting
- `java.lang.OutOfMemoryError`: ensure Docker Desktop allocates ≥6 GB RAM; SonarQube bundles Elasticsearch.
- `Database needs upgrade`: bring the stack up after `docker compose ... pull` to ensure the DB migrates automatically; never reuse an older DB schema with a newer app image.
- `vm.max_map_count` errors on Linux: run `sudo sysctl -w vm.max_map_count=262144` (documented in README).
