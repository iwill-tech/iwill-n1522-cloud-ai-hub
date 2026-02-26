# N1522 Cloud AI Hub - Installation Guide

**Hardware:** IWILL N1522 with 16GB DDR5 + 1TB NVMe SSD  
**Network:** Connected to N1241 Port 3 (AI_LAB - 192.168.20.0/24)  
**OS:** Ubuntu 24.04 LTS Server  
**Purpose:** Cloud AI orchestration with OpenClaw

**Total Time:** ~1 hour

---

## What You're Building

```
Internet (560 Mbps)
    ↓
N1241 AI Firewall (Zenarmor protection)
    ↓
Port 3 (AI_LAB) - 192.168.20.0/24
    ↓
N1522 Cloud AI Hub
    ├── Ubuntu 24.04 Server
    ├── OpenClaw (orchestration)
    ├── Docker (containers)
    ├── Tailscale (remote access)
    └── Cloud API connections
        ├── Claude Opus 4.6
        ├── Kimi 2.5
        └── Other APIs
```

---

## Prerequisites

- [ ] N1522 with 16GB RAM + 1TB SSD installed
- [ ] USB flash drive (4GB+) for Ubuntu installer
- [ ] Keyboard + monitor (for initial setup)
- [ ] Ethernet cable (to N1241 Port 3)
- [ ] Another computer to create USB installer

---

## Phase 1: Prepare Installation Media (10 min)

### Download Ubuntu Server

1. Go to: https://ubuntu.com/download/server
2. Download: **Ubuntu 24.04 LTS Server** (ISO file)
3. File: `ubuntu-24.04-live-server-amd64.iso`

### Create Bootable USB

**On Windows (Rufus):**
1. Download Rufus: https://rufus.ie
2. Insert USB drive
3. Open Rufus:
   - Device: Select your USB
   - Boot selection: SELECT → choose Ubuntu ISO
   - Partition scheme: **GPT**
   - Target system: **UEFI**
   - Click **START**
4. Wait ~5 minutes

**On Mac:**
```bash
# Convert ISO to IMG
hdiutil convert -format UDRW -o ubuntu.img ubuntu-24.04-live-server-amd64.iso

# Find USB device
diskutil list

# Unmount USB (replace diskX)
diskutil unmountDisk /dev/diskX

# Write image (replace diskX)
sudo dd if=ubuntu.img.dmg of=/dev/rdiskX bs=1m

# Eject
diskutil eject /dev/diskX
```

**On Linux:**
```bash
# Find USB device
lsblk

# Write ISO (replace sdX)
sudo dd if=ubuntu-24.04-live-server-amd64.iso of=/dev/sdX bs=4M status=progress
sync
```

---

## Phase 2: Install Ubuntu Server (20 min)

### Physical Setup

1. **Connect N1522:**
   - Keyboard + monitor
   - Ethernet to N1241 Port 3
   - Power adapter
   - Insert USB installer

2. **Boot from USB:**
   - Power on N1522
   - Press **F7** or **DEL** for boot menu (if needed)
   - Select USB drive

### Ubuntu Installation

**1. Language Selection:**
- Choose: **English**

**2. Keyboard Configuration:**
- Layout: **English (US)** or your preference

**3. Installation Type:**
- Choose: **Ubuntu Server**

**4. Network Configuration:**
- Should auto-detect ethernet
- Should get IP via DHCP: 192.168.20.x ✅
- If not: Configure manually:
  - IPv4: DHCP
  - Leave defaults

**5. Proxy:**
- Leave blank (skip)

**6. Ubuntu Archive Mirror:**
- Use default (or choose Bulgarian mirror if faster)

**7. Storage Configuration:**
- Choose: **Use entire disk**
- Select: 1TB NVMe SSD
- Partition scheme: **Default (LVM)**
- Confirm: **Continue**

**8. Profile Setup:**
- Your name: `admin` (or your choice)
- Server name: `n1522-ai-hub`
- Username: `admin`
- Password: (choose strong password - save it!)
- Confirm password

**9. SSH Setup:**
- ✅ **Install OpenSSH server** (check this!)
- Import SSH keys: **No** (skip for now)

**10. Featured Server Snaps:**
- Don't select anything (skip)
- We'll install what we need later

**11. Installation:**
- Confirm and begin installation
- Wait ~10 minutes
- When done: **Reboot Now**
- Remove USB drive when prompted

---

## Phase 3: Initial Configuration (10 min)

### First Login

After reboot, you'll see login prompt:

