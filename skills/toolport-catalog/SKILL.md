---
name: toolport-catalog
description: Reference catalog of the Toolport MCP hub — the gateway meta-tools in full detail, and every enabled downstream server with its tool count and purpose. Use when deciding which server/tool to search for, when toolport_search_tools returns too many or too few hits, or to answer "what can Toolport do".
---

# Toolport catalog

Counts reflect the last audit (2026-11-02); live truth is always `toolport_search_tools` / `toolport_status`.

## The gateway meta-tools

Five are registered at rest; `toolport_confirm` appears only while a destructive call is waiting for approval.

### toolport_search_tools
Single gateway to every connected MCP server and all their tools. **Try this FIRST for any external action or data.** Params: `query` (plain language, what you want to accomplish — search terms, not tool names), optional filters (`server` to limit to one server, `limit` 10–50). Returns ranked tool hits with exact `name` and full input `schema` per hit. Tool names are prefixed by server with a double underscore (`server__tool`, e.g. `brave_search__brave_web_search`).

### toolport_call_tool
Invoke a tool found via search. Params: `name` (exact name from the search result), `arguments` (object — ALL of that tool's parameters go inside, matching its schema). Destructive calls are intercepted and return a preview with a `token` instead of running.

### toolport_confirm
Execute a blocked destructive call. Params: `token` from the preview. Only call after the user approves the exact action in conversation. Not listed at rest — it registers while a blocked call is pending, so an empty `toolport_status` for it is expected.

### toolport_fetch_result
Read more of a large tool result. Params: `cursor` from the `[Toolport shaped this result]` marker, or a prepared draft id from an advisor note. Use instead of re-calling the tool.

### toolport_run_script
Run one JavaScript script server-side instead of many separate tool calls. Prefer it when you already know the steps; explore with `toolport_search_tools` first. Call tools as `servers.<server>.<tool>(args)`, or `toolport.call` / `callAsync` / `callAll` / `fetchResult`; loop, branch, project, then `return` one value. On failure, `structuredContent.toolportScript.progress` lists the calls that already ran — resume by index. Gates match `toolport_call_tool`.

### toolport_status
Enabled servers with tool counts, servers needing sign-in, and the discovery mode. Zero-arg fact check when unsure what exists.

## Enabled downstream servers (25)

| Server | Tools | What it's for |
|---|---|---|
| apper | 30 | Apper no-code app builder: create/edit/deploy apps, connect a live database (schema, RLS), edge functions, secrets |
| beeper | 12 | Beeper universal messaging: chats, messages, accounts, reminders, drafts |
| better_stack | 154 | Better Stack monitoring: monitors, incidents, heartbeats, escalation policies, dashboards, status pages, logs |
| brave_search | 8 | Brave web search: web, news, image, video, local; LLM-context API for agent-optimized page content |
| cloudflare_docs | 2 | Cloudflare product documentation search + Pages→Workers migration guide |
| composio | 12 | Composio connector hub: search tools across connected SaaS apps, manage app connections, execute in parallel |
| deepwiki | 3 | DeepWiki: AI answers about any GitHub repo's codebase, grounded in its wiki |
| firecrawl | 28 | Firecrawl: scrape/crawl sites, structured-data extraction, multi-site research agent |
| giststack | 8 | Giststack: brand profiles and AI content insights — workspaces, feed sources, insights/stories ranked by viral score |
| gitnexus | 17 | GitNexus code knowledge graph: symbol 360° context, pre-change API impact reports, structural checks, Cypher queries |
| hugging_face | 4 | Hugging Face Hub: search repos (models/datasets/Spaces), repo details, papers, auth context |
| imcp | 44 | iMCP — local Mac data & hardware: contacts, calendar, notes, messages, reminders, mic, camera, screenshots |
| jiro_build | 3 | Jiro Build: search/fetch 1000+ production-ready React/Tailwind components and full-page templates |
| magica | 17 | Magica AI media generation: one-shot image/media generation, credit balance, media library |
| marky | 39 | Marky social media management: posts, on-brand designs from templates, media library, scheduling |
| neon | 127 | Neon serverless Postgres: branches, databases, schemas and SQL, connection strings (sign-in completed 2026-11-02) |
| obscura | 38 | Obscura local browser automation: navigate, snapshot, click/fill, cookies, JS evaluation, structured extraction |
| penpot | 4 | Penpot design: high-level API overview, shape export to PNG/SVG, execute JS in the Penpot plugin context |
| sentry | 14 | Sentry error tracking: Seer root-cause analysis, issues, releases, dropped-event forensics |
| ubersuggest | 61 | Ubersuggest SEO: keywords, keyword lists, competitors, backlinks/anchor texts, site projects |
| untitled_ui | 7 | Untitled UI React components and page templates with CLI install commands |
| z_ai_vision_mcp | 8 | Z.ai vision: image/video analysis, charts & dashboards, error-screenshot diagnosis |
| z_ai_web_reader | 1 | Z.ai web reader: fetch a URL, converted for LLM input |
| z_ai_web_search | 1 | Z.ai web search: titles, URLs, summaries |
| z_ai_zread | 3 | Z.ai Zread: GitHub repo structure, file contents, docs/issues/commits search |

- Query the OUTCOME ("get github repo structure", "search instagram hashtags"), not a guessed tool name.
- Too many hits: add the server name as a word in the query, or pass the server prefix.
- Every call: `name` verbatim from the hit; all params nested in `arguments`.
