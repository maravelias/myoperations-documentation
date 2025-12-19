# Component: Loki

## Purpose
Stores structured logs for later querying in Grafana. The configuration favors a single-process, filesystem-backed deployment suited for laptops.

## Host & Network Access
- Host API: `http://localhost:3100`
- Container DNS: `loki`
- Static IP: `172.30.0.15`
- Auth is disabled (`auth_enabled: false`) to simplify local experimentation.

## Configuration File
- Path: `local-dev/loki/config.yml` → mounted read-only to `/etc/loki/config.yml`.
- Key settings:
  - `instance_addr` fixed to `172.30.0.15` to match the Docker network IP.
  - `common.storage.filesystem` writes chunks and rules under `/loki` (named volume `loki-data`).
  - Schema `v13`, TSDB mode, daily indexes.
  - Ruler enabled with local rule storage (no default rules ship with the repo).

## Persistence
- Named volume `loki-data` retains log chunks and index files.

## Operations
- Logs: `docker compose -f local-dev/docker-compose.yml logs -f loki`
- Restart after config tweaks: `docker compose -f local-dev/docker-compose.yml up -d loki`
- To push logs, add Promtail or direct Loki API calls from your application (out of scope for this repo, but the endpoint is ready).

## Integration Points
- Grafana datasource "Loki" points to `http://172.30.0.15:3100` and sends the HTTP header `X-Scope-OrgID: tenant1`.
- Because auth is disabled, the header is optional; it’s included to mimic multi-tenant patterns.

## Customization Tips
- Changing the network subnet requires updating `instance_addr` in `config.yml` and the Grafana datasource URL.
- To test multi-tenant auth, set `auth_enabled: true`, configure an auth middleware, and update Grafana headers accordingly.

## Troubleshooting
- Grafana 401 errors: confirm `auth_enabled` remains `false` or configure the correct tenant header.
- If logs accumulate and disk grows, stop the stack, delete `loki-data`, and start again (clears history).