```
n1522-ai-hub login: admin
Password: (enter your password)
```

### Get IP Address

```bash
# Check network
ip addr show

# Should see something like:
# eth0: 192.168.20.xxx
```

**Note your IP!** You'll SSH to this from now on.

### Update System

```bash
# Update package lists
sudo apt update

# Upgrade all packages
sudo apt upgrade -y

# Install essentials
sudo apt install -y curl wget git vim htop tmux net-tools

# Reboot to apply kernel updates
sudo reboot
```

---

## Phase 4: SSH Access via Tailscale (15 min)

**Why Tailscale?**
- N1522 is on AI_LAB network (192.168.20.x)
- Can't SSH directly from LAN (192.168.1.x) - blocked by firewall!
- Tailscale creates secure tunnel over internet

### Install Tailscale on N1522

SSH back in (or use console):

```bash
# Install Tailscale
curl -fsSL https://tailscale.com/install.sh | sh

# Start Tailscale
sudo tailscale up

# Will show URL like:
# https://login.tailscale.com/a/XXXXXXXXXXXXX
```

**Copy the URL and open in browser on your laptop**

1. Click the URL
2. Login with your Tailscale account
3. Authorize the device
4. Give it a name: "n1522-ai-hub"

### Get Tailscale IP

Back on N1522:

```bash
# Show Tailscale IP
tailscale ip -4

# Example: 100.x.x.x
```

**Note this IP!** This is your permanent address for SSH.

### Test SSH from Laptop

From your laptop (via Tailscale):

```bash
# SSH via Tailscale IP
ssh admin@100.x.x.x

# Should connect! ✅
```

**From now on, use Tailscale IP for all SSH access!**

---

## Phase 5: Install OpenClaw (15 min)

### Install Node.js 24.x

```bash
# Add NodeSource repository
curl -fsSL https://deb.nodesource.com/setup_24.x | sudo -E bash -

# Install Node.js
sudo apt install -y nodejs

# Verify
node --version   # Should show v24.x.x
npm --version    # Should show 10.x.x
```

### Install OpenClaw

```bash
# Install OpenClaw globally
sudo npm install -g openclaw

# Verify installation
openclaw --version

# Should show: openclaw/x.x.x
```

### Initialize OpenClaw

```bash
# Create workspace directory
mkdir -p ~/openclaw-workspace
cd ~/openclaw-workspace

# Initialize OpenClaw
openclaw init

# Will create:
# ~/.openclaw/          (config + agents)
# ~/openclaw-workspace/ (working directory)
```

### Configure API Keys

```bash
# Add Anthropic API key
openclaw agents auth add anthropic
# Paste your Claude API key when prompted

# Add Moonshot (Kimi) API key
openclaw agents auth add moonshot
# Paste your Kimi API key when prompted

# Add OpenAI (optional)
openclaw agents auth add openai
# Paste your OpenAI API key when prompted
```

### Start OpenClaw Gateway

```bash
# Start gateway in foreground (for testing)
openclaw gateway start

# Should see:
# ✓ OpenClaw Gateway started
# ✓ Listening on port 3333
# ✓ Agent: main ready
```

**Test from browser:**
- Go to: `http://100.x.x.x:3333` (Tailscale IP)
- Should see OpenClaw web UI ✅

**Stop gateway:**
- Press `Ctrl+C`

### Set Up Systemd Service (Auto-start)

Create service file:

```bash
sudo nano /etc/systemd/system/openclaw-gateway.service
```

Paste this:

```ini
[Unit]
Description=OpenClaw Gateway
After=network.target

[Service]
Type=simple
User=admin
WorkingDirectory=/home/admin/openclaw-workspace
Environment="PATH=/usr/bin:/usr/local/bin"
ExecStart=/usr/bin/openclaw gateway start
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Save and exit (`Ctrl+X`, `Y`, `Enter`)

Enable and start:

```bash
# Reload systemd
sudo systemctl daemon-reload

# Enable auto-start on boot
sudo systemctl enable openclaw-gateway

# Start now
sudo systemctl start openclaw-gateway

# Check status
sudo systemctl status openclaw-gateway

# Should see: "Active: active (running)"
```

**OpenClaw is now running 24/7!** ✅

---

## Phase 6: Install Docker (Optional - 10 min)

If you want to run containerized services:

```bash
# Install Docker
curl -fsSL https://get.docker.com | sh

# Add your user to docker group
sudo usermod -aG docker $USER

# Apply group membership (logout/login or run):
newgrp docker

# Verify
docker --version
docker run hello-world

