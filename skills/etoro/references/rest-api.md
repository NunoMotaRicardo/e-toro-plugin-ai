# REST route

Base URL `https://public-api.etoro.com/api/v1`; every path below is relative to it. Full response schemas: https://api-portal.etoro.com/.

## Auth

Both methods use the same endpoints; only headers differ. Every request also carries `x-request-id: <fresh UUID>`.

- **API keys** (default): `x-api-key` = Public API Key (`ETORO_API_KEY`), `x-user-key` = User Key (`ETORO_USER_KEY`). Each User Key is bound to one environment (Real or Virtual) and one permission level (Read or Write).
- **OAuth SSO** (partner and enterprise integrations; individual developers use keys, and access comes from the eToro partnerships team): `Authorization: Bearer <access_token>`. See the last section.

Send one method, never both: a request carrying a Bearer token and key headers is rejected even when each is valid. Moving from keys to OAuth means dropping the key headers entirely.

## Conventions

- Array query params are comma-separated with a literal comma (`instrumentIds=1001,1002`). `%2C` is rejected, and default URL encoders produce it, so join the values yourself. Affects `instrumentIds`, `cidList`, `usernames`.
- Pagination differs per endpoint: search `pageNumber`/`pageSize`; people search and trade history `page`/`pageSize`; feeds `take`/`offset`; watchlist items `pageNumber`/`itemsPerPage`.
- Trading endpoints containing `/demo/` are paper trading; the others execute for real. The key's environment must match the endpoint.
- Request body casing differs per endpoint, listed below. Response casing differs too and is returned as-is: `instrumentId` from search and trade history, `instrumentID` from instruments and PnL (also `positionID`, `orderID`, `CID`). Read both spellings.
- Errors arrive as `{ "error": "..." }` with an HTTP status. Surface the message to the user.
- Send only the fields the user specified; defaults cover the rest (leverage, stop-loss, take-profit).

## Which environment is a key

Each User Key is bound to one environment, chosen when it was created. To classify an unlabeled key, send `GET /trading/info/real/pnl`: `200` is a real key; `403 InsufficientPermissions` is a demo key. Use `/demo/` paths for demo keys. `/trading/info/trade/history` needs a real key, so a demo key gets `InsufficientPermissions` there: say that, not "auth failed".

## IDs

`instrumentId` from search or metadata · `positionId` and `orderId` from the portfolio endpoints or execution responses · `marketId` for instrument feeds (include it in search `fields`) · `userId` is the numeric CID, found via `/user-info/people` · `watchlistId` from the watchlist endpoints · `subPortfolioId` is a UUID.

## Market data (GET)

- `/market-data/search`: `fields` required (comma-separated; include `instrumentId`, `internalSymbolFull`, `displayname`, and `marketId` for feeds). Optional `searchText`, `pageSize`, `pageNumber`, `sort`. For an exact symbol use `internalSymbolFull=<SYMBOL>` and verify the match.
- `/market-data/instruments`: filters `instrumentIds`, `exchangeIds`, `stocksIndustryIds`, `instrumentTypeIds`.
- `/market-data/instruments/rates`: `instrumentIds` required.
- `/market-data/instruments/history/closing-price`: bulk historical closes.
- `/market-data/instruments/{instrumentId}/history/candles/{direction}/{interval}/{candlesCount}`: `direction` is `asc` or `desc`, `candlesCount` max 1000; confirm supported `interval` values in the portal docs.
- `/market-data/exchanges` (`exchangeIds`), `/market-data/instrument-types`, `/market-data/stocks-industries` (`stocksIndustryIds`).

## Trading execution

Needs Write permission and the matching environment. Paths below are demo; drop `/demo` for real. A real call needs the confirmation in `SKILL.md`.

| Action | Endpoint | Body (PascalCase) |
|---|---|---|
| Open by amount | `POST /trading/execution/demo/market-open-orders/by-amount` | required `InstrumentID`, `IsBuy`, `Leverage`, `Amount` |
| Open by units | `POST .../market-open-orders/by-units` | required `InstrumentID`, `IsBuy`, `Leverage`, `AmountInUnits` |
| Cancel open order | `DELETE .../market-open-orders/{orderId}` | none |
| Close position | `POST .../market-close-orders/positions/{positionId}` | required `InstrumentId` (lowercase d); optional `UnitsToDeduct` |
| Cancel close order | `DELETE .../market-close-orders/{orderId}` | none |
| Limit (market-if-touched) | `POST .../limit-orders` | required `InstrumentID`, `IsBuy`, `Leverage`, `Rate`, and one of `Amount` or `AmountInUnits` |
| Cancel limit order | `DELETE .../limit-orders/{orderId}` | none |

