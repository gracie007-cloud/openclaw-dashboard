# OpenClaw Agent Dashboard

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Node.js](https://img.shields.io/badge/Node.js-v18+-green.svg)](https://nodejs.org/)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-Compatible-purple.svg)](https://openclaw.dev)

A beautiful, real-time monitoring dashboard for OpenClaw agents. Track sessions, monitor API usage, view costs, manage memory files, and keep tabs on system health — all in one place.

![Dashboard Preview](docs/screenshot.png)

## ✨ Features

- 🤖 **Session Management** - View all agent sessions with real-time activity status
- 📊 **Rate Limit Monitoring** - Track Claude API usage against 5-hour rolling windows
- 💰 **Cost Analysis** - Detailed spending breakdowns by model, session, and time period
- ⚡ **Live Feed** - Real-time stream of agent messages across all sessions
- 🧠 **Memory Viewer** - Browse and read agent memory files (MEMORY.md, HEARTBEAT.md, daily notes)
- 📈 **System Health** - CPU, RAM, disk, temperature monitoring with sparklines
- 🔄 **Service Control** - Quick actions to restart OpenClaw, dashboard, or other services
- 📋 **Log Viewer** - Real-time system logs with auto-refresh
- ⏰ **Cron Management** - View, enable/disable, and manually trigger cron jobs
- 🌐 **Tailscale Integration** - View Tailscale status, IP, and connected peers
- 🎯 **Activity Heatmap** - Visualize peak usage hours over the last 30 days
- 🔥 **Streak Tracking** - Monitor daily activity streaks
- 🔍 **Session Search & Filtering** - Filter by status, model, date range with live search
- 🎨 **Dark Theme** - Beautiful glassmorphic UI with smooth animations
- ⌨️ **Keyboard Shortcuts** - Navigate quickly with hotkeys (1-5, Space, /, Esc, ?)
- 📱 **Mobile Responsive** - Works on phones and tablets
- 🔔 **Browser Notifications** - Get alerted when usage limits are approaching
- 📊 **Timeline View** - Visual timeline of session activity
- 💾 **Git Activity** - Track recent commits across your repos
- 🎛️ **Claude Usage Scraper** - Fetch real usage data from Claude Code CLI
- 🔄 **Auto-Refresh** - Live data updates every 5 seconds
- 🌟 **Lifetime Stats** - Total tokens, messages, cost since first session
- 📈 **Health History** - 24-hour CPU & RAM sparklines
- 🎯 **Quick Actions** - One-click system maintenance (updates, cleanup, restarts)
- 🔐 **No External Dependencies** - Pure Node.js, no database required

## 🚀 Quick Install

```bash
git clone https://github.com/tugcantopaloglu/openclaw-dashboard.git
cd openclaw-dashboard

# Auto-detect workspace or set explicitly
export WORKSPACE_DIR=/path/to/your/openclaw/workspace

# Start the dashboard
node server.js
```

Visit `http://localhost:7000` in your browser.

## 📦 Manual Install

### Prerequisites

- **Node.js** v18+ (check with `node --version`)
- **OpenClaw** installed and running
- **Systemd** (optional, for service installation)

### Installation Steps

1. **Clone the repository**
   ```bash
   git clone https://github.com/tugcantopaloglu/openclaw-dashboard.git
   cd openclaw-dashboard
   ```

2. **Configure environment** (optional)
   ```bash
   export DASHBOARD_PORT=7000
   export WORKSPACE_DIR=/path/to/your/workspace
   export OPENCLAW_DIR=$HOME/.openclaw
   export OPENCLAW_AGENT=main
   ```

3. **Start the server**
   ```bash
   node server.js
   ```

4. **Access the dashboard**
   Open `http://localhost:7000` or `http://your-server-ip:7000`

## ⚙️ Configuration

All configuration is done via environment variables:

| Variable | Default | Description |
|----------|---------|-------------|
| `DASHBOARD_PORT` | `7000` | Port the dashboard listens on |
| `WORKSPACE_DIR` | `$OPENCLAW_WORKSPACE` or `$(pwd)` | Path to your OpenClaw workspace |
| `OPENCLAW_DIR` | `$HOME/.openclaw` | OpenClaw data directory |
| `OPENCLAW_AGENT` | `main` | Agent ID to monitor |

### Example: Custom Port

```bash
DASHBOARD_PORT=8080 node server.js
```

### Example: Different Workspace

```bash
WORKSPACE_DIR=/mnt/data/my-openclaw node server.js
```

## 🔗 OpenClaw Integration

The dashboard automatically detects:
- **Sessions** from `$OPENCLAW_DIR/agents/$AGENT_ID/sessions/`
- **Cron jobs** from `$OPENCLAW_DIR/cron/jobs.json`
- **Memory files** from `$WORKSPACE_DIR/MEMORY.md`, `HEARTBEAT.md`, and `memory/*.md`
- **Git repos** from `$WORKSPACE_DIR/projects/*/`
- **Health data** saved to `$WORKSPACE_DIR/data/health-history.json`

### Required Files

The dashboard works best when these files exist:
- `$WORKSPACE_DIR/MEMORY.md` - Agent long-term memory
- `$WORKSPACE_DIR/HEARTBEAT.md` - Heartbeat task list
- `$WORKSPACE_DIR/memory/YYYY-MM-DD.md` - Daily memory notes
- `$WORKSPACE_DIR/scripts/scrape-claude-usage.sh` - Claude usage scraper
- `$WORKSPACE_DIR/scripts/parse-claude-usage.py` - Usage parser

## 🛠️ Systemd Service

Run the dashboard as a system service for auto-start and crash recovery.

### Create Service File

```bash
sudo nano /etc/systemd/system/agent-dashboard.service
```

Paste this content:

```ini
[Unit]
Description=OpenClaw Agent Dashboard
After=network.target openclaw.service

[Service]
Type=simple
User=your-username
WorkingDirectory=/path/to/openclaw-dashboard
ExecStart=/usr/bin/node /path/to/openclaw-dashboard/server.js
Environment=DASHBOARD_PORT=7000
Environment=WORKSPACE_DIR=/path/to/your/openclaw/workspace
Environment=OPENCLAW_DIR=/home/your-username/.openclaw
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

### Enable and Start

```bash
sudo systemctl daemon-reload
sudo systemctl enable agent-dashboard
sudo systemctl start agent-dashboard
sudo systemctl status agent-dashboard
```

### View Logs

```bash
journalctl -u agent-dashboard -f
```

## 📊 Usage Scraping

The dashboard can scrape real usage data from the Claude Code CLI.

### How It Works

1. Opens a persistent tmux session named `claude-persistent`
2. Runs `claude` CLI and sends `/usage` command
3. Captures the output and parses usage percentages
4. Saves to `$WORKSPACE_DIR/data/claude-usage.json`

### Manual Scrape

```bash
cd $WORKSPACE_DIR
bash scripts/scrape-claude-usage.sh
```

### Auto-Refresh

Enable the "Auto" toggle in the Overview page to scrape every 2 minutes.

### Requirements

- `tmux` installed
- `claude` CLI authenticated
- `scripts/scrape-claude-usage.sh` executable
- `scripts/parse-claude-usage.py` available

## 🔌 API Reference

The dashboard exposes a REST API for programmatic access.

### Core Endpoints

#### `GET /api/config`
Returns dashboard configuration.

**Response:**
```json
{
  "name": "OpenClaw Dashboard",
  "version": "1.0.0"
}
```

#### `GET /api/sessions`
List all agent sessions with metadata.

**Response:**
```json
[
  {
    "key": "agent:main:main",
    "label": "main",
    "model": "anthropic/claude-opus-4-6",
    "totalTokens": 125000,
    "cost": 1.87,
    "kind": "direct",
    "updatedAt": 1707561234567,
    "createdAt": 1707550000000,
    "aborted": false,
    "lastMessage": "Updated server.js successfully"
  }
]
```

#### `GET /api/usage`
Get 5-hour rolling window usage data.

**Response:**
```json
{
  "fiveHour": {
    "perModel": {
      "anthropic/claude-opus-4-6": {
        "input": 50000,
        "output": 12000,
        "cost": 0.245,
        "calls": 15
      }
    },
    "windowStart": 1707561234567,
    "windowResetIn": 14400000
  },
  "burnRate": {
    "tokensPerMinute": 250.5,
    "costPerMinute": 0.0125
  },
  "predictions": {
    "timeToLimit": 7200000,
    "safe": true
  }
}
```

#### `GET /api/costs`
Get spending data by day, model, and session.

**Response:**
```json
{
  "total": 45.67,
  "today": 2.34,
  "week": 12.89,
  "perDay": {
    "2024-02-10": 2.34,
    "2024-02-09": 3.45
  },
  "perModel": {
    "anthropic/claude-opus-4-6": 35.12,
    "anthropic/claude-sonnet-4-5": 10.55
  }
}
```

#### `GET /api/system`
System health metrics.

**Response:**
```json
{
  "cpu": { "usage": 35, "temp": 52.0 },
  "memory": { "total": 8589934592, "used": 4294967296, "percent": 50 },
  "disk": { "percent": 45, "used": "180G", "total": "400G" },
  "uptime": 864000,
  "crashCount": 0
}
```

### Session Management

#### `GET /api/session-messages?id=<sessionId>`
Get recent messages from a session (last 30).

#### `GET /api/crons`
List all cron jobs.

#### `POST /api/cron/<id>/toggle`
Enable/disable a cron job.

#### `POST /api/cron/<id>/run`
Manually trigger a cron job.

### Memory & Logs

#### `GET /api/memory-files`
List all memory files with metadata.

#### `GET /api/memory-file?path=<path>`
Read content of a memory file.

#### `GET /api/logs?service=<service>&lines=<N>`
Fetch system logs.  
**Params:** `service` (openclaw|agent-dashboard|tailscaled), `lines` (default 100)

### Quick Actions

#### `POST /api/action/restart-openclaw`
Restart OpenClaw service.

#### `POST /api/action/restart-dashboard`
Restart dashboard service.

#### `POST /api/action/clear-cache`
Clear internal caches.

#### `POST /api/action/update-openclaw`
Run `npm update -g openclaw`.

#### `POST /api/action/disk-cleanup`
Run `apt autoremove` and `journalctl --vacuum-time=7d`.

### Claude Usage

#### `POST /api/claude-usage-scrape`
Trigger usage scrape script.

#### `GET /api/claude-usage`
Get last scraped usage data.

### Live Feed

#### `GET /api/live`
Server-Sent Events stream of real-time messages.

## ⌨️ Keyboard Shortcuts

| Key | Action |
|-----|--------|
| `1` | Switch to Overview |
| `2` | Switch to Sessions |
| `3` | Switch to Costs |
| `4` | Switch to Rate Limits |
| `5` | Switch to Live Feed |
| `Space` | Pause/Resume Feed (when on Live Feed page) |
| `/` | Focus search box |
| `Esc` | Close modals and overlays |
| `?` | Show keyboard shortcuts help |

## 📱 Screenshots

### Overview Page
![Overview](docs/overview.png)

### Sessions Table
![Sessions](docs/sessions.png)

### Rate Limits
![Limits](docs/limits.png)

### Live Feed
![Feed](docs/feed.png)

## 🤝 Contributing

Contributions are welcome! Please follow these guidelines:

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/amazing-feature`)
3. **Commit** your changes (`git commit -m 'Add amazing feature'`)
4. **Push** to the branch (`git push origin feature/amazing-feature`)
5. **Open** a Pull Request

### Development Setup

```bash
git clone https://github.com/tugcantopaloglu/openclaw-dashboard.git
cd openclaw-dashboard
export WORKSPACE_DIR=/path/to/test/workspace
node server.js
```

The dashboard has no build step — edit `server.js` or `index.html` and reload.

### Code Style

- **No comments** in code (self-documenting)
- **Brief and direct** function names
- **Tuğcan's style:** clean, minimal, functional

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

Copyright © 2025 Tuğcan Topaloğlu

## 🙏 Acknowledgments

- Built for [OpenClaw](https://openclaw.dev) by Anthropic
- Inspired by modern dashboards (Grafana, Vercel, Railway)
- Font: [Inter](https://rsms.me/inter/) & [JetBrains Mono](https://www.jetbrains.com/lp/mono/)

## 📞 Support

- **Issues:** [GitHub Issues](https://github.com/tugcantopaloglu/openclaw-dashboard/issues)
- **Email:** topaloglutugcan@gmail.com
- **Twitter:** [@tugcantopaloglu](https://twitter.com/tugcantopaloglu)

---

Made with ✨ by [Tuğcan Topaloğlu](https://github.com/tugcantopaloglu)
