---
applyTo: "tests/**,docker/**,compose.yaml,compose.yml,docker-compose*.y*ml,Dockerfile*,package.json,package-lock.json,pyproject.toml,.gitattributes,.github/workflows/**"
description: Hybrid testing, agy plugin validate (clean bundle vs contributor/multi-tool CI), local install smoke checks, and hygiene rules for Antigravity plugins.
---

- **Hybrid test model:** Run unit, schema, hook `stdin`/`stdout` contract, and
  MCP protocol/server tests locally or in Docker containers as configured for
  the project. Keep automated suites deterministic, offline, and backed by
  disposable fixtures (never mutating real user accounts or live `~/.gemini/`
  state).
- **Manifest & component validation (`agy plugin validate`):**
  - Clean plugin directory or staged release bundle (no root `AGENTS.md` or
    other-tool files): require `agy plugin validate --strict <plugin-root>` to
    exit `0` with zero errors and zero warnings.
  - Repository root containing contributor `AGENTS.md` or multi-tool files
    (`.mcp.json`, `mcp.json`, or a shadowed root `plugin.json` alongside
    `.antigravity-plugin/plugin.json`): run `agy plugin validate <plugin-root>`
    (fails on any error) or filter only those expected contributor/other-tool
    warnings in CI with `agy plugin validate --json <plugin-root> | jq -e ...`
    so real Antigravity warnings (bad `name`, missing `version`, broken `logo`)
    still fail CI.
- **Local install & iteration checks:**
  - Qualify packaging with `agy plugin install <plugin-root>`,
    `agy plugin list --json`, and `agy agents` (when bundling custom agents).
    Ask before modifying the user's global `~/.gemini/config/plugins/` outside
    approved local testing.
  - Remember iteration rules: plugins declaring MCP `variables` install disabled
    until configured via `/plugin` or Customizations; editing local stdio MCP
    server code requires restarting the server in Customizations; `ui://` HTML
    changes apply on closing and reopening the view.
- **Honest evidence tiers:** Report unit/mock/schema passes, `agy plugin validate`,
  local `agy plugin install`, live Antigravity App UI rendering, and Marketplace
  packaging separately. Never infer live App iframe/OAuth behavior from unit
  tests or CLI runs.
- **Repository hygiene:** Never commit `node_modules/`, `.venv/`, build
  artifacts, credentials, or absolute user paths. Preserve LF line endings
  (`.gitattributes`) and `0755` executable mode on shipped `scripts/`.
