# Nginx Reverse Proxy Gateway Design

## Goal

Create an enterprise-ready Docker Compose template for running nginx as a reverse proxy gateway in the `nginx/` service directory.

## Scope

The gateway exposes HTTP and HTTPS entry points, loads nginx configuration from local files, stores access and error logs on the host, and leaves upstream service routing configurable through `conf.d/`.

This template does not hardcode existing services such as n8n, Jenkins, or Verdaccio. Those services can be proxied later by adding server blocks and joining a shared Docker network.

## Files

- `compose.yaml`: defines the nginx gateway container, ports, volumes, networks, restart policy, and health check.
- `.env.example`: documents configurable host ports and timezone.
- `nginx.conf`: provides global nginx worker, logging, gzip, proxy, and security-oriented defaults.
- `conf.d/default.conf`: provides default HTTP server behavior, health check endpoint, and example reverse proxy block.
- `html/50x.html`: provides a local error page for gateway failures.
- `.gitignore`: keeps local environment files, runtime logs, and TLS secrets out of version control.
- `logs/.gitkeep`: keeps the host log directory in version control without committing runtime logs.
- `certs/.gitkeep`: keeps the TLS certificate directory in version control without committing certificates.

## Configuration Design

Use `nginx:1.27-alpine` as the base image. The container name is `nginx-gateway`.

Expose host ports with Compose defaults:

- `NGINX_HTTP_PORT`, default `80`
- `NGINX_HTTPS_PORT`, default `443`

Mount configuration and content as read-only where possible:

- `./nginx.conf` to `/etc/nginx/nginx.conf:ro`
- `./conf.d` to `/etc/nginx/conf.d:ro`
- `./html` to `/usr/share/nginx/html:ro`
- `./certs` to `/etc/nginx/certs:ro`
- `./logs` to `/var/log/nginx`

Use a local bridge network named `network` for nginx itself. Also create a named bridge network named `proxy` so other Compose projects can opt into gateway routing by declaring the same network as external.

## Runtime Behavior

The default server listens on port `80`, returns `200 OK` at `/healthz`, and returns `404` for unmatched paths. TLS server blocks are intentionally left as examples because certificate names and hostnames are deployment-specific.

Proxy defaults preserve standard forwarding headers:

- `Host`
- `X-Real-IP`
- `X-Forwarded-For`
- `X-Forwarded-Proto`

## Validation

Run these commands from `nginx/`:

```bash
docker compose config
docker compose up -d
docker compose logs -f
curl -fsS http://localhost:${NGINX_HTTP_PORT:-80}/healthz
docker compose down
```

`docker compose config` must succeed before committing configuration changes.

## Security Notes

Do not commit real TLS certificates or private keys. Keep them in `nginx/certs/` locally or provide them through deployment secrets.

The default configuration includes conservative security headers and hides the nginx version. Service-specific headers, redirects, HSTS, and access control should be added in individual `conf.d/` server blocks once domains and TLS are known.
