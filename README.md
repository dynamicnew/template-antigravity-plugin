# Agent project template: Antigravity Plugin (Dual-Platform)

A reusable, dual-platform (**Google Antigravity** and **GitHub Copilot**)
agent scaffolding template for building, testing, validating, and publishing
Google Antigravity plugins—covering skills, MCP servers, interactive MCP Apps
UIs, custom agents/subagents, rules, and lifecycle hooks across both standalone
(`plugin.json`) and multi-tool (`.antigravity-plugin/plugin.json`) layouts.
Contains no plugin source code, secrets, or machine-local paths.

## How loading works

Files on disk cost zero tokens until loaded. Both Antigravity and GitHub Copilot
automatically discover root `AGENTS.md` and portable skills under
`.agents/skills/` (the open Agent Skills standard). Keep only `AGENTS.md` at the
root (no `GEMINI.md`). If you use GitHub Copilot Code Review or Visual Studio,
add a one-line `.github/copilot-instructions.md` containing:
`Follow [AGENTS.md](../AGENTS.md).` Do not use `@path` or `@[label](path)`
includes in either file.

| Layer | Loaded when | Platform | Cost |
|---|---|---|---|
| `AGENTS.md` | Every session (contributor workspace; ignored by plugin loader, emits a `--strict` warning if validated at repo root) | Both | Small and fixed (the only always-on file, <= ~70 lines) |
| `.agents/rules/*.md` | `trigger: model_decision` | Antigravity | Only when relevant |
| `.github/instructions/*.instructions.md` | Matching file (`applyTo`) | GitHub Copilot | Only when matching files are touched |
| `.agents/skills/*` | Names/descriptions at startup; `SKILL.md` on use | Both (portable) | Progressive disclosure |
| `.agents/agents/*` / `.github/agents/*` | When invoked / delegated | Platform-native | Only when used |
| `docs/ai/*` & `docs/plugin/*` | When `AGENTS.md` or rules point to them | Both | Only on demand, by heading |
| `docs/ai/read-lists/*` | When a subagent starts | Both | One small file per subagent role |

> **Contributor vs. shipped separation:** Antigravity's plugin loader reads
> shipped plugin components from `plugin.json` (or `.antigravity-plugin/plugin.json`),
> `skills/`, `agents/`, `rules/`, `mcp_config.json`, `hooks.json`, `scripts/`,
> and `assets/`. Root `AGENTS.md`, `.agents/`, `.github/`, `.mcp.json`, and
> `scripts/check-context-size.ps1` are contributor-only scaffolding: root
> `AGENTS.md` is not injected into end-user sessions, but triggers an expected
> warning under `agy plugin validate --strict` at repo root (filter expected
> contributor/other-tool warnings with `agy plugin validate --json | jq`, or
> run `--strict` on a clean staged bundle/plugin subdirectory). Exclude or
> stage around `scripts/check-context-size.ps1` if packaging cleanly from the
> repository root.
>
> **VS Code note:** Keep `.agents/skills` enabled in `chat.agentSkillsLocations`
> so GitHub Copilot loads the single canonical skill tree in `.agents/skills/`
> without duplicating skills into `.github/skills/`.

## Adopt it

1. Click **Use this template** on GitHub (or copy the contents of
   `antigravity-plugin/` into your repository root).
2. Fill in `Project essentials` in [AGENTS.md](AGENTS.md) with your plugin
   manifest layout, validation command (`agy plugin validate`), and unit/Docker
   test commands (keep `AGENTS.md` under ~70 lines).
3. Fill in [docs/plugin/spec.md](docs/plugin/spec.md) with your plugin identity,
   bundled components, auth/storage design, and Marketplace checklist.
4. Replace or adapt this `README.md` with your plugin's pre-install Marketplace
   documentation once you begin authoring the plugin itself (keep internal
   contributor notes in `docs/ai/` rather than user-facing `README.md`).
5. Update [docs/ai/cost.md](docs/ai/cost.md) with your Antigravity and/or GitHub
   Copilot plan limits and preferred models.
6. If this repository supports ClarifyDoc, run `setup-clarifydoc` during
   adoption or initial setup to generate the ClarifyDoc rule, skills,
   `.gitignore` entry, and MCP guidance on demand (otherwise create none).
