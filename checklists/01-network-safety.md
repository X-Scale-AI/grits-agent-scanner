# Layer 1: Network Safety Checklist

Five-minute gut check for AI agent network security. Yes or no.

---

**Is a host firewall running on the machine where your agent lives?**
[ ] Yes -- UFW, iptables, nftables (Linux) or Application Firewall (macOS)
[ ] No

**Does your firewall have a default-deny outbound policy?**
[ ] Yes -- only explicitly allowed ports can reach the internet
[ ] No -- all outbound traffic is allowed

**Is your OpenClaw gateway bound to localhost only?**
[ ] Yes -- `bind: loopback` or `bind: 127.0.0.1`
[ ] No -- bound to 0.0.0.0 or all interfaces

**Does your gateway require authentication?**
[ ] Yes -- `auth.mode: token` with a strong random token
[ ] No -- open or unauthenticated

**Is the machine running your agent on its own VLAN or subnet?**
[ ] Yes -- isolated from other devices on the network
[ ] No -- shares a network with laptops, NAS, routers

---

All yes: your network layer is solid.
Any no: run `./grits-agent-scanner` for specifics, then `./grits-agent-secure` for auto-fixes.

Items that require network architecture changes: https://xscaleai.com/consult

---
*GRITS v0.3.0 by X Scale AI -- https://github.com/X-Scale-AI/grits-agent-scanner*
