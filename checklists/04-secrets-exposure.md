# Layer 4: Secrets Exposure Checklist

Five-minute gut check for AI agent credential security. Yes or no.

---

**Are API keys stored as environment variables, not in openclaw.json?**
[ ] Yes -- config uses `${ENV_VAR}` references
[ ] No -- keys are hardcoded in the config file

**Are .env files owned by root with 600 permissions?**
[ ] Yes -- `sudo chown root:root .env && sudo chmod 600 .env`
[ ] No -- world-readable or user-owned

**Is the credentials directory locked to 700?**
[ ] Yes -- `sudo chmod 700 ~/.openclaw/credentials`
[ ] No -- group or world accessible

**Are agent activity logs forwarded outside this machine?**
[ ] Yes -- SIEM, syslog, journald with remote forwarding
[ ] No -- local only or no logs

**Do you know what the agent logged in the last 24 hours?**
[ ] Yes -- you have visibility into agent activity
[ ] No

---

All yes: your secrets layer is solid.
Any no: run `./grits-agent-secure --apply` to auto-fix OC12 and OC13.

API key migration and audit logging require expert design: https://xscaleai.com/consult

---
*GRITS v0.3.0 by X Scale AI -- https://github.com/X-Scale-AI/grits-agent-scanner*
