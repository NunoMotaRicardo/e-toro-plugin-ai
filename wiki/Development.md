# Development

```
skills/etoro/            operating skill: SKILL.md + references/{mcp,cli,rest-api}.md
skills/setup/            first-time setup skill
.mcp.json                registers the MCP server
plugin.json              portable manifest (Codex, Copilot, Antigravity)
.claude-plugin/          Claude Code manifest
.cursor-plugin/          Cursor manifest
gemini-extension.json    Gemini CLI manifest
wiki/                    this documentation, mirrored to the GitHub wiki
```

## Conventions

- Skill folder name equals the frontmatter `name`; descriptions state when to trigger.
- Keep `SKILL.md` short: rules and pointers. Reference material goes in `references/`.
- Name and version are identical across the four manifests; bump all of them together on a user-visible change and tag `vX.Y.Z`.
- Anything that moves money defaults to demo and needs a per-execution confirmation.
- Never commit keys, tokens or account identifiers.

## Validate

```bash
claude plugin validate .
```

## Wiki

`wiki/` is the source of truth. The `wiki-sync` workflow mirrors it to the GitHub wiki on every push to `main` that touches it, so do not edit the GitHub wiki directly.
