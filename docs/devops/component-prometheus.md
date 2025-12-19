# Component: Prometheus

## Purpose
Collects metrics from itself and from the developer’s running application so Grafana dashboards have time-series data. Image pinned at `prom/prometheus:v2.54.1` for deterministic scrape behavior.

## Host & Network Access
- Host URL: `http://localhost:9090`
- Container DNS: `prometheus`
- Static IP: `172.30.0.14`
- Exposes only HTTP (no auth) for simplicity in local dev.

## Configuration File
- Mounted from `local-dev/prometheus/prometheus.yml` into `/etc/prometheus/prometheus.yml` (read-only).
- Jobs:
  - `prometheus`: scrapes `localhost:9090` (self-monitoring).
  - `myoperations-app`: scrapes `/actuator/prometheus` on configurable host targets.
- Default targets include `172.17.0.1:8080`, `172.30.0.1:8080`, and `192.168.56.1:8080`. Uncomment `host.docker.internal:8080` for Docker Desktop if needed.

## Persistence
- Named volume `prometheus-data` mounted at `/prometheus` stores TSDB blocks.

## Operations
- Reloads require a container restart (hot reload not enabled by default). After editing the config file, run:
  ```bash
  docker compose -f local-dev/docker-compose.yml up -d prometheus
  ```
- Logs: `docker compose -f local-dev/docker-compose.yml logs -f prometheus`

## Integration Points
- Grafana datasource named "Prometheus" points to `http://prometheus:9090`.
- Documented developer workflow: ensure your app exposes `/actuator/prometheus` (Spring Boot example) on host port 8080.

## Customization Tips
- To add more jobs (e.g., scraping Keycloak health metrics), edit `prometheus.yml` and keep comments synchronized with the actual ports.
- If you change the network subnet, update any static IP references inside the config.

## Troubleshooting
- Targets stuck in `DOWN`: verify host application is reachable from inside the Docker network. From the Prometheus container, try `curl 172.30.0.1:8080/actuator/prometheus`.
- Disk usage creep: prune old TSDB data by stopping Prometheus, deleting `prometheus-data`, and restarting (destructive but often acceptable for local dev).
