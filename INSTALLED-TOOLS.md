# IWILL N1522 — Installed Software & Tools

**System:** Intel N150 (4 cores @ 3.6GHz), 16–32GB RAM, Ubuntu 24.04 LTS

---

## 🤖 AI & Automation
- **OpenClaw Gateway** — 24/7 AI orchestration platform
  - Default Model: Kimi K2.5 (Moonshot — 250k context, cost-effective)
  - Secondary: Claude Sonnet, Claude Opus
  - Channels: Discord, Telegram
  - Service: tmux or systemd auto-start

## 📊 Performance Monitoring
- **htop** — Interactive process viewer
- **btop** — Modern resource monitor (TUI)
- **glances** — Cross-platform monitoring tool
- **neofetch** — System info display
- **lm-sensors** — Hardware temperature monitoring

## 🔥 Benchmarking & Testing
- **stress-ng** — System stress testing tool
- **sysbench** — Database and system performance benchmark
- **iotop** — I/O monitoring per process
- **nethogs** — Network bandwidth per process

## 🛠️ Development Tools
- **GCC/G++ 13** — GNU Compiler Collection
- **make** — Build automation
- **git** — Version control
- **Python 3.12** + pip
- **Node.js v24+** — JavaScript runtime
- **build-essential** — Essential development packages

## 🧰 System Utilities
- **tmux** — Terminal multiplexer
- **jq** — JSON processor
- **tree** — Directory tree viewer
- **curl, wget** — Download managers
- **ImageMagick** — Image processing
- **w3m** — Text-based web browser

## 🎨 Graphics & Visualization
- **chafa** — Image-to-text converter
- **libsixel** — Sixel graphics library

---

## 🚀 Quick Start Commands

### Monitor System Performance
```bash
btop                    # Beautiful resource monitor
glances                 # Comprehensive system monitor
htop                    # Classic process viewer
sensors                 # Check CPU temperature
neofetch                # System info display
```

### Benchmark Performance
```bash
sysbench cpu run                           # CPU benchmark
stress-ng --cpu 4 --timeout 60s --metrics  # CPU stress test
sysbench memory run                        # Memory benchmark
```

### Monitor I/O & Network
```bash
iotop                   # Disk I/O per process
nethogs                 # Network bandwidth per process
```

### OpenClaw Gateway
```bash
openclaw status                            # Check gateway status
systemctl --user status openclaw-gateway   # Service status
journalctl --user -u openclaw-gateway -f   # Live logs
```

---

## 🌐 Network Access

### Tailscale VPN (Recommended)
```bash
# Install Tailscale for secure remote access
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
# SSH via Tailscale IP after pairing
ssh openclaw@<tailscale-ip>
```

### Web UI Access (from remote)
```bash
# Port-forward OpenClaw web UI
ssh -N -L 18789:127.0.0.1:18789 openclaw@<device-ip>
# Then open: http://localhost:18789
```

---

## ✅ Feature Highlights

- **24/7 AI Assistant** — Always online, never sleeps
- **Multi-Model Support** — Kimi 2.5, Claude Sonnet, Claude Opus
- **Multi-Channel** — Discord, Telegram integration
- **Performance Monitoring** — Enterprise-grade system visibility
- **Benchmarking Suite** — Prove performance to customers
- **Development Ready** — Full GCC, Python, Node.js stack
- **Remote Management** — SSH + Tailscale VPN access
- **Auto-Recovery** — Systemd/tmux service with auto-restart

---

**IWILL N1522 Cloud AI Hub**  
https://www.iwilltech.co.uk/n1522
