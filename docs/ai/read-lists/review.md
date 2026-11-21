# Read-list: review

Use for checking a diff or a plan.

- **Read:** the diff or files under review, the tests that cover them, and the
  matching rule (`plugin-manifest-and-layout`, `plugin-mcp-servers`,
  `plugin-mcp-apps`, `plugin-components`, or `plugin-validation-and-testing`)
  or decision they must follow.
- **Do not read:** the whole handoff or unrelated modules.
- **Do not:** edit files or install/mutate global plugin state.
- **Check:** manifest schema/branding, `${PLUGIN_ROOT}`/`${PLUGIN_DATA}` and
  secret hygiene, component frontmatter and hook `stdin`/`stdout` contracts,
  CLI plain-text fallback for MCP Apps, `scripts/` `0755` + LF endings, and
  honest separation of unit passes vs `agy plugin validate` / live App checks.
- **Return:** only findings you are confident about, each with file (line
  numbers are fine in ephemeral chat replies), problem, impact, and a suggested
  fix. Say plainly if there are none and what you could not verify.
