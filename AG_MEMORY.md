# AG_MEMORY.md (Antigravity Context)

## Project Identity: "StartupStack"
This project is an **infrastructure-only** repository designed to spin up a complete internal tool stack for early-stage startups.

### Core Philosophy
1.  **Simplicity over Flexibility**: "One stack. Same manifests. Different env values."
2.  **Anti-Fragile**: Avoid "works on my machine" issues by using the exact same `compose.yml` for local and prod, with only minimal overrides in `compose.local.yml` and `compose.prod.yml`.
3.  **Security First**: The "Zero Trust-ish" model. Nothing is exposed publicly except the VPN entry point. All actual business apps (Plane, n8n, etc.) live inside the private Tailscale network.

### Repository Patterns
- **Root**: Clean, contains only high-level docs and config.
- **`compose/`**: All Docker definitions.
  - `compose.yml`: THE source of truth. Do not duplicate service definitions unless absolutely necessary.
  - `compose.[env].yml`: Only for `ports` (local) or `volumes`/`expose` (prod).
- **`scripts/`**: Bash scripts tailored for Ubuntu/Debian VPS. They handle the "glue" that Docker Compose cannot (host directories, permissions, firewall rules).
- **`env/`**: Storage for environment files. `.env.prod` is gitignored.

### Key Context for Future Tasks
- **Headscale**: We use Headscale, not the managed Tailscale SaaS. This means we are our own VPN provider.
- **Caddy**: Used as the edge proxy. In Prod, it handles TLS (Let's Encrypt) automatically. In Local, it handles routing but TLS is often skipped or self-signed.
- **Data Persistence**: All production data lives in `/data` on the host, mapped into containers. This is critical for backups.

### Implementation Preferences
- **Bash**: Scripts should be strict (`set -euo pipefail`).
- **Docker**: Always pin versions in `compose.yml`. No `latest`.
- **Markdown**: Use GitHub-flavored markdown. Mermaid diagrams for architecture are encouraged.

### Common Tasks
- **Adding a Service**:
    1.  Add to `compose/compose.yml` (base image, internal networks).
    2.  Add volumes to `volumes:` section.
    3.  Add specific env vars to `env/.env.example`.
    4.  Expose port in `compose.local.yml` (for local dev).
    5.  Expose port (internally) or mount host paths in `compose.prod.yml`.
    6.  Update `compose/caddy/Caddyfile` if it needs a subdomain.
