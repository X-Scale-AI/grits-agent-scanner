# OpenClaw 5-Layer Zero-Trust Hardening Baseline

Manual hardening guide for issues the auto-fixer cannot handle.
Run `./grits-agent-secure --apply` first to cover auto-fixable items.

---

## Layer 1: Network

### OC02 -- Gateway Authentication

Set a strong random token in `openclaw.json`:

```json
"gateway": {
  "auth": {
    "mode": "token",
    "token": "${OPENCLAW_GATEWAY_TOKEN}"
  }
}
```

Generate a token:
```bash
openssl rand -hex 24
```

Store it in your environment (see `score/configs/install-guide.md`).

### OC03 -- Gateway Bind Address

```json
"gateway": {
  "bind": "loopback"
}
```

Never bind to `0.0.0.0` unless you have a Tailscale or VPN in place and
understand the exposure.

### OC18 -- LAN Network Segmentation

This requires network architecture changes specific to your environment.
Options range from router-level VLAN configuration to dedicated hardware.
Complexity depends on your setup.

**[Schedule a call to design your network segmentation](https://xscaleai.com/consult)**

---

## Layer 2: Operator

### OC04 -- DM Policy

```json
"channels": {
  "telegram": {
    "dmPolicy": "allowlist",
    "allowFrom": ["YOUR_TELEGRAM_USER_ID"]
  }
}
```

Find your Telegram user ID: message `@userinfobot` on Telegram.

### OC05 -- Group Policy

```json
"channels": {
  "telegram": {
    "groupPolicy": "allowlist"
  }
}
```

### OC19 -- Multi-Agent Isolation

If you run multiple agents, each should have:
- Its own credentials directory
- Its own API keys
- Its own network interface or container

**[Multi-agent trust boundary design](https://xscaleai.com/consult)**

---

## Layer 3: Application

### OC10 -- Plugin Allowlist

```json
"plugins": {
  "allow": ["openclaw-freerouter", "openclaw-tavily"]
}
```

Only list plugins you have reviewed and explicitly need.

---

## Layer 4: OS/Secrets

### OC11 -- Plaintext API Keys

Move all keys out of `openclaw.json` into environment variables.
See `score/configs/install-guide.md` for the full setup.

### OC20 -- Audit Log Pipeline

Forward OpenClaw logs to a centralized system:

**Linux (rsyslog):** Add a rule to forward logs matching `openclaw` to your SIEM.

**Docker:** Mount log volumes and use a log driver (fluentd, loki, splunk).

**Cloud:** Use Datadog, Elastic, or Vector to ship logs from the host.

**[SIEM integration design](https://xscaleai.com/consult)**

---

## Layer 5: Financial

### OC14 -- Heartbeat Model Routing

Configure heartbeat to use a local model to avoid cloud API costs:

```json
"agents": {
  "defaults": {
    "heartbeat": {
      "every": "30m",
      "model": "custom-127-0-0-1-11434/qwen3:8b"
    }
  }
}
```

This requires Ollama running locally. See `score/configs/openclaw.json.hardened.mac`
for a working example.

### OC15 -- Spending Limits

Set hard limits in your provider dashboard:
- **OpenRouter:** openrouter.ai > Settings > Limits
- **Anthropic:** console.anthropic.com > Settings > Usage limits
- **OpenAI:** platform.openai.com > Settings > Usage limits

Disable auto-topup where available.

---

*GRITS v0.3.0 by X Scale AI -- https://xscaleai.com/consult*
