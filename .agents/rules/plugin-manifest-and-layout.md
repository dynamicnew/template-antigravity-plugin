---
trigger: model_decision
description: Rules for Antigravity plugin.json manifests, standalone vs multi-tool layouts, logos, scripts permissions, and Marketplace packaging.
---

- **Manifest location:** Place `plugin.json` at `<plugin-root>/plugin.json` for
  standalone Antigravity plugins, or at
  `<plugin-root>/.antigravity-plugin/plugin.json` for multi-tool repositories
  that also ship `.claude-plugin/plugin.json` or `.cursor-plugin/plugin.json`
  (`.antigravity-plugin/plugin.json` takes precedence at runtime and is promoted
  to root `plugin.json` in Marketplace bundles). Keep component directories
  (`skills/`, `agents/`, `rules/`, `scripts/`, `mcp_config.json`, `hooks.json`)
  at `<plugin-root>`.
- **Schema compliance:** Include
  `"$schema": "https://antigravity.google/schemas/v1/plugin.json"`. The v1
  schema enforces `"additionalProperties": false` — do not add custom top-level
  keys outside the schema (`extensions` is available for namespaced metadata).
- **Naming & branding:**
  - `name` must be lowercase kebab-case (`^[a-z0-9]+(-[a-z0-9]+)*$`); it also
    namespaces bundled MCP servers (`<name>_<server>`) and skills
    (`<name>:<skill>`).
  - `displayName` must represent your product or brand: omit suffixes like
    `"Plugin"` or `"MCP"`, and never prefix with `"Antigravity"`, `"Gemini"`,
    `"Claude"`, or `"Cursor"`.
  - Keep `description` concise (1–2 sentences, ~120–160 characters) and bump
    SemVer `version` on every release so installed clients detect updates.
- **Marketplace presentation & attribution:**
  - `logo` must point to a bundled 1:1 square image inside the plugin (`.svg`,
    `.png`, `.jpg`, `.jpeg`, or `.webp`, >= 128x128px with ~10% transparent
    padding, such as `"assets/logo.svg"`) that remains legible on both Light
    and Dark themes.
  - `suggestedPrompts` accepts at most 3 non-empty, actionable starter prompts.
  - Provide `author` (string or `{ "name", "url" }` using `https://`), plus
    recommended `homepage` (`https://`), `repository`, SPDX `license`, and
    lowercase `keywords`.
  - Keep pre-install user documentation at `<plugin-root>/README.md` (or
    `.antigravity-plugin/README.md`) covering prerequisites, bundled components,
    auth setup, and example workflows.
- **Executables & multi-tool hygiene:** Place shipped helper executables under
  `scripts/` (or `skills/<name>/scripts/`) with LF line endings and `0755` mode
  so Marketplace bundles preserve executability. Treat
  `scripts/check-context-size.ps1` as contributor-only scaffolding (exclude or
  stage around it when packaging cleanly from repo root). Remember root
  `AGENTS.md`, `.mcp.json`, and `mcp.json` are contributor/other-tool files
  ignored by the Antigravity plugin loader.
