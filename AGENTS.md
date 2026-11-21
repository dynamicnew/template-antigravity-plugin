# Project guidance

The only file loaded every session; read everything else on demand. Keep this
file under 70 lines.

## Project essentials (fill in)

- **Stack & layout:** <!-- Antigravity plugin (`plugin.json` or `.antigravity-plugin/plugin.json`), `skills/`, `agents/`, `rules/`, `mcp_config.json`, `hooks.json`, `scripts/`, `assets/`, optional `src/`/`ui/`/`tests/` -->
- **Commands:** <!-- validate: `agy plugin validate --json . | jq ...` (or `--strict` on clean bundle) | install: `agy plugin install .` | tests: `<unit or Docker cmd>` -->
- **Do not edit:** <!-- built UI bundles, `node_modules/`, `~/.gemini/config/plugins/` copies directly (edit source and reinstall), or unrelated repos -->

## When to read what

- Small task (one file, question, quick fix): read nothing extra.
- Substantial work: consult `find-skills` (if installed) once before planning.
- Multi-file or cross-session work: read `#current-handoff` in
  [docs/ai/handoff.md](docs/ai/handoff.md) and [docs/ai/todo.md](docs/ai/todo.md),
  then verify against Git/source.
- Manifest, surface, or Marketplace copy work: read [docs/plugin/spec.md](docs/plugin/spec.md).
- Large docs: locate the relevant heading and read only that section.
- Procedures (coordination, debugging, verification, release audit, memory,
  canvas, or `setup-clarifydoc` on adoption/init): load from `.agents/skills/`.
- Explicit user direction overrides any standing rule here.

## Rules

- **Truthful.** Run checks before claiming success. Separate unit/schema passes
  from `agy plugin validate`, local CLI install, and live Antigravity App UI
  checks. Never record secrets, personal data, or user paths.
- **Contributor vs. shipped.** Keep contributor scaffolding in root `AGENTS.md`,
  `.agents/`, `.github/`, and `scripts/check-context-size.ps1`; keep shipped
  files in `plugin.json` (or `.antigravity-plugin/plugin.json`), `skills/`,
  `agents/`, `rules/`, `mcp_config.json`, `hooks.json`, `scripts/`, `assets/`.
- **Portable paths & safety.** Use `${PLUGIN_ROOT}` / `${PLUGIN_DATA}` in
  `mcp_config.json` (never hardcoded paths or duplicated in `env`), relative
  `./scripts/...` in `hooks.json`, and skill-relative paths in `SKILL.md`. Keep
  shipped `scripts/` executable (`0755`, LF); never commit secrets.
- **Hybrid verification.** Run unit/schema/hook/MCP tests locally or in Docker;
  qualify packaging with `agy plugin validate` and local `agy plugin install`.
- **Branches & commits.** Develop on `feature/<topic>`, merge into `dev` (then
  delete the merged local branch with `git branch -d`), and PR `dev` into
  `main`. Commit verified batches (main agent / coordinator only; subagents do
  not commit). Do not push or rewrite history unless asked.
- **Ask first.** Stop and ask before changing IDE, user, account, or billing
  settings; modifying `~/.gemini/config/plugins/` outside approved local tests;
  accessing signed-in accounts; publishing to Marketplace; or destructive actions.
- **Cost & communication.** Read [docs/ai/cost.md](docs/ai/cost.md) for model
  tiers and delegation. Be terse; no progress narration; end with one short
  report (files changed, exact checks, unverified).
- **Memory.** Record verified lessons with `project-remember`. Keep shared
  decisions in `docs/ai/decisions.md`, not only in expiring memory.

## Subagents

Give each subagent only its bounded task, exclusive file ownership and read-list
from [docs/ai/read-lists/](docs/ai/read-lists/) (`research`/`Explorer` ->
`explore.md`, `implementer`/`Implementer` -> `implement.md`,
`reviewer`/`Reviewer` -> `review.md`). `handoff`/`Project Handoff` reads
`docs/ai/handoff.md` and `docs/ai/todo.md` directly.

## Map

| Need | Where |
|---|---|
| State, TODOs, decisions, lessons, cost | [docs/ai/](docs/ai/) (`handoff.md`, `todo.md`, `decisions.md`, `memory.md`, `cost.md`) |
| Plugin spec & optional codebase maps | [docs/plugin/spec.md](docs/plugin/spec.md), [docs/codebase/README.md](docs/codebase/README.md) |
| Conditional rules (Antigravity / Copilot) | [.agents/rules/](.agents/rules/), [.github/instructions/](.github/instructions/) |
| Subagent roles (Antigravity / Copilot) | [.agents/agents/](.agents/agents/), [.github/agents/](.github/agents/) |
| Shared skills (Antigravity + Copilot) | [.agents/skills/](.agents/skills/) |
