# 01 - Prerequisites

Before you begin with **StartupStack**, ensure you have the following ready.

## 💻 Local Development
- **Docker**: [Install Docker Desktop](https://www.docker.com/products/docker-desktop) (Mac/Windows) or Engine (Linux).
- **Docker Compose**: Included with Docker Desktop. Verify with `docker compose version`.
- **Git**: To clone the repository.

## 🌍 Production (VPS)
- **VPS Server**:
  - Ubuntu 22.04 LTS or Debian 12 recommended.
  - **Minimum Specs**: 2 vCPU, 4GB RAM (for Plane + others). 8GB RAM recommended for smooth performance.
  - **Public IP**: Static IPv4 address.
- **Domain Name**:
  - You need a domain (e.g., `example.com`) to manage DNS records.
  - You will need to point subdomains to your VPS IP.

## ☁️ DNS Records
You will need to create `A` records pointing to your VPS IP address for the following subdomains:

| Record Type | Host | Points To | Purpose |
| :--- | :--- | :--- | :--- |
| `A` | `hs` | `<VPS_IP>` | Headscale (VPN Controller) |
| `A` | `plane` | `<VPS_IP>` | Plane Project Management |
| `A` | `n8n` | `<VPS_IP>` | n8n Automation |
| `A` | `chat` | `<VPS_IP>` | Rocket.Chat |
| `A` | `*` | `<VPS_IP>` | (Optional) Wildcard for convenience |

> **Note**: Only `hs.example.com` will be publicly accessible on port 443. The others will only be resolvable/accessible once connected to the VPN, but having public DNS records makes SSL certificate generation (DNS-01 or HTTP-01) and routing much simpler.
