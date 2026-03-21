# 01 - Prerequisites

Before you begin with StartupStack, ensure the following.

## Local Development
- Docker Desktop (Mac/Windows) or Docker Engine (Linux)
- Docker Compose (`docker compose version`)
- Git

## Production (VPS)
- Ubuntu 22.04 LTS or Debian 12
- Minimum 2 vCPU / 4 GB RAM, recommended 8 GB RAM
- Static public IPv4
- Domain name

## DNS Records
Create `A` records pointing to your VPS IP:

| Record Type | Host | Points To | Purpose |
| :--- | :--- | :--- | :--- |
| `A` | `hs` | `<VPS_IP>` | Headscale (VPN Controller) |
| `A` | `plane` | `<VPS_IP>` | Plane |
| `A` | `n8n` | `<VPS_IP>` | n8n |
| `A` | `s3` | `<VPS_IP>` | MinIO |
| `A` | `*` | `<VPS_IP>` | Optional wildcard |

> Note: `hs.example.com` is the only endpoint that needs public control-plane reachability. App access still requires VPN membership.

## Optional for NanoBot (Slack-first)
- Slack app credentials (bot token + app-level token)
- OpenAI API key
