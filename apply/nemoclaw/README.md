# NemoClaw Hardening Guide

NVIDIA NemoClaw runs agents inside a sandbox defined by `openclaw-sandbox.yaml`.
The GRITS scanner checks 17 items across 5 layers.

```bash
./grits-agent-scanner --agent nemoclaw
```

---

## Critical: Binary Scoping (NVIDIA/NemoClaw#272)

NemoClaw policy presets ship without `binaries:` restrictions. This means any
process on the host -- curl, python3, node -- can use allowed network endpoints,
not just the agent binary. A prompt injection can use this to exfiltrate data.

Fix every network policy entry:

```yaml
network_policies:
  api_access:
    endpoints:
      - host: api.example.com
        port: 443
    binaries:
      - /usr/bin/agent-binary   # REQUIRED -- restrict to agent binary only
```

---

## Landlock LSM

NemoClaw filesystem policy is only kernel-enforced if Landlock is enabled.

Check:
```bash
cat /sys/kernel/security/lsm | grep landlock
```

Requires Linux kernel 5.13+. If not enabled, add `landlock` to `GRUB_CMDLINE_LINUX`
in `/etc/default/grub` and rebuild.

---

## Inference Cost Controls

NemoClaw has no native spending limits. All inference uses the NVIDIA cloud API.

Options:
1. Run a local Ollama instance and route low-priority tasks there
2. Monitor usage at build.nvidia.com
3. Set spend alerts at your cloud account level

---

## Audit Logging

NemoClaw TUI provides local monitoring only. No built-in SIEM forwarding.

Forward sandbox logs to your centralized system using syslog, Fluentd, or Vector.

**[SIEM integration and NemoClaw hardening design](https://xscaleai.com/consult)**

---

*GRITS v0.3.0 by X Scale AI -- https://github.com/X-Scale-AI/grits-agent-scanner*
