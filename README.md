# IWILL N1522 Cloud AI Hub

**24/7 AI orchestration server — industrial-grade hardware for cloud AI workloads.**

Complete setup package for deploying the IWILL N1522 as a dedicated AI hub running OpenClaw, Claude API, and automated AI agents around the clock.

---

## 🎯 What This Is

The N1522 is a compact, fanless mini PC designed to run as a **dedicated AI agent server** — always on, low power, and optimized for cloud AI API orchestration.

**Use cases:**
- 24/7 AI assistant (Telegram, Discord, WhatsApp bots)
- Automated background tasks (monitoring, reporting, alerts)
- Cloud AI API orchestration (Claude, ChatGPT, Kimi, Gemini)
- Self-hosted AI infrastructure with OpenClaw
- Home automation AI hub

---

## 📦 Package Contents

```
iwill-n1522-ai-hub/
├── INSTALLATION-GUIDE.md     ← Ubuntu + OpenClaw setup (~1 hour)
├── INSTALLED-TOOLS.md        ← Pre-installed software reference
└── README.md                 ← This file
```

---

## 🖥️ Hardware Specs

### IWILL N1522
- **CPU:** Intel N150 (4C/4T, up to 3.6GHz)
- **RAM:** 16-32GB DDR5 (expandable to 96GB)
- **Storage:** 512GB–1TB M.2 NVMe SSD
- **Network:** 2× 2.5GbE Intel i226-V
- **Power:** <15W typical, fanless
- **OS:** Ubuntu 24.04 LTS (pre-installed)
- **Dimensions:** Compact, wall/rack mountable

---

## 🚀 Quick Start

### Prerequisites
- N1522 with Ubuntu 24.04 LTS
- Network connection (ethernet)
- SSH access or keyboard/monitor

### 1. Initial Setup
```bash
# SSH into the unit
ssh openclaw@<ip-address>

# Run the setup script
curl -fsSL https://raw.githubusercontent.com/iwill-tech/iwill-n1522-cloud-ai-hub/main/setup.sh | bash
```

### 2. Full Manual Setup
Follow **[INSTALLATION-GUIDE.md](INSTALLATION-GUIDE.md)** for complete step-by-step instructions including:
- Ubuntu hardening
- OpenClaw installation and configuration
- Telegram/Discord channel setup
- AI model configuration (Claude, Kimi, etc.)
- Systemd / tmux service management
- Tailscale VPN for remote access
- Nextcloud integration

---

## 🤖 OpenClaw AI Agent

The N1522 runs [OpenClaw](https://openclaw.ai) — a personal AI assistant platform with:
- Multi-channel support (Telegram, Discord, WhatsApp)
- File system access and tool execution
- Scheduled tasks and heartbeats
- Memory and context across sessions
- Multi-model support (Claude, GPT, Kimi, etc.)

### Default Configuration
- **Model:** Kimi K2.5 (cost-effective, 250k context)
- **Channels:** Telegram + Discord
- **Runtime:** tmux (stable 24/7 operation)
- **Auto-start:** On boot via startup script

---

## 🔧 What's Pre-Installed

See [INSTALLED-TOOLS.md](INSTALLED-TOOLS.md) for the full list. Key tools:

| Category | Tools |
|----------|-------|
| **AI** | OpenClaw, Node.js runtime |
| **VPN** | Tailscale |
| **Sync** | Nextcloud client |
| **Dev** | Git, Docker, Python 3 |
| **Monitoring** | htop, ncdu, netstat |

---

## 🌐 Remote Access

### Tailscale (Recommended)
```bash
# Install Tailscale
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up

# Access from anywhere via Tailscale IP
ssh openclaw@100.x.x.x
```

### Web UI (OpenClaw)
```bash
# Port forward for web access
ssh -N -L 18789:127.0.0.1:18789 openclaw@<ip>
# Then open: http://localhost:18789
```

---

## 📊 Recommended Network Setup

For best security, connect the N1522 to an isolated network segment:

```
Internet
    │
[Firewall/Router]
    │
    ├── [LAN] ──── Workstations, laptops
    │
    └── [AI-LAB] ── N1522 AI Hub (this device)
                     192.168.20.x
```

Works natively with IWILL N1241 AI Firewall (see [iwill-n1241-ai-firewall](https://github.com/iwill-tech/iwill-n1241-ai-firewall)).

---

## 🔒 Security Notes

- Change default SSH credentials on first boot
- Enable UFW firewall (`ufw enable`)
- Use Tailscale for remote access (avoid exposing SSH to internet)
- Store API keys in environment variables, not config files
- Enable 2FA on all AI service accounts

---

## 📚 Documentation

| Document | Purpose |
|----------|---------|
| [INSTALLATION-GUIDE.md](INSTALLATION-GUIDE.md) | Full setup from scratch |
| [INSTALLED-TOOLS.md](INSTALLED-TOOLS.md) | Pre-installed software reference |

---

## 🤝 Support

**Email:** support@iwilltech.co.uk  
**Discord:** [IWILL Community](https://discord.gg/PLACEHOLDER)

**Websites:**
- 🌍 [iwilltech.co.uk](https://www.iwilltech.co.uk) — UK & International
- 🌍 [iwillmena.com](https://www.iwillmena.com) — Middle East & Africa
- 🇵🇹 [iwill.pt](https://www.iwill.pt) — Portugal
- 🇵🇱 [iwill.pl](https://www.iwill.pl) — Poland
- 🇧🇬 [iwill.bg](https://www.iwill.bg) — Bulgaria

---

## 📝 License

- **Documentation:** CC BY 4.0
- **Scripts:** MIT License
- **OpenClaw:** See [openclaw.ai](https://openclaw.ai) for license terms

---

**IWILL N1522 Cloud AI Hub**  
*Your AI works while you sleep.* 🤖⚡
