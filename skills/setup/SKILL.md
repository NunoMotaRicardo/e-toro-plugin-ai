---
name: setup
description: >
  First-time eToro setup: get an API key, store it safely, connect the MCP server or CLI,
  and verify with a demo read. Use when the user has no eToro credentials, an eToro call
  returns 401 or 403, or they ask to set up, connect or configure eToro.
---

# eToro setup

Walk the user through one step at a time. Each step ends when its check passes.

The agent never sees key values: the user types them into their own terminal or `.env`, and you test presence only.

## 1. Pick the surface

Ask which they want, recommending **MCP** (nothing to install, browser sign-in):

- **MCP**: tools in the agent. Server `https://mcp.public-api.etoro.com`, name `etoro-public-api`. This plugin ships it in `.mcp.json`; other tools add it by URL.
- **CLI**: `npm install -g etoro-cli`, then the user runs `etoro setup`.
- **REST**: scripts and apps, using the keys below.

Done when the user has chosen. The key steps apply to CLI and REST, and are worth doing for MCP too.

## 2. Get the API key

Tell the user to:

1. Log in at https://api-portal.etoro.com (eToro login).
2. Open **Settings → Trading**, then **API Key Management**, and click **Create New Key**.
3. Choose **Environment: Virtual (demo)** and **Permissions: Read**. Choose **Write** only when the user wants the agent to trade.
4. Complete the identity verification the portal asks for.
5. Copy **both** values straight away: the **Public API Key** and the **User Key**. The User Key is shown once.

Each key is bound to one environment and one permission level. Create a demo Read pair first; real trading needs its own separate key pair, so leave it until the user asks.

Done when the user says both keys are copied.

## 3. Store the keys

The user adds these to a `.env` file (or their shell profile), pasted by them, not by you:

```
ETORO_API_KEY=<public api key>
ETORO_USER_KEY=<user key>
ETORO_ENVIRONMENT=demo
```

Check `.gitignore` lists `.env`; if not, offer to add it. Treat the User Key like a password: anyone holding both keys can trade as the user. Use a separate key pair per environment and per developer or service, rotate every 90 days, and delete the old key in the portal after rotating. For production, use a secrets manager instead of `.env`.

Done when `[ -n "$ETORO_API_KEY" ] && [ -n "$ETORO_USER_KEY" ]` succeeds in the user's shell, with no value printed.

## 4. Connect

- **MCP**: when tools prompt, the user signs in at `api-portal.etoro.com`; reload MCP servers if no tools appear.
- **CLI**: the user runs `etoro setup` and enters the keys there.
- **REST**: nothing more.

## 5. Verify

Run one **read** against demo: the MCP `get-my-portfolio-summary` tool, `etoro portfolio summary`, or `GET /trading/info/demo/portfolio` (see `skills/etoro/references/rest-api.md`).

Then confirm the key is demo: `GET /trading/info/real/pnl` must return `403 InsufficientPermissions` (a `200` means a real key, so stop and tell the user).

Done when the demo read returns data and the key is confirmed demo. On **401**, a key is wrong, missing or expired: check both keys, and regenerate the User Key in the portal if needed. On **403**, the key lacks permission for that endpoint (check its permissions) or real trading needs identity verification (KYC on etoro.com). On **429**, wait and retry. Report which, and stop.

Finish by telling the user the plugin is demo-only until they choose otherwise, and that the `etoro` skill is ready.
