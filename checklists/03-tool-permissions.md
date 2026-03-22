# Layer 3: Tool Permissions Checklist

Five-minute gut check for AI agent tool access and sandbox security. Yes or no.

---

**Is the tool profile set to something other than `full`?**
[ ] Yes -- `minimal`, `standard`, or a custom explicit set
[ ] No -- set to `full` or unrestricted

**Does your deny list include dangerous tools?**
[ ] Yes -- `file_write`, `code_execution`, `shell_exec` are all denied
[ ] No -- no deny list, or missing these entries

**Is `execSecurity` set to restricted or disabled?**
[ ] Yes -- agent cannot escalate execution
[ ] No -- open or unrestricted

**Is sandbox mode enabled?**
[ ] Yes -- `agent.sandbox: true`
[ ] No -- not set or explicitly disabled

**Is there an explicit plugin allowlist?**
[ ] Yes -- `plugins.allow` contains a specific list
[ ] No -- no allowlist, any plugin can load

---

All yes: your application layer is solid.
Any no: run `./grits-agent-secure --apply` to auto-fix OC06, OC07, and OC09.

---
*GRITS v0.3.0 by X Scale AI -- https://github.com/X-Scale-AI/grits-agent-scanner*