# Should download and run test container ✅
```

### Install Docker Compose

```bash
# Install Docker Compose
sudo apt install -y docker-compose

# Verify
docker-compose --version
```

---

## Phase 7: Testing & Verification (5 min)

### Network Tests

```bash
# Test internet
ping -c 4 8.8.8.8
# Should work ✅

# Test DNS
ping -c 4 google.com
# Should work ✅

# Test isolation (should FAIL - blocked by firewall)
ping -c 4 192.168.1.1
# Should timeout ✅ (this is correct!)

# Check speed
curl -s https://raw.githubusercontent.com/sivel/speedtest-cli/master/speedtest.py | python3 -
# Should see ~560 Mbps ✅
```

### OpenClaw Tests

From browser (via Tailscale):

1. **Open:** `http://100.x.x.x:3333`
2. **Should see:** OpenClaw web UI
3. **Try chat:** Ask Claude a question
4. **Should work:** Response from Claude Opus ✅

### System Resource Check

```bash
# Check RAM usage
free -h
# Should see: ~2-3GB used out of 16GB ✅

# Check disk space
df -h
# Should see: ~10-20GB used out of 1TB ✅

# Check CPU load
htop
# Press 'q' to exit
# Should see: low CPU usage ✅
```

---

## Phase 8: Firewall Configuration (Optional)

Ubuntu has firewall (UFW) disabled by default. Since N1522 is behind N1241 firewall, this is fine.

**If you want to enable local firewall:**

```bash
# Install UFW (should already be installed)
sudo apt install -y ufw

# Allow SSH
sudo ufw allow 22/tcp

# Allow OpenClaw
sudo ufw allow 3333/tcp

# Enable firewall
sudo ufw enable

# Check status
sudo ufw status verbose
```

---

## Final Configuration

### Set Timezone

```bash
# List timezones
timedatectl list-timezones | grep Sofia

# Set timezone
sudo timedatectl set-timezone Europe/Sofia

# Verify
timedatectl
```

### Enable Automatic Updates

```bash
# Install unattended-upgrades
sudo apt install -y unattended-upgrades

# Enable automatic security updates
sudo dpkg-reconfigure -plow unattended-upgrades
# Select: "Yes"
```

### Create Backup Script

```bash
# Create backup directory
mkdir -p ~/backups

# Create backup script
nano ~/backup-openclaw.sh
```

Paste:

```bash
#!/bin/bash
DATE=$(date +%Y-%m-%d-%H%M)
BACKUP_DIR=~/backups
mkdir -p $BACKUP_DIR

# Backup OpenClaw config
tar -czf $BACKUP_DIR/openclaw-$DATE.tar.gz ~/.openclaw

# Keep only last 7 backups
cd $BACKUP_DIR
ls -t openclaw-*.tar.gz | tail -n +8 | xargs -r rm

echo "Backup completed: openclaw-$DATE.tar.gz"
```

Make executable:

```bash
chmod +x ~/backup-openclaw.sh

# Test it
./backup-openclaw.sh
```

Add to cron (daily backup):

```bash
crontab -e

# Add this line (runs daily at 2 AM):
0 2 * * * /home/admin/backup-openclaw.sh
```

---

## Quick Reference

### System Access

**SSH (via Tailscale):**
```bash
ssh admin@100.x.x.x
```

**OpenClaw Web UI:**
```
http://100.x.x.x:3333
```

### Service Management

**OpenClaw Gateway:**
```bash
sudo systemctl status openclaw-gateway   # Check status
sudo systemctl restart openclaw-gateway  # Restart
sudo systemctl stop openclaw-gateway     # Stop
sudo systemctl start openclaw-gateway    # Start
sudo journalctl -u openclaw-gateway -f   # View logs
```

**Tailscale:**
```bash
tailscale status              # Check status
tailscale ip                  # Show Tailscale IP
sudo systemctl restart tailscaled  # Restart Tailscale
```

### Useful Commands

**System Status:**
```bash
htop                    # Resource monitor
df -h                   # Disk space
free -h                 # RAM usage
ip addr                 # Network interfaces
tailscale status        # Tailscale connections
```

**OpenClaw:**
```bash
openclaw status         # Gateway status
openclaw agents list    # List agents
openclaw --help         # Show help
```

**Updates:**
```bash
sudo apt update         # Update package lists
sudo apt upgrade        # Upgrade packages
sudo npm update -g openclaw  # Update OpenClaw
```

---

## Network Configuration

