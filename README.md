# e-toro-plugin-ai

An AI plugin for [eToro](https://www.etoro.com/), meant to let coding and chat agents work with the eToro Public API.

> **Status: early (0.1.0).** Demo-first by design. Real-money trading needs an explicit request and a confirmation for every execution.

It is part of my [plugin marketplace](https://github.com/NunoMotaRicardo/nmr-plugins). I build it for myself, but it is usable by anyone and contributions are welcome.

## What is in it

| Skill | Purpose |
|---|---|
| `etoro` | Market data, portfolio and PnL, watchlists, feeds, popular investors and trading. Uses the eToro MCP server when connected, else the `etoro` CLI, else the REST API. |
| `setup` | First-time guide: create an API key in the eToro portal, store it safely, connect, and verify with a demo read. |

The plugin also registers the eToro MCP server (`https://mcp.public-api.etoro.com`).

## Install (Claude Code)

```bash
claude plugin marketplace add NunoMotaRicardo/nmr-plugins
claude plugin install etoro@nmr-plugins
```

Then run the `setup` skill: it walks you through creating an API key in the eToro portal and verifying it with a demo read. Other tools: install from this repo's URL.

## Safety

Demo-first. Every real execution is confirmed with you first, trades are sent at most once, and keys never enter the chat. See [Safety rules](https://github.com/NunoMotaRicardo/e-toro-plugin-ai/wiki/Safety-rules).

## Principles

- Anything that moves money (trades, orders, transfers) defaults to the demo account and needs explicit confirmation each time.
- No API keys, tokens or account identifiers in the repo.

## Contributing

Open an issue to discuss ideas or bugs; see [`AGENTS.md`](AGENTS.md) for the conventions. More documentation is on the [wiki](https://github.com/NunoMotaRicardo/e-toro-plugin-ai/wiki).

## License

[MIT](LICENSE)
