# Getting started

## Install

```bash
claude plugin marketplace add NunoMotaRicardo/nmr-plugins
claude plugin install etoro@nmr-plugins
```

Other tools: install from the repo URL (Gemini CLI, Antigravity) or add the `nmr-plugins` marketplace (Codex, Copilot CLI, Cursor).

## Run the `setup` skill

It guides you one step at a time. You never paste keys into the chat.

1. **Pick a route.** MCP is recommended: nothing to install, browser sign-in. The CLI (`npm install -g etoro-cli`, then `etoro setup`) and REST use API keys.
2. **Create an API key.** Log in at [api-portal.etoro.com](https://api-portal.etoro.com), open **Settings → Trading → API Key Management**, click **Create New Key**, choose **Virtual (demo)** and **Read**, complete the identity check, and copy both the **Public API Key** and the **User Key**. The User Key is shown once.
3. **Store the keys** in a gitignored `.env` or your shell profile:
   ```
   ETORO_API_KEY=<public api key>
   ETORO_USER_KEY=<user key>
   ETORO_ENVIRONMENT=demo
   ```
4. **Connect.** MCP: sign in when the tools prompt. CLI: run `etoro setup`.
5. **Verify.** One demo read, plus a check that the key is a demo key (`GET /trading/info/real/pnl` returns `403`).

Choose **Write** permission only when you want the agent to trade. Real trading needs identity verification (KYC) and its own key pair; the plugin stays demo-only until you ask.
