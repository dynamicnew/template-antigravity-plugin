# Plugin specification (fill in)

Read this before editing `plugin.json`, `.antigravity-plugin/plugin.json`,
bundled components, or Marketplace-facing copy. Keep internal open questions
out of user-facing `README.md` and `suggestedPrompts`.

## Identity and layout

- **Plugin `name` (kebab-case):** `<!-- e.g., acme-cloud -->`
- **`displayName`:** `<!-- e.g., Acme Cloud (no Antigravity/Gemini/Claude/Cursor prefix; no Plugin/MCP suffix) -->`
- **Current `version`:** `<!-- e.g., 0.1.0 -->`
- **Publisher `author`:** `<!-- e.g., { "name": "Acme Corp", "url": "https://acme.example.com" } -->`
- **Manifest layout:** `<!-- standalone root plugin.json | multi-tool .antigravity-plugin/plugin.json -->`
- **Bundled logo:** `<!-- e.g., assets/logo.svg (1:1 square, >= 128x128px, ~10% padding, legible on Light + Dark) -->`
- **Target surfaces:** `<!-- Antigravity App / Antigravity CLI / Antigravity IDE -->`
- **Distribution target:** `<!-- workspace .agents/plugins/ | GitHub URL | Antigravity Marketplace -->`

## Bundled components inventory

| Component | Path | Status | Purpose / notes |
|---|---|---|---|
| Skills | `skills/<name>/SKILL.md` | *(Planned / Active / Unused)* | *(List slash commands & triggers)* |
| MCP servers | `mcp_config.json` | *(Planned / Active / Unused)* | *(Local stdio `node` vs remote Streamable HTTP)* |
| MCP Apps UI | `ui://...` | *(Planned / Active / Unused)* | *(Inline / `aux_pane` / `global` / `file` extensions)* |
| Custom agents | `agents/<name>.md` or `agents/<name>/agent.md` | *(Planned / Active / Unused)* | *(`mainAgent` / `subagent`, tool allowlist)* |
| Rules | `rules/` | *(Planned / Active / Unused)* | *(`rules/AGENTS.md` or frontmatter `rules/<name>.md`)* |
| Lifecycle hooks | `hooks.json` | *(Planned / Active / Unused)* | *(Events, scripts, fail-open impact)* |

## Auth, credentials, and state boundaries

- **Authentication mode:** `<!-- None | OAuth 2.0 PKCE (CIMD/DCR/manual) | Google ADC | per-server variables -->`
- **User-prompted `variables`:** `<!-- List mcpServers.<server>.variables names, scopes, and docs link (note: installs disabled until configured) -->`
- **Persistent state (`${PLUGIN_DATA}`):** `<!-- What is stored, max size, cleanup/migration plan -->`
- **Security & privacy boundaries:** `<!-- Read-only vs mutating tools, confirmation gates, CSP origins, excluded data -->`

## Marketplace and user-facing copy checklist

- [ ] `plugin.json` includes `$schema` (`https://antigravity.google/schemas/v1/plugin.json`) and zero unknown properties.
- [ ] `name` is lowercase kebab-case; `displayName` omits `Plugin`/`MCP` suffixes and platform prefixes.
- [ ] `description` is concise (1–2 sentences, ~120–160 chars) and accurate.
- [ ] `author` (with `https://` URL if object) and `logo` (bundled 1:1 >= 128x128px, legible on Light and Dark themes) are verified.
- [ ] `suggestedPrompts` has up to 3 concrete, actionable starter prompts.
- [ ] Pre-install `README.md` (or `.antigravity-plugin/README.md`) documents prerequisites, bundled components, auth setup, and example workflows with zero contributor/internal status notes.
- [ ] Every MCP App tool returns clear plain-text `content` for non-UI clients (Antigravity CLI / IDE).
- [ ] Shipped helper scripts under `scripts/` (excluding contributor-only `scripts/check-context-size.ps1`) have LF endings and `0755` executable permissions.
