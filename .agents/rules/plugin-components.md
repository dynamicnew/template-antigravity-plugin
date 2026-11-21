---
trigger: model_decision
description: Rules for authoring shipped plugin components under skills/, agents/, rules/, and hooks.json at the plugin root.
---

- **Plugin skills (`skills/<skill-name>/SKILL.md`):**
  - Scan depth is flat (one level under `skills/`). YAML frontmatter requires
    lowercase kebab-case `name` (1–64 chars) matching the parent directory name
    exactly, and `description` (1–1,024 chars) stating both what the skill does
    and when to trigger it. Plugin skills are namespaced as
    `<plugin-name>:<skill-name>` (invoked via `/<skill-name>` or
    `/<plugin-name>:<skill-name>`); use `disable-model-invocation: true` only
    for explicit slash-command workflows.
  - Reference bundled helpers inside that skill folder using skill-relative
    paths (`scripts/check.mjs`, `references/checklist.md`) — never
    `${PLUGIN_ROOT}` or absolute paths. Keep skills focused and keep helper
    scripts executable (`0755`) with LF endings.
- **Plugin custom agents (`agents/<agent-name>.md` or `agents/<agent-name>/agent.md`):**
  - Scan depth is flat (one level). Frontmatter requires kebab-case `name` and
    clear `description`; configure `mainAgent`, `subagent`, `model` (`inherit`,
    `flash_lite`, `flash`, or `pro`), `commandExecutionPolicy` (`off`, `auto`,
    `eager`, `sandbox`), `skills`, `inheritMcp`, and `inheritCustomizations`.
  - Verify every entry in `tools` matches exact Antigravity built-in tool names
    (for example, `view_file`, `grep_search`, `find_by_name`, `list_dir`,
    `replace_file_content`, `write_to_file`, `run_command`, `send_message`) —
    unmapped or misspelled tool names can error or hang execution. Omit
    file-edit tools (and omit `run_command` unless strictly read-only inspection
    is required) for read-only reviewers.
- **Plugin rules (`rules/AGENTS.md` or `rules/<rule-name>.md`):**
  - Scan depth is flat (immediate `.md` files in `rules/` only).
  - `rules/AGENTS.md` (or `rules/GEMINI.md`) loads as always-on without
    frontmatter; keep always-on content minimal (20,000-token aggregate budget,
    24,000-byte per-file cap).
  - Every other `rules/<rule-name>.md` MUST begin with YAML frontmatter
    declaring a valid snake_case `trigger` (`always_on`, `model_decision`,
    `glob`, or `manual`; invalid triggers are silently discarded). Include
    `description` for `model_decision` rules. For `glob`, `globs` MUST be a
    quoted comma-separated string (`"**/*.tf, **/*.yaml"`), never a YAML array.
- **Plugin lifecycle hooks (`hooks.json`):**
  - Top-level keys are named hook groups (`enabled` defaults to `true`).
  - `PreToolUse` and `PostToolUse` take an **array** of
    `{ "matcher": "<tool regex>", "hooks": [...] }` objects. `SessionStart`,
    `PreInvocation`, `PostInvocation`, and `Stop` use a **flat array** of
    handler objects directly under the event key.
  - Hook commands run synchronously with `cwd` set to `<plugin-root>`: use
    relative paths (`node ./scripts/policy.mjs`), NOT `${PLUGIN_ROOT}`.
  - Read camelCase JSON from `stdin`, exit `0`, and write valid JSON to `stdout`
    (any non-zero exit fails open and logs an error). Keep `timeout` tight;
    note `PostToolUse` receives `toolCall` and `error` (not tool output) and
    returns `{}`.
