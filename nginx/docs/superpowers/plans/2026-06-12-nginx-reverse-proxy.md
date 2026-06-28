# Nginx Reverse Proxy Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build an enterprise-ready Docker Compose template for nginx as a reverse proxy gateway.

**Architecture:** The service is self-contained under `nginx/`. Docker Compose starts a single nginx gateway container, mounts local configuration read-only, writes logs to a local directory, and joins a local bridge network plus a named proxy network that other Compose projects can join.

**Tech Stack:** Docker Compose, nginx `1.27-alpine`, POSIX shell validation through Docker Compose.

---

### Task 1: Gateway Files

**Files:**
- Create: `compose.yaml`
- Create: `.env.example`
- Create: `nginx.conf`
- Create: `conf.d/default.conf`
- Create: `html/50x.html`
- Create: `logs/.gitkeep`
- Create: `certs/.gitkeep`
- Create: `.gitignore`

- [ ] **Step 1: Create Docker Compose definition**

Create `compose.yaml` with the nginx gateway service, port variables, mounted configuration, health check, and networks.

- [ ] **Step 2: Create environment example**

Create `.env.example` documenting `NGINX_HTTP_PORT`, `NGINX_HTTPS_PORT`, and `TZ`.

- [ ] **Step 3: Create nginx global configuration**

Create `nginx.conf` with global worker settings, logs, gzip, proxy defaults, and secure baseline headers.

- [ ] **Step 4: Create default server configuration**

Create `conf.d/default.conf` with `/healthz`, default 404 behavior, error page handling, and commented reverse proxy examples.

- [ ] **Step 5: Create local support files**

Create `html/50x.html`, `logs/.gitkeep`, and `certs/.gitkeep`.

- [ ] **Step 6: Create ignore rules**

Create `.gitignore` to ignore local `.env`, runtime logs, and TLS certificate material while keeping `.env.example` and `.gitkeep` files trackable.

### Task 2: Validation

**Files:**
- Verify: `compose.yaml`
- Verify: `nginx.conf`
- Verify: `conf.d/default.conf`

- [ ] **Step 1: Render Compose configuration**

Run: `docker compose config`

Expected: command exits with status `0` and renders the final Compose configuration.

- [ ] **Step 2: Inspect repository status**

Run: `git status --short`

Expected: only `nginx/` task files appear as new changes, plus any pre-existing unrelated changes remain untouched.
