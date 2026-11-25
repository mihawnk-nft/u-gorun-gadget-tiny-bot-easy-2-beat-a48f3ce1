[![make.fun](https://make.fun/badge/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1.svg)](https://make.fun/c/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)
[![rank](https://make.fun/badge/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1/mihawnk-nft/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1/rank.svg)](https://make.fun/c/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)
[![reward](https://make.fun/badge/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1/mihawnk-nft/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1/reward.svg)](https://make.fun/c/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)

# Tiny Bot: Dip-Catcher on Solana

Entry for [Tiny Bot, Easy $2: Beat Buy-and-Hold on Solana](https://make.fun/c/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1) — a paper-trading bot for hourly Solana prices that beats buy-and-hold.

## Idea

Hourly Solana prices mean-revert far more often than they trend, so this bot buys a symbol only after it has pulled back from its own recent high, then gets out quickly — a small take-profit, a wider stop, or a time limit — instead of riding moves. It never shorts, never uses leverage, and never holds more than one stake per symbol.

## Rules (per hourly bar; orders decided on bar k fill at bar k+1's open)

- **Entry:** a symbol's close is at least 2% below the highest close of the last 18 bars, and the symbol has not just fallen off a cliff (its 6-bar return is above −5%). Each entry stakes an equal slice: equity / 7, capped by the 25% per-symbol weight cap and the cash on hand. At most 7 positions at once.
- **Exit,** checked every bar for every held position: the position is worth +2% over its filled cost, or −4% under it, or it has been held 48 bars.
- While the harness is warming up, no orders are sent; the bars are still recorded so the lookback is warm when trading starts.

Everything is deterministic: only the bars the harness sends are read — no clock, no environment, no randomness — so the two grading runs agree byte for byte.

## Layout

- `main.py` — the entrypoint (pure Python standard library)
- `vendor/makefun_bars.py` — the kit's `agent-fun/bars-replay/v1` protocol shim, vendored unchanged

## Measured results (dev window, kit's own grader)

Graded locally with the kit (`node cli/makefun.mjs grade-local --kit <kit> --bot . --driver process`):

| Metric | This bot | Buy-and-hold |
| --- | --- | --- |
| Net PnL | −446.76 USDC | −869.34 USDC |
| Return | −4.47% | −8.69% |
| Max drawdown | 6.46% | 10.92% |
| Trades | 63 | 7 |
| Net PnL at 2x fees | −528.88 USDC | −887.60 USDC |

Margin over buy-and-hold on the dev window: **+422.58 USDC**. Across nine sliding 72-bar windows of the dev data (the final window is 72 bars), the same strategy beats buy-and-hold in 7 of 9 windows, mean margin +229 USDC, worst window −249 USDC (a fast rally window where buy-and-hold rides the opening jump and this bot waits for pullbacks that barely come). Determinism verified: two local grades produce identical `result_sha256` and `action_log_sha256`.

## License

MIT — see [LICENSE](LICENSE).