- Open and limit orders accept optional `StopLossRate`, `TakeProfitRate`, `IsTslEnabled`, `IsNoStopLoss`, `IsNoTakeProfit`.
- Close by `positionId`, never by symbol. Full close: `UnitsToDeduct: null`. Partial close: the number of units.
- Limit orders: never send `IsDiscounted` or `CID`. `Rate` is the trigger price and must be better than the market (lower for a buy, higher for a sell). Reported behaviour, verify on demo before relying on it: `StopLossRate` and `TakeProfitRate` may need to be above 0 even with `IsNoStopLoss` or `IsNoTakeProfit` set.
- `Leverage` is a required field: send `1` unless the user asked for leverage, and never infer leverage from the instrument type. Never invent a stop-loss or take-profit; omit them unless the user gave one.

## Portfolio and history (GET)

`/trading/info/demo/pnl` · `/trading/info/real/pnl` · `/trading/info/demo/portfolio` · `/trading/info/portfolio` (use these to find `positionId` and `orderId`; the response is `{ clientPortfolio }` with `credit`, `positions[]`, `mirrors[]`, `orders[]`, `ordersForOpen[]`) · `/trading/info/trade/history` (`minDate` required as YYYY-MM-DD; optional `page`, `pageSize`).

Live PnL per position sits at `position.unrealizedPnL?.pnL` (lowercase `n`, capital `L`); closed positions may lack it, so use optional chaining. On `clientPortfolio`, `unrealizedPnL` is a flat number.

## Watchlists

- `GET /watchlists` (optional `itemsPerPageForSingle`, `ensureBuiltinWatchlists`, `addRelatedAssets`), `GET /watchlists/{id}` (`pageNumber`, `itemsPerPage`).
- `POST /watchlists` (query `name` required, `type`, `dynamicQuery`), `PUT /watchlists/{id}` (query `newName`), `DELETE /watchlists/{id}`. These take query params, not a body.
- Items: `POST|PUT|DELETE /watchlists/{id}/items`, body a JSON array of `{ "ItemId": int, "ItemType": "Instrument" | "Person", "ItemRank": int (optional) }`.
- Defaults: `POST /watchlists/default-watchlist/selected-items`, `GET /watchlists/default-watchlists/items` (`itemsLimit`, `itemsPerPage`), `POST /watchlists/newasdefault-watchlist` (query `name`, `type`, `dynamicQuery`), `PUT /watchlists/setUserSelectedUserDefault/{id}`, `PUT /watchlists/rank/{id}` (query `newRank`).
- Public: `GET /watchlists/public/{userId}` and `/{watchlistId}`.

## Feeds, lists, investors

- `GET /feeds/instrument/{marketId}` and `GET /feeds/user/{userId}`: optional `requesterUserId`, `take`, `offset`, `badgesExperimentIsEnabled`, `reactionsPageSize`.
- `POST /feeds/post`: lowerCamel body, `{ "message": "..." }` minimum; also `owner` (int), `tags` (`{ "tags": [{ "name", "id" }] }`), `mentions` (`{ "mentions": [{ "userName", "id", "isDirect" }] }`), `attachments` (objects with `url`, `title`, `host`, `description`, `mediaType`, optional `media`). A post is public: confirm the text with the user first.
- `GET /curated-lists` · `GET /market-recommendations/{itemsCount}` · `GET /pi-data/copiers`

## User info (GET)

- `/user-info/people`: `usernames` or `cidList`; maps username to CID.
- `/user-info/people/search`: `period` required; optional `page`, `pageSize`, `sort`, `popularInvestor`, `gainMax`, risk-score min/max filters, `weeksSinceRegistrationMin`, `countryId`, `instrumentId`, `instrumentPctMin`/`Max`, `isTestAccount`.
- `/user-info/people/{username}/gain` · `.../daily-gain` (`minDate`, `maxDate`, `type` = `Daily` or `Period`) · `.../portfolio/live` · `.../tradeinfo` (`period`, e.g. `LastTwoYears`).

