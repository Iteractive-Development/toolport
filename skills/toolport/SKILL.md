---
name: toolport
description: >
  Connects EnConvo to Toolport, the local MCP hub: one gateway exposing every MCP server configured in the Toolport app through search, call, script, confirm and paging meta-tools.
metadata:
  author: brad
  version: "1.1.1"
---

# Toolport — the local MCP hub

Toolport (a Mac app) holds the connections and credentials for every MCP server you configure in it. Instead of mounting hundreds of downstream tools into your context, it exposes **six meta-tools** — five registered at rest (`toolport_status`, `toolport_search_tools`, `toolport_call_tool`, `toolport_fetch_result`, `toolport_run_script`), plus `toolport_confirm`, which appears while a destructive call is blocked. Downstream tools are discovered on demand.

## The pattern (always follow this order)

1. **Search**: `toolport_search_tools` with a plain-language query of WHAT you want to do ("send an email", "list neon databases", "scrape this url"). It returns matching tool names + their input schemas.
2. **Read the schema**: pick the best hit; the result carries the tool's exact `name` and parameters. Never guess a downstream tool's name or parameters.
3. **Call**: `toolport_call_tool` with `name` exactly as returned and **every parameter inside the `arguments` object** — keys and shapes must match that tool's schema.
4. **Handle the three special returns**:
   - Destructive call blocked → you get a preview with a `token`. Proceed only with the user's approval, then `toolport_confirm` with that `token`.
   - Result carries `[Toolport shaped this result]` with a cursor → page through with `toolport_fetch_result` and the cursor.
   - Advisor note about repeated similar calls → fetch the prepared draft with `toolport_fetch_result` and run it as one `toolport_run_script` call instead of repeating one-by-one.
5. **When stuck**: `toolport_status` lists enabled servers, per-server tool counts, and servers needing sign-in.

## Governance

- Destructive downstream tools are intercepted; every confirm re-enters current governance — a past approval never carries forward.
- Saved routines may be advertised as `toolport_routine_*` tools. Prefer a routine whose description matches the task over re-orchestrating the same steps. Every routine execution is governed anew.
- Never save a routine unless the user asks; tell the user a save-approval prompt is coming (statement, not question). Do not retry a save after denial or timeout.

## Credentials

Nothing is stored in prompts or plugin files. Secrets live in the Toolport app; the gateway injects them per call. If a server answers 401 / "needs sign-in", tell the user to open the Toolport app and sign in — do not retry in a loop.

## Choosing Toolport vs other tools

- Web search / reading / scraping, docs lookup (Cloudflare, DeepWiki, Hugging Face), email/chat (Beeper), design (Penpot, Untitled UI), SEO (Ubersuggest), monitoring (Sentry, Better Stack), AI dev services (Magica, Marky, Composio, Apper), local Mac services (iMCP), code intel (GitNexus): all live behind Toolport — search for them rather than assuming a built-in exists.
- The current catalog of enabled servers is in the companion skill `toolport-catalog`.