**N1522 Network Info:**
- **Local IP:** 192.168.20.x (DHCP from N1241)
- **Tailscale IP:** 100.x.x.x (permanent VPN address)
- **Gateway:** 192.168.20.1 (N1241)
- **DNS:** 192.168.20.1 (via N1241)
- **Internet:** 560 Mbps via N1241
- **Isolation:** Cannot reach 192.168.1.0/24 (LAN) ✅

**Access Methods:**
- ✅ SSH via Tailscale: `ssh admin@100.x.x.x`
- ✅ OpenClaw UI via Tailscale: `http://100.x.x.x:3333`
- ✅ Internet access: Full 560 Mbps
- ❌ Direct SSH from LAN: Blocked (use Tailscale)

---

## Troubleshooting

### Can't SSH to N1522

**Problem:** Connection refused or timeout

**Solutions:**
1. Check Tailscale is running:
   ```bash
   sudo systemctl status tailscaled
   ```
2. Get correct Tailscale IP:
   ```bash
   tailscale ip -4
   ```
3. Make sure Tailscale is running on your laptop too
4. Try from N1522 console, then set up Tailscale again

---

### OpenClaw Web UI Not Accessible

**Problem:** Can't reach http://100.x.x.x:3333

**Solutions:**
1. Check OpenClaw is running:
   ```bash
   sudo systemctl status openclaw-gateway
   ```
2. Check logs:
   ```bash
   sudo journalctl -u openclaw-gateway -f
   ```
3. Check firewall:
   ```bash
   sudo ufw status
   # If enabled, allow port 3333:
   sudo ufw allow 3333/tcp
   ```
4. Restart gateway:
   ```bash
   sudo systemctl restart openclaw-gateway
   ```

---

### No Internet Connection

**Problem:** Can't ping 8.8.8.8

**Solutions:**
1. Check network interface:
   ```bash
   ip addr show
   # Should have 192.168.20.x
   ```
2. Check gateway:
   ```bash
   ip route
   # Default via 192.168.20.1
   ```
3. Check physical connection to N1241 Port 3
4. Check N1241 firewall rules (should allow internet)

---

### Slow Performance

**Problem:** System feels slow

**Check:**
```bash
# CPU usage
htop

# RAM usage
free -h

# Disk I/O
iotop

# Network speed
curl -s https://raw.githubusercontent.com/sivel/speedtest-cli/master/speedtest.py | python3 -
```

**Common causes:**
- Too many services running (stop what you don't need)
- Low RAM (should have ~10GB+ free)
- Disk full (check with `df -h`)

---

## Security Best Practices

1. **Keep System Updated:**
   ```bash
   sudo apt update && sudo apt upgrade
   ```
   
2. **Strong Passwords:**
   - Use strong password for admin user
   - Don't reuse passwords

3. **SSH Key Authentication:**
   ```bash
   # Generate key on laptop
   ssh-keygen -t ed25519
   
   # Copy to N1522
   ssh-copy-id admin@100.x.x.x
   
   # Disable password auth (optional)
   sudo nano /etc/ssh/sshd_config
   # Set: PasswordAuthentication no
   sudo systemctl restart sshd
   ```

4. **API Keys:**
   - Store in OpenClaw auth (encrypted)
   - Never commit to git
   - Rotate periodically

5. **Backups:**
   - Run daily backups (see backup script)
   - Test restores periodically

---

## Maintenance Schedule

### Daily (Automated)
- Backup OpenClaw config (cron)
- Security updates (unattended-upgrades)

### Weekly
- Check system logs: `sudo journalctl -p err`
- Monitor disk space: `df -h`
- Review OpenClaw logs

### Monthly
- Update packages: `sudo apt update && sudo apt upgrade`
- Update OpenClaw: `sudo npm update -g openclaw`
- Review API usage/costs
- Test backups restore

---

## Next Steps

✅ **Installation complete!**

**Now you can:**
1. Access OpenClaw via Tailscale
2. Configure agents and workflows
3. Integrate with your tools
4. Add more API providers
5. Build automation

**Documentation:**
- OpenClaw docs: Check `~/.openclaw/docs/`
- This guide: Save for reference
- Community: Discord/forums

---

**Installation Time:** ~1 hour  
**Status:** Production-ready ✅  
**Performance:** Optimized for cloud AI workloads

---

*Document created: 2026-02-11*  
*Hardware: IWILL N1522 (16GB + 1TB)*  
*Software: Ubuntu 24.04 + OpenClaw*  
*Network: N1241 AI_LAB (protected)*
