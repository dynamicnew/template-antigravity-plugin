---
trigger: model_decision
description: Rules for building interactive MCP Apps (ui:// resources) and Antigravity UI entrypoints (aux_pane, global, file) inside an Antigravity plugin.
---

- **Protocol & bundling:** Follow the MCP Apps specification
  (`io.modelcontextprotocol/ui`) and `@modelcontextprotocol/ext-apps`. Serve a
  self-contained single-file `ui://` HTML resource with MIME type
  `text/html;profile=mcp-app` and reference it from the tool's
  `_meta.ui.resourceUri`.
- **Antigravity UI entrypoints (`_meta["com.google.antigravity/ui"].entrypoints`):**
  - Inline in chat: omit `aux_pane`/`global`/`file` entrypoints; card
    auto-resizes up to 800 px (falls back to 150 px if no `size-changed`
    arrives within 100 ms; design taller views to scroll internally).
  - `aux_pane`: opens beside the conversation (one tab per `(server, tool)`);
    manual launches pass `{}` and repeat agent calls update the open tab.
  - `global`: opens full-width outside any conversation from Plugins in the left
    sidebar with `{}`; conversation-only capabilities (`message`,
    `updateModelContext`) are omitted.
  - `file`: replaces the built-in viewer for claimed `extensions` (required
    `string[]`, normalized lowercase with leading dot). Always place `file`
    entrypoints on their own separate tool — the host unconditionally hides it
    from the model and passes `{"file": {"name", "resourceUri"}}`. Read via
    `resources/read` (`application/octet-stream` base64, max 10 MiB); enable
    writes only when `hostContext["com.google.antigravity/resources"]?.write` is
    truthy via `com.google.antigravity/resources/write` (`{ uri, text }` or
    `{ uri, blob }`, max 10 MiB).
- **Host limits & persistence:**
  - Keep `ui://` HTML documents and `antigravity-file://` reads/writes <= 10 MiB
    and persisted `CallToolResult` payloads <= 1 MiB (fetch larger datasets via
    an app-only `visibility: ["app"]` helper tool).
  - Stay within 4 concurrent `tools/call` requests per view (scoped to the
    owning server; aborted on close) and remember up to 5 hidden `aux_pane` /
    `global` frames stay mounted (LRU).
  - Antigravity sends **no** `ui/resource-teardown` notification on close:
    persist user edits eagerly.
  - Handle JSON-RPC `-32601` gracefully when calling optional Antigravity host
    actions such as `com.google.antigravity/toggle-aux-pane`.
- **Non-UI fallback & security:**
  - Antigravity CLI and IDE run tools as plain-text MCP without mounting
    iframes, and the CLI ignores `_meta.ui.visibility`. Always return clear
    plain-text `content` and enforce authorization/validation server-side.
  - Iframes run under default-deny CSP with no persistent browser storage:
    prefer `tools/call` over `connectDomains`, allowlist bare origins only in
    resource `_meta.ui.csp`, render untrusted text with `textContent` (never
    raw `innerHTML`), and support Light/Dark themes and `prefers-reduced-motion`.
