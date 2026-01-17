# 08 - Architecture Deep Dive

StartupStack allows you to run a "Zero Trust" internal tool stack on a standard VPS.

## Core Components

1.  **Tailscale / Headscale**:
    - **Headscale**: A self-hosted implementation of the Tailscale control server.
    - **Tailscale**: The VPN client.
    - **Purpose**: Creates a private, encrypted mesh network. No ports need to be opened on the firewall except for the control plane.

2.  **Caddy (Reverse Proxy)**:
    - Sits at the edge of the internal Docker network.
    - Routes requests based on subdomains (`plane.example.com` -> `plane-web`).
    - Handles TLS termination (Prod) or self-signed certs (Local).
    - Can inspect the `Remote-IP` to ensure requests come from the Tailnet (if configured), although the Firewall is the primary defense.

3.  **Application Containers**:
    - **Plane**: Three-tier architecture (Web, API, Worker). Uses Redis for queues and Postgres for data.
    - **n8n**: Node.js based workflow automation.
    - **Rocket.Chat**: Meteor app backed by MongoDB.

## Data Flow

```mermaid
graph TD
    User-->|HTTPS (443)| HeadscaleServer[Headscale (Public)]
    User-->|VPN Tunnel via UDP| TailscaleInterface[VPS "tailscale0"]
    
    TailscaleInterface -->|Traffic| Caddy
    Caddy -->|Reverse Proxy| InternalNet[Docker Internal Network]
    
    subgraph "Docker Internal"
        Plane
        n8n
        RocketChat
    end
```

## "Local = Prod"

The key to this stack is the shared `compose.yml`.
- **Local**: We use `compose.local.yml` to map ports (e.g., `8080:80`) so you can access them on `localhost`.
- **Prod**: We use `compose.prod.yml` to remove port mappings (locking them inside the container network) and define heavy storage paths (`/data`).

This ensures that the *software* running is identical, only the *network exposure* changes.
