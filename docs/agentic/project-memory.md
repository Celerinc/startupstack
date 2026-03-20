# Project Memory

Shared, non-sensitive context for agents and contributors.

## Project Identity
- StartupStack is an infrastructure-only repository for running a low-cost internal startup stack on a VPS.

## Core Principles
- One base manifest (`compose/compose.yml`) with minimal env overlays.
- Prefer operational simplicity and low drift between local and production.
- Security-first defaults: private internal apps behind VPN access.

## Repository Patterns
- `compose/`: source-of-truth service definitions and environment overrides.
- `scripts/`: strict Bash automation for provisioning and operations.
- `env/`: env templates and local/prod configs (`.env.prod` is sensitive and untracked).
- `docs/`: runbooks and architecture documentation.

## Operating Preferences
- Bash scripts use `set -euo pipefail`.
- Docker images should be version-pinned (avoid `latest`).
- Keep edits minimal and scoped; avoid unrelated refactors.

## Service-Change Checklist
1. Add/update service in `compose/compose.yml`.
2. Add required variables in `env/.env.example`.
3. Add local exposure in `compose/compose.local.yml` only when needed.
4. Add production exposure/volume mapping in `compose/compose.prod.yml`.
5. Update `compose/caddy/Caddyfile` for any new routing/subdomain.
6. Update docs when operator behavior changes.
