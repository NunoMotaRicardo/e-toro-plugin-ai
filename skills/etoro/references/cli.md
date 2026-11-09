# CLI route

Install: `npm install -g etoro-cli`, then `etoro setup` (interactive; the user runs it, since it takes the keys).

```
etoro search apple
etoro quote AAPL TSLA
etoro portfolio summary
etoro trade open AAPL --amount 500
etoro shell            # interactive REPL
```

Output is colored tables by default; add `--output json` to any command when you parse results. Check `etoro --help` and `etoro <command> --help` for flags rather than guessing them. `etoro trade ...` is a real execution unless the CLI is configured for demo; confirm per the rules in `SKILL.md`.
