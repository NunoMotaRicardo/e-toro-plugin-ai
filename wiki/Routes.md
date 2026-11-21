# Routes: MCP, CLI and REST

## MCP

Server `https://mcp.public-api.etoro.com`, registered by the plugin as `etoro-public-api`. Authentication happens in the browser at the eToro portal when the tools first prompt.

Reads use the `get-my-*` tools, `get-instruments-overview` and `get-trader-profile-summary`; any other route goes through `get-all-routes`, `get-route-spec` and `execute-read` / `execute-write`. Trades are **prepare, show, confirm, place**: `prepare-trade` then `place-trade`, `prepare-close` then `place-close`.

## CLI

```bash
go install github.com/NunoMotaRicardo/etoro-cli/cmd/etoro-cli@latest
etoro-cli connect
etoro-cli quote AAPL TSLA
etoro-cli portfolio --json
```

Every command accepts `--json`. The agent confirms each `etoro-cli trade ...` before running it.

### More than one account

Each eToro account is a CLI **profile** with its own demo and real keys: `etoro-cli --profile work connect`, then `--profile work` (or `ETORO_PROFILE=work`) on every command. Once two profiles exist the CLI refuses to run without one, so a trade cannot silently go to the wrong account; see the [etoro-cli Profiles page](https://github.com/NunoMotaRicardo/etoro-cli/wiki/Profiles). For MCP, register the server a second time under another name and sign in as the other account (not verified end to end; check which account each server returns before trading).

## REST

Base URL `https://public-api.etoro.com/api/v1`. Headers: `x-request-id` (a fresh UUID), plus `x-api-key` and `x-user-key`, or a Bearer token, never both. Highlights:

- Comma-separated list parameters use literal commas.
- Request body casing differs per endpoint (PascalCase for orders, `InstrumentId` for market close, lowerCamel for feed posts).
- Close positions by `positionId`; full close sends `UnitsToDeduct: null`.

The full endpoint list is in `skills/etoro/references/rest-api.md`.
