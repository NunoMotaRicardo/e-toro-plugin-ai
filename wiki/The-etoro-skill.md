# The etoro skill

`skills/etoro/SKILL.md` is short on purpose: the rules, the route, and pointers. Detail lives in `references/` and is read only when needed.

## What it covers

Market data, portfolio and PnL, watchlists, feeds, popular investors, user analytics, agent portfolios (sub-portfolios), and trading (market open and close, limit orders, cancels).

## How it picks a route

1. The `etoro-public-api` MCP server is connected: use it.
2. Else the `etoro` CLI is installed: use it.
3. Else REST.
4. No credentials, or a 401/403: run the `setup` skill.

| Reference | Read when |
|---|---|
| `references/mcp.md` | using the MCP route |
| `references/cli.md` | using the CLI |
| `references/rest-api.md` | using REST: headers, endpoints, body casing, errors, trade delivery, OAuth |
