# StartupStack

Production‑grade **internal company stack** designed to run **locally and in production** using the **same Docker Compose setup**, with environment‑specific overrides.

This repository is **infra‑only** (Docker Compose + config).  
It does **not** vendor or fork upstream application source code.

---

## What’s inside (mandatory)

- **Plane** — project & issue management  
- **n8n (v2)** — automation / workflow engine  
- **Rocket.Chat** — internal chat (channels, DMs, bots)  
- **PostgreSQL** — Plane database  
- **Redis** — Plane cache / queues  
- **MinIO** — S3‑compatible object storage (Plane uploads)  
- **MongoDB** — Rocket.Chat database (single‑node replica set)  
- **Caddy** — reverse proxy + automatic HTTPS (production)

### Optional (provided but disabled)
- **ClickHouse** — analytics / large‑scale aggregations (commented out)

---

## Philosophy

- **One stack definition**
- **Same behavior locally and in production**
- Differences handled only by:
  - environment files
  - compose override files
- No “works on my machine” drift
- All images **pinned** (no `latest`)

---

## Prerequisites

- Docker Desktop (Mac) or Docker Engine (Linux / VPS)
- Docker Compose v2 (`docker compose`)
- (Production) DNS `A` records pointing to the server:
  - `plane.<domain>`
  - `n8n.<domain>`
  - `chat.<domain>`

---

## Repository structure

```
startupstack/
├── compose/
│   ├── compose.yml           # base (shared, pinned images)
│   ├── compose.local.yml     # local overrides (ports)
│   └── compose.prod.yml      # prod overrides (HTTPS only)
├── env/
│   ├── .env.example          # documented variables (no secrets)
│   ├── .env.local            # local values (gitignored)
│   └── .env.prod             # production values (gitignored)
├── scripts/
│   ├── up
│   └── down
├── Caddyfile                 # TLS + routing (prod)
├── README.md
├── CONTRIBUTING.md
└── LICENSE
```

---

## Environment configuration

All configuration is done through env files.

- `env/.env.local` → local development
- `env/.env.prod` → production (VPS)

Same keys, different values.

**Never commit secrets.**

Use the example file to bootstrap:
```bash
cp env/.env.example env/.env.local
cp env/.env.example env/.env.prod
```

---

## Run locally (development)

Local mode exposes ports directly on your machine.

Start:
```bash
./scripts/up local
```

Services:
- Plane: http://localhost:8080
- n8n: http://localhost:5678
- Rocket.Chat: http://localhost:3000
- MinIO console: http://localhost:9001

Stop:
```bash
./scripts/down local
```

---

## Run in production (VPS)

Production mode exposes **only ports 80/443** via Caddy.

Start:
```bash
./scripts/up prod
```

Services:
- Plane: `https://plane.<domain>`
- n8n: `https://n8n.<domain>`
- Rocket.Chat: `https://chat.<domain>`

Stop:
```bash
./scripts/down prod
```

---

## Persistence

Persistent data is stored in Docker volumes:

- PostgreSQL (Plane)
- MinIO (uploads)
- n8n state
- MongoDB (Rocket.Chat)
- Caddy certificates

On a VPS, volumes should be bound to a persistent disk
(e.g. `/data`) via `compose.prod.yml`.

---

## Automation & webhooks (Plane → n8n)

Local:
```
http://host.docker.internal:5678/webhook/<workflow-id>
```

Production:
```
https://n8n.<domain>/webhook/<workflow-id>
```

Same workflow, same logic — only the base URL changes.

---

## Rocket.Chat integrations

Rocket.Chat is intended to be used with:
- Bots
- Webhooks
- n8n workflows

Typical flows:
- Plane issue created → notify channel
- Status change → DM assignees
- Project created → auto‑create channel

---

## Backup strategy (recommended)

- **PostgreSQL**: nightly `pg_dump` → off‑site storage
- **MongoDB**: periodic dump → off‑site storage
- **MinIO**: bucket sync / replication
- **Config**: Git

VM snapshots alone are **not** sufficient.

---

## Future extensions

- Enable ClickHouse for analytics
- Add VPN (WireGuard / Tailscale)
- Add SSO (OIDC) at the Caddy or app level
- Externalize databases if scale requires it

---

## Contributing

See **[CONTRIBUTING.md](CONTRIBUTING.md)** (includes roadmap + how to propose changes).

Contact: **contact@celerinc.com**

---

## Golden rule

> If it runs locally, it must run in production  
> without rewriting anything.

Only environment files and compose overrides may differ.

---

## License

MIT — see `LICENSE`
