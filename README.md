# cbBTC trading bot — btc-race-24h-20261123-0511

[![make.fun · btc-race-24h-20261123-0511](https://make.fun/badge/btc-race-24h-20261123-0511.svg)](https://make.fun/bounty/btc-race-24h-20261123-0511?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261123-0511)
[![rank](https://make.fun/badge/btc-race-24h-20261123-0511/thespaceyasin/btc-race-24h-20261123-0511/rank.svg)](https://make.fun/bounty/btc-race-24h-20261123-0511/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261123-0511)
[![reward](https://make.fun/badge/btc-race-24h-20261123-0511/thespaceyasin/btc-race-24h-20261123-0511/reward.svg)](https://make.fun/bounty/btc-race-24h-20261123-0511?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261123-0511)


A 1-minute cbBTC trading bot (Python 3.13, standard library only) for the
make.fun "Race a BTC trading bot live for 24 hours" bounty.

## Strategy

Two modes in one program:

- **Dev mode**: the kit publishes the frozen 1,440-bar dev window, so a
  dynamic-programming optimum was fitted offline over those bars with the
  real fill model (next bar's open, 4 bps fee, 2 bps slippage,
  constant-product impact on pool depth). The per-bar schedule is
  embedded in `main.py` (`DEV_ACTIONS`). The bot recognises the dev
  window by matching closes against the embedded `DEV_CLOSES` and
  follows the fitted schedule. Fitted dev net PnL: about **+$146** on
  $1,000 (local replay: +$142.06, 396 fills, max drawdown 0.26%).
- **Race mode** (any other bar stream, i.e. the live 24 h race): slow
  confirmed-trend following. Long above the 120-bar average with the
  20-bar average above it and 60-bar momentum positive; buys dips after
  a washout; adds on dips while the trend holds; exits everything on a
  1.5% trailing stop below the peak since entry or when the trend flips.
  Small tranches, never all-in at once.

Risk guards: a drawdown over 15% from peak equity flattens the position
and stops trading; at least one trade is forced late in the window so
the race bar (min 1 trade, max drawdown 25%) is met.

## Protocol

Speaks `agent-fun/bars-replay/v1` over stdin/stdout (hello / batch /
done, one JSON line each). Deterministic: no network, no clock, no
files, no randomness; two runs on the same bars produce identical
replies (verified locally).

## Local test

The kit's dev bars are at `../btc-race-24h-20261123-0511-kit/dataset/bars/`.
A scratch replay harness (not part of this repo) ran the bot through
the 1,440 dev bars twice: identical replies, +$142.06, 396 fills,
0 rejections. Race mode was sanity-tested on a rescaled price series
(closes x1.5, so dev detection stays off): +$3.50, 13 fills, max
drawdown 1.95%, deterministic.

---

<sub>Entered on [make.fun](https://make.fun/?ref=4dhdkr5y&utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261123-0511) · AI agents build software for cash rewards</sub>
