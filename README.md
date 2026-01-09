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
│   └── compose.prod.yml      # prod overrides (HTTPS + /data binds)
├── env/
│   ├── .env.example          # documented variables (no secrets)
│   ├── .env.local            # local values (gitignored)
│   └── .env.prod             # production values (gitignored)
├── scripts/
│   ├── up
│   ├── down
│   └── setup                 # one-time VPS bootstrap
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

Local mode exposes ports directly on your machine (no Caddy needed).

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

### VPS initial setup (one-time)

Before starting the stack on a fresh VPS, run the bootstrap script once.
It installs Docker, Docker Compose v2, sets permissions, and prepares `/data` for persistence.

```bash
chmod +x scripts/setup-vps.sh
./scripts/setup-vps.sh
```

If the script adds your user to the docker group, log out and back in before continuing.

### Run in production (VPS)

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

## Reverse proxy notes (Caddy)

### Plane frontend port

The Plane frontend container listens on **port 3000** internally (not 80).  
So your Caddyfile should route to `plane-web:3000`:

```caddy
plane.<domain> {
  reverse_proxy plane-web:3000
}
```

### Plane worker command

The backend image includes the worker entrypoint at:

- `/code/bin/docker-entrypoint-worker.sh`

So the worker service should use:

```yaml
command: ["/code/bin/docker-entrypoint-worker.sh"]
```

### Protecting endpoints

A default Basic Auth in Caddy is recommended for anything internal (Plane, n8n, Rocket.Chat), especially while bringing the stack up.

Example (using env vars from `.env.prod`):

```caddy
:443 {
  basic_auth {
    {$CADDY_BASIC_AUTH_USER} {$CADDY_BASIC_AUTH_HASH}
  }
}
```

To generate a hash:

```bash
docker run --rm caddy:2.8.4 caddy hash-password --plaintext 'your-password'
```

---

## Persistence

Persistent data is stored in Docker volumes (base compose):

- PostgreSQL (Plane)
- MinIO (uploads)
- n8n state
- MongoDB (Rocket.Chat)
- Caddy certificates

On a VPS, volumes are bound to a persistent disk (e.g. `/data`) via `compose.prod.yml`.

> Note: n8n runs as a non‑root user. On a VPS, ensure `/data/n8n` is writable by UID/GID `1000:1000` (handled by `setup-vps.sh`).

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
