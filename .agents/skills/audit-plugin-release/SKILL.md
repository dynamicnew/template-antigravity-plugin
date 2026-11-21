---
name: audit-plugin-release
description: Run a pre-publish and Antigravity Marketplace readiness audit on a plugin directory—checking plugin.json schema and branding, multi-tool validation, portable paths, executable permissions, MCP/MCP App constraints, and pre-install README quality.
---

# Audit plugin release readiness

Use before tagging a release, sharing a GitHub plugin repository, or submitting
to the Antigravity Marketplace. Follow [AGENTS.md](../../../AGENTS.md) and
[docs/plugin/spec.md](../../../docs/plugin/spec.md). Report every failure,
warning, and unverified check plainly.

## 1. Manifest (`plugin.json` or `.antigravity-plugin/plugin.json`)

- Confirm `$schema` points to
  `https://antigravity.google/schemas/v1/plugin.json` and zero unknown top-level
  keys exist (`additionalProperties: false`).
- Confirm `name` matches `^[a-z0-9]+(-[a-z0-9]+)*$`.
- Confirm `displayName` omits `"Plugin"`/`"MCP"` suffixes and does not lead with
  `"Antigravity"`, `"Gemini"`, `"Claude"`, or `"Cursor"`.
- Confirm SemVer `version` was bumped since the previous release, `description`
  is 1–2 concise sentences (~120–160 chars), `suggestedPrompts` has <= 3
  non-empty actionable prompts, and `author` (with `https://` URL if object),
  `homepage`, `license`, and `keywords` are populated.
- Confirm `logo` resolves to a bundled 1:1 square image (`>= 128x128px`, ~10%
  transparent padding) legible on both Light and Dark themes.

## 2. Bundled components & portable paths

- **Skills (`skills/<name>/SKILL.md`):** flat 1-level scan; frontmatter `name`
  matches parent folder; `description` states what + when; paths to helper
  scripts/references are skill-relative (never `${PLUGIN_ROOT}` or absolute
  paths).
- **MCP (`mcp_config.json`):** uses `serverUrl`/`url` (Streamable HTTP) or
  cross-platform stdio (`"command": "node"` preferred); uses `${PLUGIN_ROOT}` /
  `${PLUGIN_DATA}` without redeclaring them in `"env"`; `variables` are scoped
  inside `mcpServers.<server>.variables`; zero literal tokens or user paths.
- **MCP Apps (`ui://`):** `text/html;profile=mcp-app` <= 10 MiB, plain-text
  CLI fallback present, `file` entrypoints isolated on dedicated tools, eager
  persistence (no teardown hook), default-deny CSP.
- **Agents (`agents/`):** flat 1-level layout; every `tools` entry matches an
  exact Antigravity built-in tool name.
- **Rules (`rules/`):** flat 1-level layout; valid snake_case `trigger` (plus
  `description` for `model_decision`) on every named rule; `globs` is a
  comma-separated string (not a YAML array); each file <= 24,000 bytes.
- **Hooks (`hooks.json`):** named hook groups; array of `{ matcher, hooks }`
  objects for `PreToolUse`/`PostToolUse` and flat handler arrays for
  `SessionStart`/`PreInvocation`/`PostInvocation`/`Stop`; relative
  `./scripts/...` commands (not `${PLUGIN_ROOT}`); exit `0` + JSON `stdout`.

## 3. Packaging hygiene & pre-install docs

- Confirm shipped helper executables under `scripts/` (and `skills/*/scripts/`)
  have LF line endings and `0755` executable mode. Treat
  `scripts/check-context-size.ps1` as contributor-only scaffolding (exclude or
  stage around it when packaging cleanly from repo root).
- Confirm pre-install `README.md` (or `.antigravity-plugin/README.md`) covers
  setup prerequisites, bundled components, auth/variables, and example workflows
  with no internal handoff or contributor notes.
- Confirm no `node_modules/`, `.venv/`, build scratch, secrets, or user paths
  are tracked.

## 4. CLI validation & local smoke check

- Clean plugin directory or staged bundle (no root `AGENTS.md` or other-tool
  files): run `agy plugin validate --strict <plugin-root>`.
- Repository root with contributor `AGENTS.md` or multi-tool files (`.mcp.json`,
  `mcp.json`, or shadowed root `plugin.json` alongside
  `.antigravity-plugin/plugin.json`): run `agy plugin validate --json <plugin-root>`
  and verify zero errors and zero warnings outside those expected
  contributor/other-tool files.
- With user approval when modifying global state, run
  `agy plugin install <plugin-root>`, `agy plugin list --json`, and
  `agy agents` (if bundling agents), and confirm every expected component loads.
- End with one short readiness report listing pass/fail findings and any
  unverified live App or Marketplace intake checks.
