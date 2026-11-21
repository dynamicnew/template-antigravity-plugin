---
trigger: model_decision
description: Guidance for authoring or modifying AGENTS.md, contributor workspace rules, instructions, agents, skills, and hook docs under .agents/ or .github/.
---

- Keep **contributor workspace** customizations (root `AGENTS.md`, `.agents/`,
  `.github/`) strictly separate from **shipped plugin** components (`plugin.json`
  or `.antigravity-plugin/plugin.json`, `skills/`, `agents/`, `rules/`,
  `mcp_config.json`, `hooks.json`). Follow the matching shipped plugin rules
  (`plugin-manifest-and-layout.md`, `plugin-mcp-servers.md`,
  `plugin-mcp-apps.md`, `plugin-components.md`) when editing shipped plugin
  files.
- Define clearly when guidance applies, its inputs, expected outcome, safety
  boundaries, and how failures or missing context are reported.
- Reuse canonical repository docs and tools instead of duplicating workflows.
  Keep model choices, external services, and automatic side effects optional
  unless explicitly required.
- Keep shared contributor skills in `.agents/skills/<name>/SKILL.md` (single
  portable source for Antigravity and GitHub Copilot) with frontmatter `name`
  matching the folder name; do not duplicate them under `.github/skills/`.
- Use valid platform frontmatter (`trigger: model_decision` for `.agents/rules/`,
  `applyTo` for `.github/instructions/`), working relative links, and no literal
  secrets or machine-specific absolute paths.
- Keep root `AGENTS.md` under ~70 lines; run
  `pwsh -NoProfile -File scripts/check-context-size.ps1` after edits.
