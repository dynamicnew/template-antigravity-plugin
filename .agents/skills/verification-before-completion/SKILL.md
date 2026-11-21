---
name: verification-before-completion
description: Run targeted unit/Docker tests, agy plugin validate, and local install checks before claiming an Antigravity plugin change is fixed, valid, or ready, distinguishing unit/CLI checks from live Antigravity App UI verification.
---

# Verification before completion

Evidence before claims. A passing unit test or schema check is evidence about
that check, not proof that `agy plugin validate`, local installation, OAuth
sign-in, or live Antigravity App `ui://` rendering works.

## Match evidence to the claim

Before editing, state the expected plugin behavior and smallest relevant checks.
Never report unrun commands as passed, and label simulated/mocked results
plainly.

| Claim | Required evidence |
|---|---|
| Manifest & discovery valid | Fresh `agy plugin validate --strict <path>` (clean plugin root/bundle without root `AGENTS.md` or other-tool files) or `agy plugin validate --json <path> \| jq` filtering only expected contributor/other-tool warnings, exiting `0` with exact finding counts |
| Unit / schema / hook tests passed | Exact local or Docker test command with pass/fail/skip counts (including hook `stdin`/`stdout` JSON contract checks) |
| MCP server tools work | Automated stdio/HTTP protocol test or MCP Inspector run verifying tool list, schema validation, error handling, and plain-text output |
| MCP App UI verified | Single-file `ui://` HTML build output, size check (`<= 10 MiB`), CSP/DOM safety check, and live Antigravity App render check (distinct from browser harness) |
| Local install works | Fresh `agy plugin install <path>`, `agy plugin list --json` showing expected component counts, and `agy agents` (when bundling agents) |
| Marketplace ready | Completed [audit-plugin-release](../audit-plugin-release/SKILL.md) checklist with zero unresolved errors or Antigravity warnings |
| Docs / scaffolding verified | Relative link check, `git diff --check`, and `scripts/check-context-size.ps1` |

## Check packaging and hygiene alongside assertions

- Confirm no secrets, OAuth client secrets, or absolute user paths appear in
  `plugin.json`, `mcp_config.json`, `hooks.json`, `skills/`, `agents/`,
  `rules/`, or `scripts/`.
- Confirm shipped helper executables under `scripts/` (excluding contributor-only
  `scripts/check-context-size.ps1`) have LF line endings and `0755` mode, and no
  `node_modules/`, `.venv/`, or `.plugin-data/` files are tracked.
- Confirm internal contributor notes and open decisions stay in `docs/ai/`, not
  in pre-install `README.md` or `plugin.json` user copy.

## Verify, clean up, and report

1. Run the required checks after the final meaningful edit (rebuilding Docker
   images first if tests run in Docker).
2. Read full outputs, exit codes, pass/fail/skip counts, and every
   `agy plugin validate` finding.
3. For delegated work, verify subagent diffs and command outputs yourself
   before accepting or committing on the feature branch.
4. Remove temporary files, scratch fixtures, or debugging logs.
5. Report exact commands, counts, and unverified limits plainly. If runtime
   evidence contradicts green checks, switch to
   [systematic-debugging](../systematic-debugging/SKILL.md).
