# Verdaccio Compose Design

## Goal

Deploy Verdaccio as a private npm registry behind an existing Nginx reverse proxy.

## Chosen Approach

Use a Docker-only reverse proxy network. The Verdaccio container does not publish a host port. Nginx reaches Verdaccio through a shared external Docker network at `http://verdaccio:4873`.

## Files

- `compose.yaml`: starts Verdaccio, mounts persistent storage and configuration, adds health checks, logging limits, resource limits, and the external proxy network.
- `conf/config.yaml`: defines Verdaccio storage, authentication, npm uplink, package access, audit middleware, server timeout, and structured logs.
- `.env.example`: documents deployment variables.

## Security

Verdaccio is not directly reachable from the host network because `compose.yaml` has no `ports` mapping. User self-registration is disabled with `max_users: -1`; publishing and unpublishing require authentication. Secrets are not committed.

## Operations

Operators must create the external Docker network before starting the service:

```bash
docker network create nginx_proxy
```

Nginx should proxy to:

```text
http://verdaccio:4873
```

Configuration validation uses:

```bash
docker compose config
```

