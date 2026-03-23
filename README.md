# GRITS Audit: AI Agent Security Scanner & Auto-Fixer

<p align="center">
  <img src=".github/assets/grits-banner.svg" alt="GRITS Agent Scanner" width="100%">
</p>

[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)
[![Version](https://img.shields.io/badge/GRITS-v0.3.0-blue.svg)](https://github.com/X-Scale-AI/grits-agent-scanner)
[![Platform](https://img.shields.io/badge/platform-macOS%20%7C%20Ubuntu%20%7C%20CentOS%2FRHEL-lightgrey.svg)]()
[![By X Scale AI](https://img.shields.io/badge/by-X%20Scale%20AI-black.svg)](https://xscaleai.com)

70% of AI agent deployments ship with unrestricted access to your API keys, filesystem, and local network. Most operators never find out until something goes wrong.

**GRITS Audit** scans your agent's live configuration (OpenClaw and NVIDIA NemoClaw), scores your security posture across 5 layers, and patches what it can automatically -- with a full backup and one-command rollback before touching anything.

---

## ⚡ Quick Start

```bash
git clone https://github.com/X-Scale-AI/grits-agent-scanner.git
cd grits-agent-scanner
chmod +x grits-*
./grits-audit
```

No dependencies. Python 3 stdlib only.

> **Record a 10-second `./grits-audit` GIF and drop it here.**

---

## The 5-Layer Zero-Trust Architecture

Native tools like `openclaw security audit` act as smoke detectors -- they tell you there is a fire, but leave the high-risk manual patching to you. **GRITS is the fire extinguisher.** GRITS actively scans, scores, and remediates vulnerabilities across the five critical trust boundaries:

- **Layer 1: Network (Blast Radius):** Unrestricted LAN exposure. We detect and help restrict agents from port-scanning or attacking your private `192.168.x.x` home or VPC subnets.
- **Layer 2: Operator (Identity):** Missing trust boundaries. We enforce cryptographic Telegram ID allowlists so unauthorized users can't hijack your agent.
- **Layer 3: Application (Plugin Sandbox):** Unauthorized tool access. We lock down permissive tool policies to strict, explicit allowlists.
- **Layer 4: OS & Secrets (Credential Vault):** Credential leakage. We flag plaintext API keys sitting in `openclaw.json` and enforce system-level secret injection.
- **Layer 5: Financial (Cloud Cost Bleed):** Unthrottled API usage. We patch bloated contexts and re-route expensive background heartbeats to free, local SLMs.

Run `./grits-audit` to see exactly what you're exposing and what can be patched automatically right now.

---

## How It Works (Stateful & Safe)

Patching a live AI agent is risky. One malformed JSON bracket can crash the gateway. `./grits-audit` eliminates that risk using a stateful three-step workflow:

1. **Scans:** Evaluates your live config and scores your zero-trust posture.
2. **Safely Patches:** Takes a complete snapshot of your state *before* mutating anything. Applies the auto-fixes with your confirmation. (If anything breaks, `grits-audit --rollback` restores your exact previous state instantly).
3. **Re-scans:** Verifies the applied patches and generates your updated compliance score.

Currently supports **OpenClaw** and **NVIDIA NemoClaw**.

---

## Continuous Compliance (CI/CD Integration)

A secure baseline is useless if a developer accidentally opens a port, tweaks a plugin policy, or commits a plaintext API key next week. **Configuration drift is the #1 cause of compromised AI agents.**

GRITS isn't just a local developer tool; it is an automated gatekeeper designed to run headlessly in your CI/CD pipelines (GitHub Actions, GitLab CI, etc.).

- **Block Insecure Deployments:** Instantly fail the build and block pull requests if an agent's Zero-Trust boundaries are degraded.
- **Zero-Touch Auditing:** Generate machine-readable compliance reports (JSON, SARIF) on every single commit.
- **Enforce the Standard:** Guarantee that no agent hits production unless it strictly adheres to your 5-Layer Security Model.

To configure GRITS for headless pipeline execution and advanced reporting, run:

```bash
./grits-audit --yes --scan-only       # headless audit, no prompts
./grits-agent-scanner --json          # machine-readable output for pipelines
```

---

## The Baseline is Automated. The Hard Stuff Needs Expertise.

GRITS patches what can be scripted. Network segmentation, SIEM integration, zero-trust architecture, and compliance mapping require decisions specific to your infrastructure and your threat model.

If your scan output flags **Advanced Configurations** -- or if you are deploying agents in a production or regulated environment -- that is where X Scale AI comes in.

**[Book a free architecture review](https://xscaleai.com/consult)**

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Issues and PRs welcome.
Vulnerabilities: [SECURITY.md](SECURITY.md).

---

<p align="center">
  Built by <a href="https://xscaleai.com">X Scale AI</a> &nbsp;|&nbsp;
  Apache 2.0 &nbsp;|&nbsp;
  <a href="https://xscaleai.com/consult">Free architecture review</a>
</p>
