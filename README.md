# GRITS: AI Agent Security Scanner & Auto-Fixer

<p align="center">
  <img src=".github/assets/grits-banner.svg" alt="GRITS Agent Scanner" width="100%">
</p>

[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)
[![Version](https://img.shields.io/badge/GRITS-v0.3.0-blue.svg)](https://github.com/X-Scale-AI/grits-agent-scanner)
[![Platform](https://img.shields.io/badge/platform-macOS%20%7C%20Ubuntu%20%7C%20CentOS%2FRHEL-lightgrey.svg)]()
[![By X Scale AI](https://img.shields.io/badge/by-X%20Scale%20AI-black.svg)](https://xscaleai.com)

AI agents should not have the keys to your kingdom without guardrails.

If you are running local or cloud-connected AI agents, they likely have unrestricted access to your host filesystem, network, and plaintext API keys. **GRITS** is an open-source scanner that finds and automatically patches critical security vulnerabilities across 5 distinct layers. Nothing is permanently changed without a full, automatic backup and a one-command rollback.

Currently supports: **OpenClaw** and **NVIDIA NemoClaw**.

---

## ⚡ Full Audit in One Command

```bash
git clone https://github.com/X-Scale-AI/grits-agent-scanner.git
cd grits-agent-scanner
chmod +x grits-*
./grits-audit
```

No dependencies. Python 3 stdlib only. Works on macOS, Ubuntu, and CentOS/RHEL.

`./grits-audit` runs the full workflow in three steps:

1. **Scans** your config across 5 security layers and scores your posture
2. **Offers** to apply auto-fixes -- shows every planned change, creates a backup, asks to confirm
3. **Re-scans** and shows your before/after score delta

```
  step 1/3  SCANNING YOUR AGENT CONFIG

  GRITS  v0.3.0  ·  X Scale AI
  Agent    OpenClaw
  Config   /Users/you/.openclaw/openclaw.json
  Scanned  2026-03-23 00:49

  NETWORK

    ✖  OC01  No firewall. A compromised agent can reach anything on the internet.
    ✖  OC18  Agent can reach all devices on your LAN. Data exfiltration possible.
    ✔  2 checks passed

  APPLICATION

    ✖  OC06  Full tool access enabled. Prompt injection can abuse every tool.
    ✖  OC09  No sandbox. Agent can read your entire filesystem including SSH keys.
    ✔  3 checks passed

  ──────────────────────────────────────────────────────

  ████████████████░░░░  81%  COMPLIANT
  15 of 20 checks passed

  ──────────────────────────────────────────────────────

  →  Fix 4 issues automatically
     ./grits-agent-secure

  Advanced Configurations  (7 items)
    OC18  Agent can reach all devices on your LAN. Data exfiltration possible.
    OC14  Heartbeat burning expensive cloud API credits for a simple ping.
    ...

  Scored with GRITS v0.3.0 by X Scale AI · https://xscaleai.com/consult

  step 3/3  VERIFYING -- RE-SCANNING

  ──────────────────────────────────────────────────────

  BEFORE    21%   ████░░░░░░░░░░░░░░░░   CRITICAL
  AFTER     47%   █████████░░░░░░░░░░░   PARTIAL

                  +26%  improvement

  ──────────────────────────────────────────────────────
```

Scan only, no fix prompt:

```bash
./grits-audit --scan-only
```

---

## 🛡️ What GRITS Checks (The 5 Layers)

20 checks. Every failure is explained in plain language with the exact risk it creates.

| Layer | What GRITS checks |
|---|---|
| **1 Network** | Host firewall (UFW, firewalld, Application Firewall), gateway authentication, bind address, LAN exposure |
| **2 Operator** | DM policy, group chat policy, CLI availability, multi-agent isolation on shared hosts |
| **3 Application** | Tool profile, dangerous tool deny list, exec policy, sandbox mode, plugin allowlist |
| **4 OS / Secrets** | Plaintext API keys in config, `.env` file permissions, credentials directory permissions, audit log pipeline |
| **5 Financial** | Heartbeat model routing (cloud vs local LLM), API spending limits, dangerous bypass flags |

Also supports **NVIDIA NemoClaw**:

```bash
./grits-audit --agent nemoclaw
```

Checks binary scoping (NemoClaw#272), Landlock LSM, seccomp, TLS enforcement, and inference cost controls.

---

## 🛠️ The Toolkit

Three tools for different workflows:

| Tool | What it does |
|---|---|
| `./grits-audit` | Full interactive workflow: scan → fix → verify with score delta. Start here. |
| `./grits-agent-scanner` | Read-only scan and report. Nothing modified. Supports `--json` for CI/CD. |
| `./grits-agent-secure` | Remediation engine. Preview changes, apply with backup, or roll back. |

```bash
# Full audit (recommended)
./grits-audit
./grits-audit --agent nemoclaw
./grits-audit --yes              # non-interactive, for CI/CD
./grits-audit --scan-only        # scan only, no fix prompt

# Scan only
./grits-agent-scanner
./grits-agent-scanner --report   # Markdown output for PRs, wikis
./grits-agent-scanner --json     # JSON output for dashboards, automation
./grits-agent-scanner --config /path/to/openclaw.json

# Fix
./grits-agent-secure             # preview every change (nothing applied)
./grits-agent-secure --apply     # apply with backup + confirmation
./grits-agent-secure --rollback  # restore most recent backup
./grits-agent-secure --rollback 20260323_005031  # restore specific backup
./grits-agent-secure --list-backups
```

---

## 🔧 What Gets Fixed Automatically

The fixer is additive-only. It never replaces your config or deletes existing rules.

| Fix | What changes | What is never touched |
|---|---|---|
| Enable firewall | UFW (Ubuntu/Debian), firewalld (CentOS/RHEL), Application Firewall (macOS) | Existing rules |
| Block dangerous tools | Appends to deny list | Tools you currently allow |
| Enable sandbox mode | Sets `agent.sandbox: true` | All other agent settings |
| Fix `.env` permissions | Locks to 600, root-owned, SELinux context restored on RHEL | File contents |
| Fix credentials dir | Locks to 700, root-owned, SELinux context restored on RHEL | Credential contents |
| Disable bypass flags | Sets `yolo` / `trustAll` / `autoApprove` to false | Keys stay in config |

**Before applying anything, the fixer:**

1. Shows you exactly what will change and what will not
2. Creates a timestamped backup of your config, `.env`, and credentials directory
3. Prints the rollback command
4. Asks for confirmation

**Rollback:**

```bash
# Restore the most recent backup
./grits-agent-secure --rollback

# Restore a specific backup by timestamp
./grits-agent-secure --rollback 20260323_005031

# List all available backups
./grits-agent-secure --list-backups
```

Backups live at `~/.openclaw/grits-backups/`. The rollback itself backs up current state first -- you can always undo the undo.

---

## 🔒 Advanced Configurations

Some findings require decisions specific to your infrastructure and cannot be automated.

| Issue | Why it requires manual action |
|---|---|
| Network segmentation | VLAN/subnet design specific to your environment |
| Gateway auth and allowlists | You supply the token and approved user IDs |
| API key migration | You choose the secrets backend (Vault, AWS Secrets Manager, etc.) |
| Audit log forwarding | Depends on your SIEM (Datadog, Splunk, Elastic) |
| Spending limits | Set at your API provider dashboard |
| Multi-agent isolation | Container or VM architecture decision |

Hardening guides:

| Agent | Guide |
|---|---|
| OpenClaw | [`apply/openclaw/hardening-baseline.md`](apply/openclaw/hardening-baseline.md) |
| NVIDIA NemoClaw | [`apply/nemoclaw/README.md`](apply/nemoclaw/README.md) |

Quick checklists (copy, fill in, share with your team):

| Layer | Checklist |
|---|---|
| 01 Network | [`checklists/01-network-safety.md`](checklists/01-network-safety.md) |
| 02 Operator | [`checklists/02-operator-identity.md`](checklists/02-operator-identity.md) |
| 03 Application | [`checklists/03-tool-permissions.md`](checklists/03-tool-permissions.md) |
| 04 OS/Secrets | [`checklists/04-secrets-exposure.md`](checklists/04-secrets-exposure.md) |
| 05 Financial | [`checklists/05-cost-exposure.md`](checklists/05-cost-exposure.md) |

---

## 🏢 Enterprise & Advanced Hardening

GRITS handles the baseline automatically. Production AI deployments often require network segmentation, SIEM integration, zero-trust architecture, and compliance mapping (SOC 2, FedRAMP, ISO 27001).

If your output flags **Advanced Configurations**, or if you are deploying agents in a regulated environment, X Scale AI can help.

**[Book a free architecture review with an X Scale AI security engineer](https://xscaleai.com/consult)**

---

## Reference Configs

Hardened configs ready to diff and apply. See [`score/configs/install-guide.md`](score/configs/install-guide.md) for environment variable setup.

| Config | What it is |
|---|---|
| `score/configs/openclaw.json.default` | Vanilla out-of-the-box OpenClaw config |
| `score/configs/openclaw.json.hardened.linux` | Hardened Linux config with env var placeholders |
| `score/configs/openclaw.json.hardened.mac` | Hardened macOS config with env var placeholders |

---

## Built on the GRITS Framework

This scanner implements the [GRITS Framework](https://github.com/X-Scale-AI/GRITS) -- the governance spec, control catalog, and compliance mappings that define the 5-Layer Zero-Trust model for AI agents.

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Issues and PRs welcome.
Vulnerabilities: [SECURITY.md](SECURITY.md).

---

## License

Apache 2.0. Use, modify, and distribute freely.

Any use of GRITS scoring output must retain:

> Scored with GRITS by X Scale AI

See [NOTICE](NOTICE) for full requirements.

---

<p align="center">
  <strong>GRITS v0.3.0</strong><br>
  Built by <a href="https://xscaleai.com">X Scale AI</a> &nbsp;|&nbsp;
  Apache 2.0 &nbsp;|&nbsp;
  <a href="https://xscaleai.com/consult">Free architecture review</a>
</p>
