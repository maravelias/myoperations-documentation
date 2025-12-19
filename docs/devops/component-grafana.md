# Component: Grafana OSS

## Purpose
Visualizes metrics from Prometheus and logs from Loki. Ships with auto-provisioned datasources so developers can focus on building dashboards instead of wiring integrations.

## Host & Network Access
- Host URL: `http://localhost:3000`
- Container DNS: `grafana`
- Static IP: `172.30.0.17`

## Credentials
- Admin user: `admin`
- Admin password: `admin`
- Set via `GF_SECURITY_ADMIN_USER/PASSWORD` environment variables.

## Provisioning
- Datasources defined under `local-dev/grafana/provisioning/datasources/datasource.yml`:
  - Prometheus (default) → `http://prometheus:9090`
  - Loki → `http://172.30.0.15:3100` with header `X-Scope-OrgID: tenant1`
- To add dashboards/datasources, follow Grafana’s provisioning structure inside `local-dev/grafana/provisioning/`.

## Persistence
- Named volume `grafana-storage` preserves SQLite DB, users, folders, and imported dashboards.

## Operations
- Logs: `docker compose -f local-dev/docker-compose.yml logs -f grafana`
- Restart after provisioning changes: `docker compose -f local-dev/docker-compose.yml up -d grafana`
- Import dashboards via UI or commit JSON files under `provisioning/dashboards` (create folder as needed).

## Customization Tips
- For SSO experiments, enable auth providers (`GF_AUTH_*` env vars) but remember to document any port or callback changes.
- If Loki’s IP/subnet changes, update the datasource URL and header definitions before restarting Grafana.

## Troubleshooting
- `Bad Gateway` pages often mean Prometheus/Loki are still starting—check their containers.
- Lost password? Remove `grafana-storage` to reset credentials (destructive: removes dashboards too).
- Datasource errors referencing TLS: confirm URLs remain HTTP; no TLS termination is configured by default.
