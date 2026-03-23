# GRITS Agent Scanner

<p align="center">
  <img src=".github/assets/grits-banner.svg" alt="GRITS Agent Scanner" width="100%">
</p>

[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)
[![GRITS Version](https://img.shields.io/badge/GRITS-v0.3.0-green.svg)](https://github.com/X-Scale-AI/grits-agent-scanner)
[![Platform](https://img.shields.io/badge/platform-macOS%20%7C%20Linux-lightgrey.svg)]()
[![X Scale AI](https://img.shields.io/badge/by-X%20Scale%20AI-black.svg)](https://xscaleai.com)

---

**Your AI agent has full access to your filesystem, API keys, and local network. Most configs ship insecure by default.**

GRITS scans your actual config, scores your posture across 5 security layers, fixes what it can automatically, and tells you exactly what still needs attention. Nothing is modified until you say so. Every change is backed up with a one-command rollback.

---

## Full Audit in One Command

```bash
git clone https://github.com/X-Scale-AI/grits-agent-scanner.git
cd grits-agent-scanner
./grits-audit
```

No dependencies. Python 3 stdlib only. Works on macOS, Ubuntu, and CentOS/RHEL.

`grits-audit` runs the full workflow automatically:

1. **Scans** your config across 5 security layers
2. **Offers** to apply auto-fixes -- shows every change first, backs up, asks to confirm
3. **Re-scans** and shows your before/after score delta:

```
  STEP 3/3  --  VERIFYING -- RE-SCANNING
  ...
  SCORE DELTA   21%  ->  47%   (+26%)
```

### Just the scan

```bash
./grits-agent-scanner
```

```
====================================================================
  GRITS Agent Scanner  v0.3.0  by X Scale AI
====================================================================

  Platform  MACOS  |  Agent  OpenClaw
  Config    /Users/you/.openclaw/openclaw.json

  L1 -- NETWORK
  [FAIL]  OC01  No firewall. A compromised agent can reach anything on the internet.
  [PASS]  OC02  Gateway requires authentication to connect.
  [PASS]  OC03  Gateway locked to this machine only. Other devices cannot reach it.
  [FAIL]  OC18  Agent can reach all devices on your LAN. Data exfiltration possible.

  L3 -- APPLICATION
  [FAIL]  OC06  Full tool access enabled. Prompt injection can abuse every tool.
  [PASS]  OC07  Dangerous tools are on the deny list.
  [PASS]  OC09  Sandbox mode active. Agent workspace is isolated.

  L5 -- FINANCIAL
  [FAIL]  OC14  Heartbeat burning expensive cloud API credits for a simple ping.
  [PASS]  OC16  No dangerous runtime flags detected.

--------------------------------------------------------------------
  SCORE   11/20   ███████████░░░░░░░░░   55%   BASELINE

  AUTO-FIXABLE NOW (4 issues)
  Run:  ./grits-agent-secure

  NEEDS AN EXPERT (5 issues)
  Schedule a free 30-min call:  https://xscaleai.com/consult
====================================================================
```

---

## Fix It

```bash
# Full workflow (recommended):
./grits-audit

# Or run the fixer directly:

# Preview every change before anything is touched:
./grits-agent-secure

# Apply -- backs up first, asks for confirmation:
./grits-agent-secure --apply

# Changed your mind? Roll back instantly:
./grits-agent-secure --rollback

# See all available backups:
./grits-agent-secure --list-backups
```

**Before touching anything, the fixer:**
1. Shows you exactly what will change and what will not
2. Creates a full timestamped backup of your config, `.env`, and credentials directory
3. Displays the rollback command
4. Asks for confirmation

**What gets fixed automatically:**

| Fix | What changes | What is never touched |
|---|---|---|
| Enable firewall | UFW (Ubuntu/Debian), firewalld (CentOS/RHEL), Application Firewall (macOS) | Existing rules |
| Block dangerous tools | Appends to deny list | Tools you already allow |
| Enable sandbox mode | Sets `agent.sandbox: true` | All other agent settings |
| Fix .env permissions | Locks to 600, root-owned | File contents |
| Fix credentials dir | Locks to 700, root-owned | Credential contents |
| Disable bypass flags | Sets yolo/trustAll/autoApprove to false | Keys stay in config |

**Rollback:**

```bash
# Restore the most recent backup:
./grits-agent-secure --rollback

# Restore a specific backup by timestamp:
./grits-agent-secure --rollback 20260322_143022

# List all backups:
./grits-agent-secure --list-backups
```

Backups live at `~/.openclaw/grits-backups/`. The rollback itself backs up your current state first, so you can always undo the undo.

---

## What Gets Checked

20 checks across 5 layers. Every result -- pass or fail -- is explained in plain language.

| Layer | What GRITS checks |
|---|---|
| **1 Network** | Host firewall, gateway auth, gateway bind address, LAN exposure |
| **2 Operator** | DM policy, group policy, CLI availability, multi-agent isolation |
| **3 Application** | Tool profile, deny list, exec policy, sandbox mode, plugin allowlist |
| **4 OS/Secrets** | Plaintext API keys, .env permissions, credentials dir, audit logs |
| **5 Financial** | Heartbeat model routing, spending limits, dangerous bypass flags |

Also supports **NVIDIA NemoClaw**:

```bash
./grits-agent-scanner --agent nemoclaw
```

Checks binary scoping (NemoClaw#272), Landlock LSM, seccomp, TLS enforcement, inference cost controls.

---

## What Needs an Expert

Some findings require decisions about your infrastructure. The fixer lists them explicitly.

| Issue | Why it cannot be automated |
|---|---|
| Network segmentation | VLAN/subnet design specific to your environment |
| Gateway auth + allowlists | You supply the token and user IDs |
| API key migration | You choose the secrets backend |
| Audit logging | Depends on your SIEM |
| Spending limits | Set at your API provider |
| Multi-agent isolation | Architectural trust boundary decision |

**[Schedule a free 30-min call with an X Scale AI security engineer](https://xscaleai.com/consult)**

---

## Output Formats

```bash
./grits-audit                                # full audit: scan + fix + verify (recommended)
./grits-audit --scan-only                    # scan only, no fix prompt
./grits-audit --agent nemoclaw               # audit NemoClaw
./grits-audit --yes                          # non-interactive (CI/CD)

./grits-agent-scanner                        # scan only, terminal output
./grits-agent-scanner --report               # markdown (for PRs, wikis)
./grits-agent-scanner --json                 # JSON (for dashboards, automation)
./grits-agent-scanner --agent nemoclaw       # scan NemoClaw
./grits-agent-scanner --config /path/to/file # custom config location
```

---

## Reference Configs

Hardened configs ready to diff and apply. See [`score/configs/install-guide.md`](score/configs/install-guide.md) for environment variable setup.

| Config | What it is |
|---|---|
| `score/configs/openclaw.json.default` | Vanilla out-of-the-box config |
| `score/configs/openclaw.json.hardened.linux` | Hardened Linux config (env var placeholders) |
| `score/configs/openclaw.json.hardened.mac` | Hardened macOS config (env var placeholders) |

---

## Hardening Guides

| Agent | Guide |
|---|---|
| OpenClaw | [`apply/openclaw/hardening-baseline.md`](apply/openclaw/hardening-baseline.md) |
| NVIDIA NemoClaw | [`apply/nemoclaw/README.md`](apply/nemoclaw/README.md) |

---

## Quick Checklists

Five yes/no checklists, one per layer. Copy, fill in, share with your team.

| Layer | Checklist |
|---|---|
| 01 Network | [`checklists/01-network-safety.md`](checklists/01-network-safety.md) |
| 02 Operator | [`checklists/02-operator-identity.md`](checklists/02-operator-identity.md) |
| 03 Application | [`checklists/03-tool-permissions.md`](checklists/03-tool-permissions.md) |
| 04 OS/Secrets | [`checklists/04-secrets-exposure.md`](checklists/04-secrets-exposure.md) |
| 05 Financial | [`checklists/05-cost-exposure.md`](checklists/05-cost-exposure.md) |

---

## Attribution

Apache 2.0. Use, modify, and distribute freely.

Any use of GRITS scoring output must retain:

> Scored with GRITS by X Scale AI

See [NOTICE](NOTICE) for full requirements.

---

## Built on the GRITS Framework

This scanner implements the [GRITS Framework](https://github.com/X-Scale-AI/GRITS) -- the governance spec, control catalog, and compliance mappings that define the 5-Layer Zero-Trust model for AI agents.

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Issues and PRs welcome.
Vulnerabilities: [SECURITY.md](SECURITY.md).

---

<p align="center">
  <strong>GRITS v0.3.0</strong><br>
  Built by <a href="https://xscaleai.com">X Scale AI</a> &nbsp;|&nbsp;
  Apache 2.0 &nbsp;|&nbsp;
  <a href="https://xscaleai.com/consult">Free 30-min hardening call</a>
</p>
