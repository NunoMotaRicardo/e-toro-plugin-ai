# CLI route

Uses [`etoro-cli`](https://github.com/NunoMotaRicardo/etoro-cli), a Go CLI with compact output (fewer tokens than raw API responses). It is also published as its own plugin, `etoro-cli`, with a skill of its own.

Install: `go install github.com/NunoMotaRicardo/etoro-cli/cmd/etoro-cli@latest` or a release binary. Then the user runs `etoro-cli connect` in their own terminal: it walks through creating the key pair in the portal and stores the keys. Keys never enter the chat.

```
etoro-cli search apple
etoro-cli quote AAPL TSLA
etoro-cli portfolio
etoro-cli trade open AAPL --buy --amount 500          # demo; prints the summary, exits 4 until --yes
etoro-cli api GET /market-data/instruments -q instrumentIds=1001,1002
```

Defaults to demo; `--real` needs a stored real key pair. State-changing commands need `--yes`, which you add only after the user confirms (rules in `SKILL.md`). Exit codes: 3 = not connected (run `setup` or `etoro-cli connect`), 4 = confirmation required, 5 = ambiguous trade outcome (never resend; check `etoro-cli portfolio`). Add `--json` only when parsing. Check `etoro-cli --help` rather than guessing flags.
