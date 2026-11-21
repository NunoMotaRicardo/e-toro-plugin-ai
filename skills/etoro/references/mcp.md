# MCP route

Server: `https://mcp.public-api.etoro.com`, registered as `etoro-public-api`. Auth happens in the browser at `api-portal.etoro.com` when the tools first prompt.

Read first, then act:

| Need | Tool |
|---|---|
| Portfolio, balances, positions, orders, history, watchlists | `get-my-*` tools |
| Instrument lookup | `get-instruments-overview` |
| Trader profile | `get-trader-profile-summary` |
| Any other route | `get-all-routes` → `get-route-spec` → `execute-read` / `execute-write` |
| Open a trade | `prepare-trade` → show the result to the user → `place-trade` |
| Close a position | `prepare-close` → show the result to the user → `place-close` |

**Prepare, show, confirm, place.** `place-*` and `execute-write` run only after the user confirms the prepared payload.

## More than one eToro account

The server is tied to the account the user signs in with. To use a second account, the user registers the same URL a second time under another name (for example `etoro-public-api-work`) in their own MCP config and signs in there as the other account. The plugin's `.mcp.json` ships only the first. Not verified end to end: after connecting, check which account each server returns (`get-my-profile-and-scopes`) before any trade, and never mix tools from the two servers in one action. The CLI's profiles (`references/cli.md`) are the better-tested route for several accounts.

If a route is missing, say so; do not fall back to guessing REST paths.

For response schemas outside the connected server, the eToro docs also publish an MCP at `https://api-portal.etoro.com/mcp`.
