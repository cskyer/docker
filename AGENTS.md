# Repository Guidelines

## Project Structure & Module Organization

This repository is a collection of independent Docker Compose setups for self-hosted services. Each top-level service directory contains its own `compose.yaml`:

- `n8n/` runs n8n on port `5678` with a named `data` volume.
- `crawl4ai/` runs Crawl4AI on port `11235` with a named `data` volume.
- `postgresql/` runs PostgreSQL on port `5432` by default and includes `create-multiple-postgresql-databases.sh` for optional multi-database initialization.

Keep service-specific files inside the relevant service directory. Document any intentional cross-service volume, network, or port dependency.

## Build, Test, and Development Commands

Run commands from the target service directory, for example `cd n8n`.

- `docker compose up -d`: start the service in the background.
- `docker compose down`: stop and remove service containers and networks.
- `docker compose logs -f`: follow runtime logs.
- `docker compose pull`: fetch newer images before recreating containers.
- `docker compose config`: validate and render the final Compose configuration.

For PostgreSQL, provide required values such as `POSTGRES_PASSWORD` and `POSTGRES_DB` through the environment or a local `.env`.

## Coding Style & Naming Conventions

Use two-space indentation in YAML and keep Compose files named `compose.yaml`. Service, volume, and network names should be lowercase and descriptive. Prefer Compose variable defaults, such as `${POSTGRES_PORT:-5432}`, and required-variable checks for secrets.

Shell scripts should use Bash, start with `#!/bin/bash`, and enable `set -e` and `set -u`.

## Testing Guidelines

There is no automated test suite. Validate changes with `docker compose config` in the affected service directory before committing. For runtime changes, start the service, inspect logs, and verify the expected local port responds.

When editing `postgresql/create-multiple-postgresql-databases.sh`, test against a fresh PostgreSQL volume because entrypoint scripts run only during initial setup.
Ensure the script is executable before fresh initialization: `chmod +x postgresql/create-multiple-postgresql-databases.sh`.

## Commit & Pull Request Guidelines

Recent commits use Conventional Commit style, for example `feat(project): feat add n8n and crawl4ai docker compose`. Follow `type(scope): summary`, using types such as `feat`, `fix`, `chore`, or `docs`.

Pull requests should include the affected service directory, configuration changes, required environment variables, and manual validation performed.

## Security & Configuration Tips

Do not commit real secrets in `.env` files. Keep credentials in local environment files, deployment secrets, or a password manager. Document any new exposed host port and expected access pattern.
