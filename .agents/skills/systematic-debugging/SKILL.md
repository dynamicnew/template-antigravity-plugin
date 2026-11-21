---
name: systematic-debugging
description: Investigate Antigravity plugin failures—manifest or component discovery issues, MCP stdio/HTTP connection or tool errors, MCP Apps ui:// rendering or CSP defects, subagent hangs, and hooks.json contract bugs—before changing code.
---

# Systematic debugging

Find the root cause of the observed plugin failure, not just a workaround.
Follow [AGENTS.md](../../../AGENTS.md) and never weaken schema checks, swallow
errors, or commit hardcoded credentials/paths.

## Establish the actual surface and copy

- Read `#current-handoff` in [docs/ai/handoff.md](../../../docs/ai/handoff.md)
  and relevant sections of [docs/plugin/spec.md](../../../docs/plugin/spec.md).
- Confirm which surface failed: unit/Docker test runner, `agy plugin validate`,
  Antigravity CLI (`agy`), Antigravity App, or Antigravity IDE.
- Confirm you are testing the edited source, not a stale installed copy in
  `~/.gemini/config/plugins/<plugin-name>/` (reinstall after manifest/file
  changes, restart local stdio MCP servers in Customizations after server code
  edits, and close/reopen `ui://` views after HTML edits).

## Collect evidence by component

1. **Manifest & discovery:** Run `agy plugin validate --json <plugin-root>`
   first. Check `.antigravity-plugin/plugin.json` precedence over root
   `plugin.json`, strict `additionalProperties: false`, flat 1-level scan depth
   in `rules/` and `agents/`, matching `skills/<name>/SKILL.md` folder/name,
   valid snake_case `trigger`, and comma-separated `globs` string (not array).
2. **MCP servers (`mcp_config.json`):**
   - Confirm server namespacing (`<plugin-name>_<server-key>`) and check
     whether per-server `variables` left the plugin disabled after CLI install.
   - Verify remote servers use Streamable HTTP (`serverUrl`/`url`, not legacy
     SSE/`httpUrl`) and OAuth CIMD/callback (`https://antigravity.google/oauth-callback`).
   - Verify stdio servers use `${PLUGIN_ROOT}` / `${PLUGIN_DATA}` (never
     hardcoded paths or duplicated `env` keys) and write logs strictly to
     `stderr` so `stdout` stays pure JSON-RPC.
3. **MCP Apps (`ui://`):** Verify MIME type `text/html;profile=mcp-app`,
   `_meta.ui.resourceUri`, separate tool for `file` entrypoints, 10 MiB HTML /
   1 MiB persisted result / 4 concurrent `tools/call` limits, default-deny CSP
   allowlist, and plain-text `content` fallback on Antigravity CLI.
4. **Custom agents (`agents/`):** Check every name in `tools` against exact
   Antigravity built-in tool names (unmapped/misspelled tool names can hang
   subagent execution) and confirm `subagent`/`mainAgent` flags.
5. **Lifecycle hooks (`hooks.json`):** Verify top-level named groups, flat
   handler arrays for `SessionStart`/`PreInvocation`/`PostInvocation`/`Stop`,
   relative `./scripts/...` paths (not `${PLUGIN_ROOT}`), `0755` permissions,
   LF line endings, and exit `0` + valid JSON on `stdout` (non-zero fails open).

## Test one hypothesis at a time

- State one evidence-backed hypothesis and test it with the smallest relevant
  check (unit/hook `stdin` replay, MCP inspector/test, or `agy plugin validate`).
- Fix the cause, not the symptom. Do not add arbitrary sleeps or loosen schemas.
- If three distinct attempts fail, stop and report what was ruled out and what
  remains unknown.

## Clean up and verify

Remove temporary diagnostics and follow
[verification-before-completion](../verification-before-completion/SKILL.md)
before claiming the fix is complete.
