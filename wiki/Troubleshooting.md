# Troubleshooting

| Status | Cause | Fix |
|---|---|---|
| 401 | Missing, wrong or expired key; or key headers sent together with a Bearer token | Check both headers; regenerate the User Key in the portal; run `setup` |
| 403 | The key lacks permission (a Read key on a trading call, or a demo key on real-only history) | Check the key's permissions and environment |
| 403 KYC | Real trading needs identity verification | Complete KYC on etoro.com |
| 413 / 414 | Request too large | Send fewer IDs per call |
| 429 | Rate limit | Wait `Retry-After`, then back off; trade calls also have their own limit of about 20 per minute |

## A trade timed out

Do not resend it. Wait about 10 seconds, check the portfolio's positions and pending orders, and only send again if the trade is not there.

## The MCP tools do not appear

Reload the MCP servers, then sign in at the eToro portal when prompted.
