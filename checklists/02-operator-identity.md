# Layer 2: Operator Identity Checklist

Five-minute gut check for AI agent identity and access control. Yes or no.

---

**Is your Telegram DM policy set to `allowlist`?**
[ ] Yes -- only specific user IDs can message the agent
[ ] No -- open, any, or no policy set

**Does your DM allowlist contain explicit user IDs?**
[ ] Yes -- `allowFrom: [your_telegram_id]`
[ ] No -- no allowFrom configured

**Is your group chat policy set to `allowlist`?**
[ ] Yes -- only specific users can command the agent in groups
[ ] No -- open to all group members

**Do you know who has access to the gateway token?**
[ ] Yes -- only you (or your CI system)
[ ] No -- shared, embedded in config, or unknown

**Is the OpenClaw CLI installed?**
[ ] Yes -- `openclaw` on PATH
[ ] No

---

All yes: your operator layer is solid.
Any no: run `./grits-agent-scanner` for specifics.

Items that require user ID configuration: https://xscaleai.com/consult

---
*GRITS v0.3.0 by X Scale AI -- https://github.com/X-Scale-AI/grits-agent-scanner*
