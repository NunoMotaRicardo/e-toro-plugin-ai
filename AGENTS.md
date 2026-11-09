# e-toro-plugin-ai — eToro plugin

AI plugin for eToro, published through the `nmr-plugins` marketplace (https://github.com/NunoMotaRicardo/nmr-plugins) to Claude Code, Codex, Copilot CLI, Gemini CLI, Antigravity and Cursor.

`CLAUDE.md` is a symlink to this file. Edit `AGENTS.md` only.

## Principles
- Skills are Agent Skills folders (`skills/<name>/SKILL.md`), kept tool-neutral. Flag any tool-specific feature in the skill itself.
- Anything that moves money (trades, orders, transfers) must default to the demo account and require explicit user confirmation per execution.
- Never commit API keys, tokens or account identifiers.

## Layout
```
AGENTS.md / CLAUDE.md   agent instructions (symlink)
wiki/                   documentation, mirrored to the GitHub wiki
.github/workflows/      CI (wiki-sync.yml)
```
`skills/etoro/` is the operating skill (short `SKILL.md`, detail in `references/`); `skills/setup/` is first-time setup; `.mcp.json` registers the MCP server. Manifests: `plugin.json`, `.claude-plugin/`, `.cursor-plugin/`, `gemini-extension.json` (keep name and version in sync).

## Conventions
- Skill folder name == frontmatter `name`; descriptions state when to trigger.
- Bump the plugin version on every user-visible change and tag releases `vX.Y.Z`.
- Document each skill and its safety rules in `wiki/`.

## Wiki
- `wiki/` is the source of truth; `Home.md` is the landing page. Do not edit the GitHub wiki directly: the `wiki-sync` workflow overwrites it on push to `main`.
- First-time setup: enable Wikis in repo settings and create one page by hand so the wiki repo exists.