## Agent portfolios (sub-portfolios)

Dedicated sub-accounts with their own virtual balance, traded via copy-trading. `investmentAmountInUsd` comes out of the user's balance and positions mirror proportionally ($2k on a $10k virtual balance = 20% sizing).

- `GET /sub-portfolios` · `POST /sub-portfolios` (required `investmentAmountInUsd`, `subPortfolioName` of 6 to 10 characters, `userTokenName`, `scopeIds`; optional `subPortfolioDescription`, `ipsWhitelist`, `expiresAt`).
- `DELETE /sub-portfolios/{id}` is permanent: it revokes tokens and stops mirroring. Confirm first.
- `POST /sub-portfolios/{id}/user-tokens` (required `userTokenName`, `scopeIds`; optional `ipsWhitelist`, `expiresAt`) · `PATCH .../user-tokens/{userTokenId}` (at least one of `scopeIds`, `ipsWhitelist`, `expiresAt`) · `DELETE .../user-tokens/{userTokenId}`.
- Scope IDs: 200 real:read, 201 demo:read, 202 real:write, 203 demo:write. The `userToken` secret is returned only at creation.

## Errors and rate limits

| Status | Cause | Action |
|---|---|---|
| 401 | `x-api-key` missing or wrong, or the User Key is wrong or expired; or key headers sent alongside a Bearer token | Check the headers; regenerate the User Key in the portal; run the `setup` skill |
| 403 | The key lacks permission for this endpoint (for example a Read key on a trading call, or a demo key on real-only history) | Check the key's permissions and environment; do not retry |
| 403 KYC | Real trading needs identity verification | Tell the user to complete KYC on etoro.com |
| 413 / 414 | Request too large (many IDs in one call) | Halve the batch and retry |
| 429 | Rate limit; the response carries `Retry-After` | Wait `Retry-After`, else back off exponentially with jitter, at most 3 retries; same payload size |
| 5xx | Server error | Short exponential backoff, then retry (reads only) |

**Trade limit.** Execution endpoints (`market-open-orders`, `market-close-orders`, `limit-orders`) also allow about 20 requests per minute, separate from the general limit. Space trade calls at least 3 seconds apart; on a 429 there, wait 15s, then 30s, then 60s, at most 3 retries.

## Trade delivery: at-most-once

There is no idempotency key, so a resent trade can open a duplicate position. A missing position is recoverable; a duplicate is hard to unwind.

| Outcome | Meaning | Action |
|---|---|---|
| 2xx with `orderId` | Placed | Do not resend; verify |
| 4xx except 429 | Nothing placed | Show the error; never resend the same payload |
| 429 | Not processed | Safe to retry after backoff |
| 5xx with a body | Processed and failed | Show the error; do not retry |
| Timeout, reset, no response, unparseable | **Ambiguous** | Do not retry; reconcile |

**Reconcile an ambiguous trade.** Wait about 10 seconds, read the PnL endpoint, and look for the trade in `positions[]` and `ordersForOpen[]`. Found: it landed, so report it and place nothing else. Not found: it did not land, and only then ask the user whether to send it again as a new trade. In a multi-trade run, keep a status per trade (`ok`, `failed`, `ambiguous`), read the PnL once at the end, and settle the ambiguous ones from what landed.

## OAuth SSO

1. Send the user to `GET https://www.etoro.com/sso` with `response_type=code`, `client_id`, `redirect_uri`, `scope`, `state` (optional PKCE `code_challenge` and `code_challenge_method=S256`).
2. eToro redirects back to `redirect_uri` with `code` and `state`.
3. `POST https://www.etoro.com/api/sso/v1/token`, `application/x-www-form-urlencoded`, credentials in the body (`client_secret_post`, never a Basic header): `grant_type=authorization_code`, `code`, `client_id`, `client_secret`, `redirect_uri` (exactly the registered one), and `code_verifier` only if PKCE was used.
4. The response holds `access_token` (the Bearer token), `id_token`, `refresh_token`, `expires_in`.

`/api/sso/v1/userinfo`, the JWKS (`https://public-api.etoro.com/.well-known/jwks.json`), PKCE and reading the `id_token` are optional.
