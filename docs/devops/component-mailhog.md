# Component: MailHog

## Purpose
Captures outbound email sent by the application so developers can inspect messages without hitting real SMTP infrastructure.

## Host & Network Access
- SMTP (host): `localhost:1025`
- SMTP (in-network): `mailhog:1025`
- Web UI: `http://localhost:8025`
- Container DNS: `mailhog`
- Static IP: `172.30.0.18`

## Configuration
- Uses the `mailhog/mailhog:latest` image. For deterministic builds you can pin a specific tag once tested.
- No authentication is enabled; this is intentionally insecure for localhost-only use.

## Operations
- Logs: `docker compose -f local-dev/docker-compose.yml logs -f mailhog`
- Reset captured emails by hitting the “Delete all” button in the UI or restarting the container.

## Integration Tips
- Point your application’s SMTP host to `mailhog` (in Docker) or `localhost` (host machine) on port 1025.
- TLS is not supported; configure your mail library for plain SMTP.

## Troubleshooting
- If you can’t reach the UI, ensure no other service binds host port 8025.
- When emails don’t appear, confirm your app isn’t enforcing STARTTLS or authentication, which MailHog ignores by default.
