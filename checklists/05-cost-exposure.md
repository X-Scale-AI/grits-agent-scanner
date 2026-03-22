# Layer 5: Cost Exposure Checklist

Five-minute gut check for AI agent cloud spend and budget controls. Yes or no.

---

**Is the heartbeat model set to a local or cheap model?**
[ ] Yes -- Ollama, llama.cpp, or a specified low-cost model
[ ] No -- defaults to the primary (expensive) cloud model

**Do you have a local LLM running for fallback routing?**
[ ] Yes -- Ollama, LM Studio, vLLM, or llama.cpp
[ ] No -- all inference hits the cloud API

**Have you set spending limits in your API provider dashboard?**
[ ] Yes -- hard cap at the provider level (OpenRouter, Anthropic, OpenAI)
[ ] No -- unlimited spend possible

**Is auto-topup disabled?**
[ ] Yes -- automatic balance refills are off
[ ] No -- balance tops up automatically

**Are dangerous bypass flags disabled?**
[ ] Yes -- no yolo, trustAll, autoApprove, dangerouslySkipPermissions in config
[ ] No -- one or more are enabled

---

All yes: your financial layer is solid.
Any no: run `./grits-agent-scanner` for specifics.

`./grits-agent-secure --apply` auto-fixes OC16 (dangerous flags).

Heartbeat routing and spending limits require manual config changes:
https://xscaleai.com/consult

---
*GRITS v0.3.0 by X Scale AI -- https://github.com/X-Scale-AI/grits-agent-scanner*
