# OpenClaw Config Install Guide

How to apply a hardened config and inject environment variables safely.
Reference configs are in this directory -- diff them against your current
config before applying.

---

## Step 1: Diff before applying

```bash
diff ~/.openclaw/openclaw.json score/configs/openclaw.json.hardened.mac
```

Understand every change. Apply only what makes sense for your setup.

---

## Step 2: Set environment variables

Never put API keys directly in `openclaw.json`. Use environment variables.

### macOS (Terminal / Foreground)

Add to `~/.zshrc`:

```bash
export OPENROUTER_API_KEY="your_openrouter_key"
export BRAVE_API_KEY="your_brave_key"
export OPENCLAW_GATEWAY_TOKEN="your_gateway_token"
export TELEGRAM_BOT_TOKEN="your_telegram_bot_token"
```

Apply immediately:

```bash
source ~/.zshrc
```

### Linux (systemd service)

Create a secure environment file:

```bash
sudo nano /etc/openclaw.env
```

Add your keys (no `export`, no quotes needed):

```
OPENROUTER_API_KEY=your_openrouter_key
BRAVE_API_KEY=your_brave_key
OPENCLAW_GATEWAY_TOKEN=your_gateway_token
TELEGRAM_BOT_TOKEN=your_telegram_bot_token
```

Lock it down:

```bash
sudo chmod 600 /etc/openclaw.env
sudo chown root:root /etc/openclaw.env
```

Reference it in your systemd service file (`/etc/systemd/system/openclaw.service`):

```ini
[Service]
EnvironmentFile=/etc/openclaw.env
```

Reload:

```bash
sudo systemctl daemon-reload && sudo systemctl restart openclaw
```

---

## Step 3: Apply the hardened config

```bash
# Backup first
cp ~/.openclaw/openclaw.json ~/.openclaw/openclaw.json.backup-$(date +%Y%m%d)

# Apply
cp score/configs/openclaw.json.hardened.mac ~/.openclaw/openclaw.json
# or for Linux:
cp score/configs/openclaw.json.hardened.linux ~/.openclaw/openclaw.json
```

---

## Step 4: Verify

```bash
./grits-agent-scanner
```

Your score should improve. Remaining findings are the items that need
manual configuration specific to your infrastructure.

---

## Config reference

| File | What it is |
|---|---|
| `openclaw.json.default` | Vanilla OpenClaw config (what ships out of the box) |
| `openclaw.json.hardened.linux` | Hardened config for Linux (env var placeholders) |
| `openclaw.json.hardened.mac` | Hardened config for macOS (env var placeholders) |

---

*Scored with GRITS v0.3.0 by X Scale AI*
*https://github.com/X-Scale-AI/grits-agent-scanner*
