# 04 - Tailnet Setup (Headscale)

Once the stack is running via `./scripts/up prod`, you need to initialize the "Tailnet" (your private VPN network). This involves two steps: configuring the Headscale controller and joining the VPS host to it.

## 1. Bootstrap Headscale

The Headscale service starts empty. You need to create a namespace (user) so devices can register.

**Script**: `./scripts/bootstrap`

Usage:
```bash
./scripts/bootstrap [username]
# Defaults to 'ssuser' if no username provided
```

**What it does**:
- Checks if the user exists in the running `headscale` container.
- Creates the user if missing.
- This user (e.g., `ssuser`) will own all the devices in your network.

## 2. Join VPS to Tailnet

Your VPS host itself must join the Tailnet so it can route traffic to internal containers and be reachable by other devices.

**Script**: `./scripts/join-tailnet`

Usage:
```bash
./scripts/join-tailnet
```

**What it does (Automated):**
1.  **Installs Tailscale**: Installs the CLI on the host if missing.
2.  **Generates Token**: Asks the `headscale` container for a reusable pre-auth key for the `ssuser` user.
3.  **Authenticates**: Runs `tailscale up` on the host, pointing the login server to your Headscale URL (e.g., `https://hs.example.com`).
4.  **Configures Routing**: Enables DNS and Route acceptance (`--accept-dns --accept-routes`).


## Verification

After running the scripts, verify the VPS is connected:

```bash
tailscale status
# Should show:
# 100.64.0.1  srv1234  ssuser    linux  -
```

And check from the Headscale container:
```bash
docker compose -f compose/compose.yml -f compose/compose.prod.yml --env-file env/.env.prod exec headscale headscale nodes list
```

## 3. Manual Node Registration

Sometimes a device (like a router or server) provides a "node key" (e.g., from the logs) and asks you to register it manually in the CLI.

We provide helper scripts for this:

1.  **Ensure User Exists**:
    ```bash
    ./scripts/create-user myuser
    ```

2.  **Register the Key**:
    ```bash
    ./scripts/register-node myuser mkey:12345...
    ```

This is easier than running the full `docker compose exec` command manually.