7. Delete any conditional rules you do not need (for example, `plugin-mcp-apps`
   if your plugin ships no `ui://` views). Fewer files means less startup
   metadata.
8. Run `pwsh -NoProfile -File scripts/check-context-size.ps1 -FailOnWarning` to
   verify always-on budgets, Antigravity rule caps, and skill deduplication.

## Curated skills included

Stored once in `.agents/skills/` for both platforms:

- **Core workflow:** `coordinate-task` (Antigravity main-agent coordination;
  Copilot uses `.github/agents/coordinator.agent.md`), `project-remember`,
  `systematic-debugging` (plugin discovery, MCP stdio/HTTP, MCP Apps `ui://`,
  subagent tool allowlists, and `hooks.json` contracts),
  `verification-before-completion` (evidence matrix separating unit/Docker
  checks, `agy plugin validate`, local `agy plugin install`, and live App UI
  qualification), and `audit-plugin-release` (pre-publish and Marketplace
  readiness audit).
- **On-demand generators:** `generate-handoff-canvas` (recreates a Copilot
  `.github/extensions/<id>/` canvas or Antigravity MCP App side-pane panel),
  `setup-clarifydoc` (detects ClarifyDoc support at template adoption/init and
  scaffolds only the needed ClarifyDoc config, rules, and workflow skills).

## Optional external skills

Heavy vendor skill bundles are deliberately excluded to keep this template
light. If you want deeper language- or protocol-specific authoring guides,
review and install only what your plugin needs via the open Skills CLI:

- **MCP server design (`mcp-builder`, `anthropics/skills`):**
  `npx skills add https://github.com/anthropics/skills --skill mcp-builder`
- **MCP Apps `ui://` views (`create-mcp-app` / `add-app-to-server`, `modelcontextprotocol/ext-apps`):**
  `npx skills add modelcontextprotocol/ext-apps --skill create-mcp-app`
- **Skill authoring & evals (`skill-creator`, `anthropics/skills` or `write-a-skill`, `mattpocock/skills`):**
  `npx skills add https://github.com/anthropics/skills --skill skill-creator`

## What is deliberately omitted

- **No prebuilt plugin code, manifest, or active `mcp_config.json`.** Workspace
  MCP examples live as empty `.example` files
  (`.agents/mcp_config.json.example` and `.mcp.json.example`); note that
  `.mcp.json`, `.agents/mcp_config.json`, and shipped plugin `mcp_config.json`
  use `"mcpServers"`, while VS Code's `.vscode/mcp.json` expects `"servers"`.
  Create your shipped `plugin.json` (or `.antigravity-plugin/plugin.json`),
  `mcp_config.json`, and `hooks.json` when scaffolding your plugin. Never commit
  literal secrets or user paths.
- **No enabled workspace dev hooks.** See `.agents/hooks/README.md` and
  `.github/hooks/README.md` (distinct from your plugin's shipped `hooks.json`).
- **No prebuilt canvas extension or ClarifyDoc skill trees.** Use
  `generate-handoff-canvas` and `setup-clarifydoc` only in repos that need them.

## Layout

```text
AGENTS.md                                  always-loaded dual-platform router (<= ~70 lines)
.agents/
  rules/                                   8 Antigravity conditional rules (model_decision)
  agents/                                  implementer, reviewer, handoff (explore uses built-in research)
  skills/                                  7 shared portable skills (core + release audit + canvas & ClarifyDoc generators)
  hooks/README.md                          workspace hook policy (none enabled; distinct from plugin hooks.json)
  mcp_config.json.example                  empty workspace MCP server list (Antigravity format)
.github/
  instructions/                            8 Copilot path-scoped instructions (applyTo)
  agents/                                  coordinator, explorer, implementer, reviewer, handoff
  hooks/README.md                          workspace hook policy (none enabled)
  pull_request_template.md                 PR verification & handoff checklist
docs/
  ai/                                      handoff.md, handoff-history/, todo.md, decisions.md, memory.md, cost.md, read-lists/
  plugin/spec.md                           plugin manifest, components, auth & Marketplace spec template
  codebase/README.md                       index for optional on-demand architecture maps
scripts/check-context-size.ps1             contributor-only context-budget & hygiene check (not a shipped plugin script)
.mcp.json.example                          empty workspace MCP server list (Copilot CLI / portable format)
.gitignore  .gitattributes                 dependency/secret hygiene & consistent LF endings
```
