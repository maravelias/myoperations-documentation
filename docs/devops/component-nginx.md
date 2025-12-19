# Component: Nginx Welcome Page

## Purpose
Provides a friendly landing page on `http://localhost/` that confirms the stack is running and links to other service endpoints. Useful for onboarding and smoke tests.

## Host & Network Access
- Host URL: `http://localhost/`
- Container DNS: `nginx`
- Static IP: `172.30.0.16`
- Image: `nginx:1.26.2-alpine`

## Content Source
- Static files served from `local-dev/nginx/html`, mounted read-only to `/usr/share/nginx/html`.
- Update `index.html` to change the welcome experience; keep instructions concise and link to key services.

## Health & Operations
- Healthcheck: runs `wget -qO- http://127.0.0.1/ | grep -q 'MyOperations Local Stack'` every 10 seconds.
- Logs: `docker compose -f local-dev/docker-compose.yml logs -f nginx`

## Customization Tips
- To add reverse proxy behavior, edit the compose service to mount a custom `nginx.conf` and expose additional ports. Document any new routes promptly.
- If port 80 is occupied on the host, change the mapping to something like `8080:80` and update onboarding docs.

## Troubleshooting
- Failing healthcheck usually indicates the HTML file changed and no longer contains the expected text; update the grep target or restore the copy.
- Browser cache issues: hard-refresh to avoid stale assets after editing the welcome page.
