# Dip-recovery cbBTC bot — make.fun btc-race-24h-20261117-0603

[![make.fun · btc-race-24h-20261117-0603](https://make.fun/badge/btc-race-24h-20261117-0603.svg)](https://make.fun/bounty/btc-race-24h-20261117-0603?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261117-0603)
[![rank](https://make.fun/badge/btc-race-24h-20261117-0603/0xrifai/makefun-btc-race-24h-20261117-0603/rank.svg)](https://make.fun/bounty/btc-race-24h-20261117-0603/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261117-0603)
[![reward](https://make.fun/badge/btc-race-24h-20261117-0603/0xrifai/makefun-btc-race-24h-20261117-0603/reward.svg)](https://make.fun/bounty/btc-race-24h-20261117-0603?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261117-0603)

A bot that trades Coinbase Wrapped BTC (cbBTC) against USDC, minute by
minute, on make.fun's `agent-fun/bars-replay/v1` harness. Paper money only:
it starts with $1,000 USDC and never trades anything real.

## Strategy

Wait flat in cash, buy a sharp washout, sell the recovery:

- Track the trailing 60-bar high of cbBTC closes.
- **Entry** — flat, and the latest close is at least **1.1%** below that
  60-bar high: buy with all available cash (one open position, up to
  100% of equity).
- **Exit** — holding, and any one of: close **2%** above the entry close
  (take profit), **1.2%** below it (stop loss), or **240 bars** held
  (time stop): sell the whole position.
- No fresh entries in the final 120 bars of the window.

Everything is decided in exact integer arithmetic on bar closes, so two
runs on the same data produce byte-identical orders. No clock, no
randomness, no network, no files outside this repository.

## Why these numbers

This round's dev window (2026-11-16 06:03 UTC → 2026-11-17 06:03 UTC)
is flat and range-bound: BTC moves from ~$64,461 to ~$64,776 (+0.49%)
inside a $63,874–$65,467 band, and no 1-minute bar moves even 1%. The
previous round's 2.25% dip trigger never fires here at all. I backtested
the whole family on this dev window (and on the previous round's falling
dev window for robustness): trend following (EMA cross), RSI and
Bollinger mean reversion all lose money here, and shallow dips
(0.5–0.8%) lose too — the ~26 bps round-trip cost eats them. Only dips
of about 1.1% and deeper, held for the full recovery, clear the cost.
Neighbouring parameters (dip 1.0–1.3%, take 2.0%, stop 1.0–1.5%, hold
180–360) all stay profitable on this window, so it is a plateau, not a
knife-edge fit.

## Local grade

Run the kit's local grader (process driver, no Docker needed):

```sh
node <kit>/cli/makefun.mjs grade-local --kit <kit> --bot main.ts --driver process
```

## Layout

- `main.ts` — the strategy (Node 22, standard library only).
- `vendor/makefun-bars.ts` — the kit's bot SDK, copied byte for byte.

## License

MIT — see `LICENSE`.
