# 08 - Architecture Deep Dive

StartupStack runs a zero-trust internal tool stack on a standard VPS.

## Core Components

1. Headscale / Tailscale:
   - Headscale: self-hosted Tailscale control server.
   - Tailscale: client VPN.
   - Purpose: private encrypted mesh network.

2. Caddy (Reverse Proxy):
   - Routes internal app subdomains.
   - Handles TLS in production.
   - Can enforce VPN-only access rules.

3. Application Containers:
   - Plane: web/API/worker architecture with Postgres/Redis/MinIO.
   - Twenty: CRM service with Postgres/Redis and S3-compatible object storage.
   - n8n: workflow automation engine.
   - NanoBot (optional): AI gateway + CLI, Slack-first integration.

## Storage Model

- One shared MinIO service is used for object storage.
- Isolation is done by bucket:
  - `plane` for Plane
  - `twenty` for Twenty
- This allows app-level separation while keeping infrastructure simpler and backups centralized.

## Data Flow

```mermaid
graph TD
    User-->|HTTPS (443)| HeadscaleServer[Headscale (Public)]
    User-->|VPN Tunnel| TailscaleInterface[VPS tailscale0]

    TailscaleInterface -->|Traffic| Caddy
    Caddy -->|Reverse Proxy| InternalNet[Docker Internal Network]

    subgraph "Docker Internal"
        Plane
        n8n
        NanoBot
    end
```

## Local = Prod

The key is shared `compose.yml`.
- Local: `compose.local.yml` exposes selected ports.
- Prod: `compose.prod.yml` keeps apps internal and binds persistent `/data` paths.

The software remains consistent; only exposure/storage strategy changes.
