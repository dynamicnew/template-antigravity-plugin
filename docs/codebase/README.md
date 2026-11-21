# Codebase maps (optional, read on demand)

Add concise, evidence-backed reference maps here only as the plugin grows, so
agents can inspect a single subsystem without scanning large source trees or
inflating always-on `AGENTS.md`:

- `ARCHITECTURE.md` — plugin manifest layout, component discovery boundaries, and local vs remote MCP topology.
- `MCP-TOOLS.md` — MCP server tool catalog, input/output schemas, annotations, eager vs lazy settings, and error codes.
- `MCP-APPS.md` — `ui://` views, `com.google.antigravity/ui` entrypoints (`aux_pane`, `global`, `file`), CSP allowlists, and plain-text fallbacks.
- `HOOKS-AND-RULES.md` — `hooks.json` event contracts, timeout budgets, and `rules/` trigger matrix.
- `TESTING.md` — hybrid unit/Docker test suites, `agy plugin validate` filters, and local `agy plugin install` smoke procedures.
- `RELEASE.md` — semantic versioning, multi-tool packaging, and Antigravity Marketplace intake checklist.

Keep active task state in [docs/ai/handoff.md](../ai/handoff.md) and settled
architectural choices in [docs/ai/decisions.md](../ai/decisions.md).
