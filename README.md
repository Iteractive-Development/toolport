# toolport

Toolport MCP Hub plugin — portable Agent-Plugins format that installs natively in Enconvo, Codex and Claude Code.

[Toolport](https://toolport.app) is a Mac app that holds the connections and credentials for every MCP server you configure in it. This plugin exposes its gateway: every downstream server behind a handful of meta-tools, so hundreds of tools never flood your context.

## Install

- **Enconvo**: Plugins → **Add** → **Add from GitHub…** → `Iteractive-Development/toolport`, or from Terminal:

  ```
  npx -p @enconvo/api enconvo plugin install Iteractive-Development/toolport
  ```

- **Codex / Claude Code**: install this repository as a plugin the way each app takes GitHub plugins.

## Configure

The committed `mcp.json` (Enconvo, Codex) and `.mcp.json` (Claude Code) carry **no secrets**: the gateway URL and token are referenced through plugin settings (`${user_config.hub_url}`, `${user_config.api_token}`). After installing, open the plugin's page and fill in:

| Setting | Value |
|---|---|
| Toolport gateway URL | `http://127.0.0.1:8766/mcp` when Toolport runs on this Mac (the default), or your gateway's `https://` URL when it is remote |
| Toolport API token | Bearer token issued by the Toolport app (stored as a sensitive setting, never committed) |

Until the required token is filled in, the plugin shows **Need configure**; the gateway server starts as soon as it is set.

> **Note on `type: streamable-http`:** `mcp.json` uses the Agent-Plugins schema's spelling (`streamable-http`, the same transport Enconvo docs call `http`); `.mcp.json` (Claude Code) uses `http`. Enconvo accepts both spellings, and installs either file unchanged.

## Skills

- `toolport` — when and how to drive the gateway meta-tools.
- `toolport-catalog` — reference catalog of the enabled downstream servers and their tool counts.

## Security

No credential is committed to this repository, in any file or in its history. The token lives only in the plugin's sensitive settings on your Mac.
