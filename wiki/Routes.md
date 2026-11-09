# Routes: MCP, CLI and REST

## MCP

Server `https://mcp.public-api.etoro.com`, registered by the plugin as `etoro-public-api`. Authentication happens in the browser at the eToro portal when the tools first prompt.

Reads use the `get-my-*` tools, `get-instruments-overview` and `get-trader-profile-summary`; any other route goes through `get-all-routes`, `get-route-spec` and `execute-read` / `execute-write`. Trades are **prepare, show, confirm, place**: `prepare-trade` then `place-trade`, `prepare-close` then `place-close`.

## CLI

```bash
npm install -g etoro-cli
etoro setup
etoro quote AAPL TSLA
etoro portfolio summary --output json
```

Every command accepts `--output json`. The agent confirms each `etoro trade ...` before running it.

## REST

Base URL `https://public-api.etoro.com/api/v1`. Headers: `x-request-id` (a fresh UUID), plus `x-api-key` and `x-user-key`, or a Bearer token, never both. Highlights:

- Comma-separated list parameters use literal commas.
- Request body casing differs per endpoint (PascalCase for orders, `InstrumentId` for market close, lowerCamel for feed posts).
- Close positions by `positionId`; full close sends `UnitsToDeduct: null`.

The full endpoint list is in `skills/etoro/references/rest-api.md`.
