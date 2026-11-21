# Workspace hooks

No workspace dev hooks are enabled. Workspace Copilot hooks live in
`.github/hooks/*.json` and run shell commands automatically during contributor
sessions, so add one only for a measured need (for example, blocking a
destructive command).

**Distinct from shipped plugin hooks:** If your Antigravity plugin itself
bundles lifecycle hooks for end users, define them at `<plugin-root>/hooks.json`
and follow
[../instructions/plugin-components.instructions.md](../instructions/plugin-components.instructions.md).

In GitHub Copilot, repository hooks live in `.github/hooks/*.json` using
`"version": 1` and a `"hooks"` object mapping lifecycle events (`sessionStart`,
`preToolUse`, `postToolUse`) to command handlers (`"type": "command"`, `"bash"`,
and `"powershell"` or `"windows"`).

If you enable a workspace hook, document here:

- what it does and when it runs,
- what it reads or changes,
- how to turn it off (for example, `"disableAllHooks": true`).

Never put secrets in a hook file or script.
