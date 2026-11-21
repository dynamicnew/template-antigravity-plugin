---
applyTo: "mcp_config.json,src/**,server/**,scripts/**"
description: Rules for authoring plugin mcp_config.json definitions, local stdio or remote Streamable HTTP MCP servers, plugin path variables, OAuth/credential variables, and tool design.
---

- **Configuration & namespacing:** Define bundled MCP servers under `mcpServers`
  in `<plugin-root>/mcp_config.json`. At runtime Antigravity namespaces each
  entry as `<plugin-name>_<server-key>`.
- **Transport selection:**
  - Remote servers must use Streamable HTTP via `serverUrl` (or `url`); legacy
    HTTP+SSE and `httpUrl` are unsupported.
  - Local stdio servers use `command` and `args`. Prefer `"command": "node"` so
    the plugin runs cross-platform and can fall back to Antigravity's bundled
    Node.js runtime. Never write logs to `stdout` on stdio servers (use
    `stderr`); `stdout` carries JSON-RPC frames exclusively.
- **Portable path variables:** Installed plugins unpack into a read-only folder
  replaced on updates (`~/.gemini/config/plugins/<plugin-name>`). Use
  `${PLUGIN_ROOT}` for read-only bundled scripts/assets and `${PLUGIN_DATA}` for
  persistent writable state outside the workspace (expanded in `args`, `cwd`,
  and custom `env` values). Never hardcode absolute user paths, and do not
  redeclare `PLUGIN_ROOT` or `PLUGIN_DATA` as keys in `"env"` (Antigravity
  exports both automatically).
- **Credentials & authentication:** Never commit API keys, tokens, or secrets.
  - Prefer OAuth 2.0 + PKCE for remote services: support CIMD
    (`https://antigravity.google/oauth/client-metadata.json`, redirect
    `https://antigravity.google/oauth-callback`) or DCR, or declare manual
    `oauth.clientId` (and optional `clientSecret`). Use
    `"authProviderType": "google_credentials"` for Google ADC.
  - For user-supplied API keys or endpoints, declare `variables` inside
    `mcpServers.<server>.variables` (never at the top level of
    `mcp_config.json`), interpolated via `${VARIABLE_NAME}` in `command`,
    `args`, `env`, `url` / `serverUrl`, `headers`, or `oauth`. Note that
    declaring `variables` leaves CLI installs disabled until configured, while
    OAuth-only installs enabled.
- **Tool loading, timeouts & quality:**
  - All MCP tools lazy-load by default; set `tools.<toolName>.eager: true` only
    for high-frequency core tools whose schema cost is justified on every turn.
    Use `enabledTools` / `disabledTools` and `timeoutSeconds` intentionally
    (defaults: 300s connect, 180s per call).
  - Design focused tools with strict input validation (Zod/Pydantic), accurate
    tool annotations (`readOnlyHint`, `destructiveHint`, `idempotentHint`),
    actionable error messages, and clear plain-text `content` alongside any
    `structuredContent` so non-UI hosts work cleanly.
