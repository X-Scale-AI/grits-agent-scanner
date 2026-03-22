# GRITS Agent Scanner -- Quickstart

Get from zero to a security score in under 5 minutes.

## Step 1: Clone and scan (30 seconds)

```bash
git clone https://github.com/X-Scale-AI/grits-agent-scanner.git
cd grits-agent-scanner

# OpenClaw
./grits-agent-scanner

# NVIDIA NemoClaw
./grits-agent-scanner --agent nemoclaw
```

No dependencies. Python 3 stdlib only.

The scanner reads your actual config files and checks 20 things across 5 security
layers. It tells you in plain language what each failure means, what you can fix
now, and what needs an expert. Nothing is modified.

## Step 2: Preview the fixes (1 minute)

```bash
./grits-agent-secure
```

Shows you exactly what will change, creates a backup location, and prints the
rollback command. Nothing is applied until you confirm.

## Step 3: Apply fixes (2 minutes)

```bash
./grits-agent-secure --apply
```

The fixer will:
1. Show what it will change
2. Create a full backup with a rollback command
3. Ask for confirmation
4. Apply only if you say yes

Covers: firewall, tool deny list, sandbox mode, file permissions, dangerous flags.

## Step 4: Re-scan to verify

```bash
./grits-agent-scanner
```

Your score should go up. Remaining findings are the ones that need human judgment
(network segmentation, secrets architecture, identity setup, audit logging).

## Step 5: Manual hardening

For the issues the auto-fixer cannot handle:

**Option A: Do it yourself** using the guides in `apply/openclaw/` or `apply/nemoclaw/`.

**Option B: Talk to an expert.** Schedule a free 30-min call: https://xscaleai.com/consult

## Step 6: Host hardening (optional, 10 minutes)

```bash
# Linux host (CIS Benchmark + DISA STIG)
sudo bash tools/harden.sh

# Docker host
sudo bash tools/harden-docker.sh
```

Review the configuration section at the top of each script before running.

## Output formats

```bash
# Terminal (default) -- color output, designed for screenshots
./grits-agent-scanner

# Markdown -- for PRs, wikis, security reports
./grits-agent-scanner --report > security-report.md

# JSON -- for dashboards, CI/CD, automation
./grits-agent-scanner --json > scan-results.json
```

---

*Scored with GRITS v0.3.0 by X Scale AI*
*https://github.com/X-Scale-AI/grits-agent-scanner | https://xscaleai.com*
