# 06 - Client Access (VPN)

To access your internal apps (`plane.example.com`, `twenty.example.com`, `n8n.example.com`), your computer must join the Tailnet.

## 1. Generate an Auth Key

Each device needs a unique key to join the network.

**Script**: `./scripts/vpn-keygen`

Usage:
```bash
./scripts/vpn-keygen <device-name>
# Example: ./scripts/vpn-keygen my-macbook
```

This will output a command you can copy-paste, or just the Auth Key.

## 2. Connect Your Device

### macOS / Windows
1.  **Install Tailscale**: Download from [tailscale.com](https://tailscale.com/download).
2.  **Configure Server**:
    - **Hold `Shift` (or `Alt` on Windows)** and click the Tailscale menu bar icon.
    - Go to **Debug** -> **Add Account...** (or "Log in to custom server...").
    - Enter your Headscale URL: `https://hs.example.com` (Your domain).
3.  **Authenticate**:
    - Paste the Auth Key generated in Step 1.

### Linux
Run the following command (replace with your values):

```bash
tailscale up \
  --login-server=https://hs.example.com \
  --auth-key=<YOUR_GENERATED_KEY> \
  --accept-dns=true
```

## 3. Verify Connection

Once connected:
1.  Check your IP:
    ```bash
    tailscale ip
    # Should start with 100.64.x.x
    ```
2.  Ping the VPS:
    ```bash
    ping 100.64.0.1  # Assuming VPS is .1
    ```
3.  Access Apps:
    Open your browser and visit:
    - [https://plane.example.com](https://plane.example.com)
    - [https://twenty.example.com](https://twenty.example.com)
    - [https://n8n.example.com](https://n8n.example.com)

## DNS Troubleshooting
If you cannot load pages by domain name, ensure your computer is using the "MagicDNS" provided by Headscale/Tailscale.
- On Mac: Check System Settings -> Network -> Tailscale -> DNS.
- Verification: `dig +short plane.example.com` should return the VPS Tailscale IP (or Public IP, depending on your setup, but Access Control requires VPN).
- Verification: `dig +short twenty.example.com` should return the VPS Tailscale IP (or Public IP, depending on your setup, but Access Control requires VPN).
