# Workspace hooks

No workspace dev hooks are enabled. Workspace hooks live in `.agents/hooks.json`
and run synchronously during contributor sessions, so add one only for a
measured need (for example, blocking a destructive command).

**Distinct from shipped plugin hooks:** If your plugin itself bundles lifecycle
hooks for end users, define them at `<plugin-root>/hooks.json` and follow
[../rules/plugin-components.md](../rules/plugin-components.md).

In Antigravity, both workspace (`.agents/hooks.json`) and plugin
(`<plugin-root>/hooks.json`) hooks map named hook entries to `SessionStart`,
`PreToolUse`, `PostToolUse`, `PreInvocation`, `PostInvocation`, or `Stop`.
Handlers use `"type": "command"`, receive camelCase JSON on stdin, and return
JSON on stdout with exit code `0`.

If you enable a workspace hook, document here:

- what it does and when it runs,
- what it reads or changes,
- how to turn it off (for example, `"enabled": false` in `.agents/hooks.json`).

Never put secrets in a hook file or script.
