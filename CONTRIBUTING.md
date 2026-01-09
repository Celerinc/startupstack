# Contributing to StartupStack

Thanks for your interest in **StartupStack**.

StartupStack is intentionally **opinionated**, **infra‑focused**, and built from real internal usage.

---

## How to contribute

Before opening issues or pull requests, please contact us:

📩 **contact@celerinc.com**

We prefer discussion first, especially for:
- architectural changes
- security‑sensitive topics
- production behavior changes

---

## What we welcome

- Compose improvements
- Security hardening
- Documentation improvements
- Backup & recovery strategies
- Observability suggestions

---

## Branching + commit conventions (simple GitFlow)

We keep it lightweight:

- `main` → stable, production-ready
- `develop` → integration branch
- feature work → `feature/<short-name>`
- fixes → `fix/<short-name>`
- docs-only → `docs/<short-name>`
- refactors → `refactor/<short-name>`
- chores/tooling → `chore/<short-name>`

### Commit messages

Use conventional-ish prefixes (no ceremony, just clarity):

- `feat:` new capability
- `fix:` bug fix
- `docs:` documentation only
- `refactor:` code/compose refactor, no behavior change
- `chore:` tooling, maintenance, deps, formatting

Examples:
- `fix: run Plane web on port 3000 behind Caddy`
- `fix: n8n volume permissions on VPS`
- `docs: clarify Caddy basic_auth + password hashing`
- `refactor: simplify compose overrides (local vs prod)`

---

## Pull requests

1) Create a branch from `develop`  
2) Keep changes focused (one theme per PR)  
3) Update docs if behavior changes (README, env example, scripts)  
4) Open a PR targeting `develop`  
5) In the PR description, include:
   - What changed + why
   - How to test locally (`./scripts/up local`)
   - How to validate in prod (`./scripts/up prod`) if relevant
   - Any breaking changes / migration steps

### PR checklist

- [ ] Images remain pinned (no `latest`)
- [ ] Local and prod still match (only overrides differ)
- [ ] Secrets are not committed
- [ ] `/data` persistence still works in production
- [ ] n8n volume permissions documented or handled by setup script
- [ ] Caddy routing matches the real internal ports

---

## Roadmap

### Short term
- Plane worker hardening
- Backup scripts
- IP allow‑listing at Caddy level

### Mid term
- Optional ClickHouse profile
- Observability stack
- SSO / OIDC examples

### Long term
- Multi‑node patterns
- External DB patterns
- Opinionated presets

---

## Principles

- Local == Production
- Explicit over clever
- Reproducible over convenient

---

📩 **contact@celerinc.com**
