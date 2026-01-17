# 03 - VPS Setup

This guide covers setting up a fresh VPS (Ubuntu/Debian) for StartupStack.

## 1. Create a Deploy User
It is recommended to run the stack as a non-root user with sudo privileges.

**On the VPS (as root):**
```bash
# Create user 'deploy'
adduser deploy

# Add to sudo group
usermod -aG sudo deploy

# Install basic tools
apt update && apt install -y curl git
```

## 2. SSH Configuration (Secure Access)
Set up SSH keys to access your VPS securely without passwords.

**On your Local Machine (Mac/Linux):**
```bash
# 1. Generate Key (if you don't have one)
ssh-keygen -t ed25519 -f ~/.ssh/vps

# 2. Copy Public Key to VPS
ssh-copy-id -i ~/.ssh/vps.pub deploy@<VPS_IP>
# OR manually: cat ~/.ssh/vps.pub | ssh deploy@<VPS_IP> "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys"

# 3. Test Login
ssh -i ~/.ssh/vps deploy@<VPS_IP>
```

**Optional: Disable Password Authentication**
Once SSH keys are working, disable password login for better security.
```bash
# On VPS:
sudo nano /etc/ssh/sshd_config
# Set:
# PasswordAuthentication no
# PubkeyAuthentication yes

sudo systemctl restart sshd
```

## 3. Clone & Provision
Now that you are logged in as `deploy`:

```bash
# 1. Clone the repo
git clone https://github.com/Celerinc/startupstack.git
cd startupstack

```bash
# 2. Run the Setup Script
chmod +x scripts/setup
./scripts/setup
# Follow the interactive prompts to configure your domain and email.
```

## 4. Review & Launch
The setup script will generate `env/.env.prod` for you.

1.  **Review Configuration**:
    ```bash
    nano env/.env.prod
    ```
    Ensure your `DOMAIN` and `EMAIL` are correct. The script auto-generated secure passwords for you.

2.  **Start the Stack**:
    ```bash
    ./scripts/up prod
    ```
