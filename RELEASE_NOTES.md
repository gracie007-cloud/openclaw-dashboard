# OpenClaw Dashboard v1.0.0 - Release Notes

## 🎉 Initial Public Release

The OpenClaw Agent Dashboard is now ready for public use on GitHub!

**Repository:** https://github.com/tugcantopaloglu/openclaw-dashboard

---

## ✅ Changes for Public Release

### 1. **Configuration via Environment Variables**

All hardcoded paths have been replaced with environment variables:

| Variable | Default | Purpose |
|----------|---------|---------|
| `DASHBOARD_PORT` | `7000` | Server port |
| `WORKSPACE_DIR` | `$OPENCLAW_WORKSPACE` or `$(pwd)` | OpenClaw workspace path |
| `OPENCLAW_DIR` | `$HOME/.openclaw` | OpenClaw data directory |
| `OPENCLAW_AGENT` | `main` | Agent ID to monitor |

**Example:**
```bash
WORKSPACE_DIR=/my/workspace DASHBOARD_PORT=8080 node server.js
```

### 2. **Auto-Detection of Git Repositories**

The dashboard now automatically discovers git repos by scanning `$WORKSPACE_DIR/projects/` for directories containing `.git`:

```javascript
function getGitRepos() {
  const repos = [];
  const projDir = path.join(WORKSPACE_DIR, 'projects');
  // Scans for .git directories...
  return repos;
}
```

### 3. **Branding Updates**

- Removed "Clara" references throughout
- Updated to generic "Agent Dashboard" branding
- Added `/api/config` endpoint for dashboard metadata
- Browser notifications now say "Agent Dashboard"

### 4. **Comprehensive Documentation**

Created extensive README.md with:
- 20+ feature highlights
- Quick install guide
- Manual installation steps
- Environment variable documentation
- Systemd service template
- Complete API reference
- Keyboard shortcuts table
- Contributing guidelines

### 5. **Installation Script**

New `install.sh` script that:
- Checks for Node.js v18+
- Detects workspace automatically
- Creates systemd service
- Enables auto-start on boot
- Provides useful commands

**Usage:**
```bash
./install.sh
```

### 6. **Git Configuration**

Added `.gitignore` to exclude:
- `node_modules/`
- `data/` (health history, usage data)
- Private memory files
- Environment files
- Log files

### 7. **Updated Scraper Script**

`scrape-claude-usage.sh` now uses `$WORKSPACE_DIR` instead of hardcoded `/root/clawd`:

```bash
WORKSPACE_DIR="${WORKSPACE_DIR:-${OPENCLAW_WORKSPACE:-$(pwd)}}"
OUTPUT_FILE="${WORKSPACE_DIR}/data/claude-usage.json"
```

---

## 📦 What's Included

### Core Files

- `server.js` - Main dashboard server (Node.js)
- `index.html` - Frontend UI (pure HTML/CSS/JS)
- `README.md` - Comprehensive documentation
- `install.sh` - Automated installer
- `.gitignore` - Git exclusions

### Documentation

- `FEATURES.md` - Feature list
- `IMPLEMENTATION_COMPLETE.md` - Implementation notes
- `VERIFICATION.md` - Testing checklist
- `RELEASE_NOTES.md` - This file

---

## 🚀 Getting Started

### Quick Start

```bash
git clone https://github.com/tugcantopaloglu/openclaw-dashboard.git
cd openclaw-dashboard
export WORKSPACE_DIR=/path/to/your/workspace
node server.js
```

Visit http://localhost:7000

### Automated Install

```bash
git clone https://github.com/tugcantopaloglu/openclaw-dashboard.git
cd openclaw-dashboard
./install.sh
```

---

## 🔒 Security & Privacy

- **No API keys** are included in the repo
- **No private data** (memory files, session data) is committed
- `data/` directory is gitignored
- All sensitive paths are configurable via environment variables

---

## ✅ Verification

### Dashboard Status
```bash
systemctl status agent-dashboard
```

### API Test
```bash
curl http://localhost:7000/api/config
```

Expected response:
```json
{
  "name": "OpenClaw Dashboard",
  "version": "1.0.0"
}
```

### Environment Variables in Use
```bash
journalctl -u agent-dashboard | grep Environment
```

---

## 🎯 Next Steps

1. ⭐ Star the repo: https://github.com/tugcantopaloglu/openclaw-dashboard
2. 📖 Read the full README for API docs and features
3. 🐛 Report issues on GitHub
4. 🤝 Contribute improvements via PRs

---

## 📝 Commit

**Commit Hash:** 169bec7  
**Message:** Initial release: OpenClaw Agent Dashboard v1.0.0  
**Date:** 2026-02-10  
**Author:** Tuğcan Topaloğlu <topaloglutugcan@gmail.com>

---

## 🙏 Acknowledgments

Built with ✨ for the OpenClaw community.

**Repository:** https://github.com/tugcantopaloglu/openclaw-dashboard  
**License:** MIT  
**Author:** Tuğcan Topaloğlu
