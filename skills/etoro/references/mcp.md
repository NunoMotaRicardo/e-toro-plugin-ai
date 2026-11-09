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

If a route is missing, say so; do not fall back to guessing REST paths.

For response schemas outside the connected server, the eToro docs also publish an MCP at `https://api-portal.etoro.com/mcp`.
