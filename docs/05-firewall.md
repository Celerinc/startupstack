# 05 - Firewall & Security

StartupStack relies on a strict firewall configuration to ensure that internal services (Plane, n8n, etc.) are **never** exposed to the public internet.

## Security Model

1.  **Public Zone (Internet)**:
    - **Port 22 (SSH)**: Allowed (for management).
    - **Port 443 (HTTPS)**: Allowed (for Caddy/Headscale entrypoint).
    - **Port 80 (HTTP)**: Allowed (usually redirected to 443 by Caddy).
    - All other ports are **BLOCKED**.

2.  **Private Zone (Tailscale)**:
    - **Interface (`tailscale0`)**: Trusted.
    - Traffic coming from the VPN is allowed to access identifying internal ports if necessary, but typically we utilize Caddy to route traffic to the container network.

## Applying Rules

We use `UFW` (Uncomplicated Firewall) on Ubuntu.

**Script**: `./scripts/firewall-rules`

Usage:
```bash
sudo ./scripts/firewall-rules
```

**What it does**:
- Resets UFW to defaults (Deny Incoming, Allow Outgoing).
- Allows SSH.
- Allows 443/tcp.
- Allows traffic on the `tailscale0` interface.
- Enables the firewall.

## Lockdown (Optional)

Docker manipulation of `iptables` can sometimes bypass UFW. To strictly enforce rules, we provide a lockdown script.

**Script**: `./scripts/firewall-lockdown`

This script is an advanced measure to ensure Docker doesn't inadvertently expose ports. Use with caution and inspect the script before running.

## Verification

Check your firewall status:
```bash
sudo ufw status numbered
```
You should see `443` allowed from Anywhere, and `tailscale0` allowed.
Ensure you cannot access `http://<VPS_IP>:8080` (Plane) or `http://<VPS_IP>:5678` (n8n) from a public (non-VPN) IP.
