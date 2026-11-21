# Read-list: implement

Use for a bounded change the coordinator has already planned.

- **Read:** the files the task owns, their tests, the matching conditional rule
  under `.agents/rules/` (or `.github/instructions/`), and the section of the
  plan or [docs/plugin/spec.md](../../plugin/spec.md) the task cites by heading.
- **Do not read:** other sections of the handoff, history archives, or files
  owned by another subagent.
- **Do:** make the change cleanly (keeping contributor scaffolding separate from
  shipped plugin files), run the targeted unit/validation checks, preserve LF
  endings and `0755` permissions on `scripts/`, and remove temporary files you
  created.
- **Do not:** refactor files outside the task, modify `~/.gemini/config/plugins/`
  unless explicitly tasked, commit, or change settings.
- **Return:** at most 15 lines: files changed, commands run with exact results,
  and anything unverified.
