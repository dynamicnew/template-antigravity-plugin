## Summary

- What changed and why:
- Source branch (`dev` from `feature/<topic>`, targeting `main`):

## Verification (Hybrid: Tests + Plugin CLI)

- [ ] Unit / schema / hook / MCP server tests (local or Docker): <!-- exact command + pass/fail counts -->
- [ ] `agy plugin validate` (`--strict` on a clean plugin root/bundle without root `AGENTS.md`, or `--json | jq` filtering only expected contributor/other-tool warnings): <!-- exit code / findings -->
- [ ] Local install smoke check (`agy plugin install` + `agy plugin list --json` / `agy agents`): <!-- component counts or why skipped -->
- [ ] Hygiene checked (no secrets, no hardcoded user paths, LF endings, `0755` on shipped `scripts/` executables, no tracked `node_modules/`)

## Marketplace / Release Checks (if releasing)

- [ ] SemVer bumped in `plugin.json` (or `.antigravity-plugin/plugin.json`)
- [ ] `displayName`, `description`, `logo` (Light + Dark legible), `suggestedPrompts` (<= 3), `author`, and pre-install `README.md` verified

## Unverified / Limits

- <!-- e.g., live Antigravity App MCP App iframe rendering, OAuth browser sign-in, Marketplace packaging -->

## Documentation & Handoff

- [ ] Updated `docs/ai/handoff.md` (and `docs/ai/decisions.md` / `docs/plugin/spec.md` if applicable)
- [ ] Kept internal contributor notes out of user-facing plugin `README.md`
