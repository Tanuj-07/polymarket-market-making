# prediction-market-making

This is a market-making bot that trades on Polymarket US with real money. It has been running since September 5, 2026, and it uses Kalshi's prices for the same markets as its fair value.

The repository has no code, only the results. **They update automatically every day at noon ET**, once the previous day's markets have settled, so everything here is current through yesterday.

Last update: October 15, 2026, 12:01 PM ET (40 days, September 5 to October 14).

## Returns

| | |
|---|---|
| Total return on capital in use | **+222.2%** (+$76.46 on an average of $34.41 in use) |
| Average daily return | **+5.6%** (net per day over capital in use per day, all 40 days) |
| Last 7 days | **+5.1% per day** (+$14.07 total, on $39 in use) |
| Latest version with a full day of data (v14.4) | **−1.5% per day** over 1.8 days, Oct 12 to Oct 14 |
| Version running now | v14.5, since Oct 14, 11:00 AM ET (its results appear here once it has a full settled day) |
| Days up | 28 of 40 (70.0%) |
| Days up, trading only | 22 of 40 (55.0%) |
| Best / worst day | +$9.05 (+20.7%) on Oct 7 / −$10.62 (−20.7%) on Sep 25 |
| Worst drawdown | −$10.62 (−20.7% of the capital in use at the time) |

![Cumulative net P&L](charts/cumulative.svg)

Where it came from:

| | |
|---|---|
| Trading | +$17.12 |
| Maker rebates | +$47.98 |
| Liquidity rewards (now rare) | +$11.36 |
| Fills / contracts | 22,256 / 35,148 |
| Capital the bot can use | $60 now (earlier: $20 from Sep 5 to Sep 14, $40 from Sep 15 to Sep 21) |
| Capital in use | $37 on average under v14.4, $34 over all 40 days |

![Capital: the cap and what the bot used](charts/capital.svg)

## About that return

The percentage return looks suspiciously high, and it is real, but it doesn't scale with size. Every so often a much better-informed buyer shows up. They already know which bracket is mispriced, and they buy a huge number of contracts at the stale price, mostly out of market makers' resting orders. The bigger those orders are, the bigger the hit, and one of those days can wipe out a lot of normal ones.

That is why the size stays small. I think the strategy is already close to its ceiling. Small improvements can still add something, but probably not much. I can't be sure of that, though. It's just how things look right now.

## How it works

The bot keeps buy and sell orders resting on both sides of every bracket. It makes money from the spread when both sides fill and from the maker rebate the exchange pays on qualifying fills. Early on it also earned the exchange's liquidity rewards, but Polymarket cut that reward pool on Sep 23 and the bot has rarely qualified since (see the timeline). Kalshi lists the same contracts with deeper books, so the bot takes its fair value from Kalshi instead of predicting anything itself.

Most of the losses come from getting picked off by traders who know something before the market does, and much of the version history is about limiting that.

On the risk side, each market has an inventory limit and the bot has a hard cap on capital. Quotes are pulled when Kalshi moves, a dead-man switch cancels everything if the bot stalls, and a soft stop winds positions down after a bad run. A watchdog checks on all of it every few minutes.

Every change ships as a new version. Before a version goes live, I write down what it should change and how I'll measure it, and each change to the trading logic runs for at least 48 hours before the next one.

## Timeline

Outside events that changed the economics. They are the purple lines on the charts.

| Date (ET) | What happened |
|---|---|
| Sep 22 | Switched to collecting Polymarket's maker rebate on qualifying fills. |
| Sep 23 | Polymarket cut the liquidity reward pool for these markets by 75%. The bot has rarely qualified since. |
| Oct 9 | Started testing a way to qualify for liquidity rewards again, beginning with tiny orders. |

## Charts

![Daily P&L](charts/daily.svg)

![Where the money came from](charts/sources.svg)

![Contracts traded per day](charts/volume.svg)

## Versions

| Version | Started (ET) | What changed | Days | Net | Net/day | In use | Return/day | Trading | Rebates | Contracts |
|---|---|---|---:|---:|---:|---:|---:|---:|---:|---:|
| v00 | Sep 5, 11:34 AM | First version: a $20 test run that quotes both sides of every bracket off Kalshi prices. | 5.6 | +$7.13 | +$1.27 | $13 | +9.7% | −$0.48 | $0.00 | 1,709 |
| v01 | Sep 11, 2:33 AM | Joins the queue at the best price and goes after the liquidity rewards. | 1.8 | +$4.56 | +$2.55 | $15 | +17.4% | +$4.56 | $0.00 | 1,200 |
| v02 | Sep 12, 9:28 PM | Detects exchange halts and settled markets, and reports its safety limits to the monitor. | 0.2 | +$0.55 | too short | $17 | too short | +$0.55 | $0.00 | 91 |
| v03 | Sep 13, 1:39 AM | Waits for fill confirmation before taking on more risk (added after an exchange data outage). | 1.8 | +$5.35 | +$3.03 | $15 | +19.7% | +$5.35 | $0.00 | 863 |
| v04 | Sep 14, 8:03 PM | Combines the fixes above into a single build. | 0.3 | −$5.58 | too short | $18 | too short | −$5.58 | $0.00 | 87 |
| v05 | Sep 15, 4:04 AM | Raises the cap to $40 to cover more markets at the same order size. | 0.5 | −$0.72 | too short | $27 | too short | −$0.72 | $0.00 | 504 |
| v06 | Sep 15, 3:48 PM | Pulls resting quotes as soon as Kalshi moves. | 6.1 | +$8.91 | +$1.46 | $28 | +5.2% | +$7.65 | $0.00 | 3,961 |
| v07 | Sep 21, 6:08 PM | Restarts the bot as a long-running service, with no logic changes. | 0.7 | −$2.79 | too short | $33 | too short | −$2.79 | $0.00 | 359 |
| v08 | Sep 22, 10:11 AM | Uses larger orders and raises the cap to $60. | 1.7 | +$11.08 | +$6.34 | $52 | +12.2% | +$4.63 | $3.96 | 2,440 |
| v09 | Sep 24, 4:07 AM | Adds a soft stop that winds positions down after a losing stretch. | 5.2 | +$0.78 | +$0.15 | $53 | +0.3% | −$8.99 | $9.77 | 5,569 |
| v10 | Sep 29, 8:51 AM | Uses larger orders again. | 2.1 | +$3.14 | +$1.52 | $53 | +2.9% | −$2.62 | $5.76 | 2,799 |
| v11 | Oct 1, 10:16 AM | Drops an outside model input, so fair value comes from Kalshi alone. | 1.0 | +$5.53 | +$5.71 | $55 | +10.4% | +$3.08 | $2.45 | 1,328 |
| v12 | Oct 2, 9:32 AM | Quotes only same-day markets, so nothing is held overnight. | 2.4 | +$18.51 | +$7.73 | $42 | +18.3% | +$11.37 | $7.14 | 3,605 |
| v12.1 | Oct 4, 7:00 PM | Adds a small safety fix for wind-downs. | 4.7 | +$13.68 | +$2.92 | $41 | +7.2% | +$5.07 | $8.61 | 4,870 |
| v14 | Oct 9, 11:16 AM | Fixes a few small bugs, reads prices faster, and starts a small test of a way back into the liquidity rewards. | 0.1 | −$5.07 | too short | $52 | too short | −$5.55 | $0.48 | 201 |
| v14.1 | Oct 9, 12:40 PM | Changes only when the rewards test pulls its orders. | 2.1 | +$5.34 | +$2.59 | $41 | +6.2% | +$1.44 | $3.90 | 2,097 |
| v14.2 | Oct 11, 2:13 PM | Adds safety rules to the rewards test. | 1.0 | +$7.19 | +$7.07 | $45 | +15.8% | +$5.31 | $1.88 | 1,160 |
| v14.3 | Oct 12, 2:37 PM | Runs the rewards test on one market at a time. | 0.0 | +$0.39 | too short | $34 | too short | +$0.10 | $0.29 | 126 |
| v14.4 | Oct 12, 3:13 PM | Keeps the rewards test's orders in place without gaps. | 1.8 | −$1.01 | −$0.56 | $37 | −1.5% | −$3.81 | $2.80 | 1,666 |
| v14.5 | Oct 14, 11:00 AM | Goes back to the v12 trading rules (the v14 trading changes measured worse) and runs the rewards test at full size overnight. | 0.5 | −$0.51 | too short | $31 | too short | −$1.45 | $0.94 | 511 |

Versions that ran for less than about 18 hours say "too short" instead of per-day figures and aren't in the chart, since stretching a few hours into a full day isn't meaningful. Return per day is measured against the average capital the bot had in use while that version ran.

![Net P&L per day by version](charts/versions.svg)

## By market series

Each series is one recurring daily market with a few brackets each day. I've left out which markets they are on purpose.

| Series | Fills | Contracts | Trading | Rebates | Rewards | Net |
|---|---:|---:|---:|---:|---:|---:|
| A | 4,628 | 7,577 | +$4.15 | $10.71 | $0.64 | +$15.50 |
| B | 4,086 | 6,153 | −$0.86 | $8.93 | $0.90 | +$8.97 |
| C | 3,912 | 6,170 | −$5.38 | $8.93 | $2.09 | +$5.64 |
| D | 5,524 | 8,859 | +$30.38 | $11.02 | $0.74 | +$42.14 |
| E | 4,106 | 6,388 | −$11.18 | $8.39 | $6.99 | +$4.20 |

![Net P&L by market series](charts/series.svg)

## Daily log

| Date | Version | Fills | Contracts | Trading | Rebates | Rewards | Net | In use | Return | Total |
|---|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 2026-10-14 | v14.5 | 407 | 816 | +$3.25 | $1.49 | $0.00 | +$4.74 | $35.81 | +13.2% | +$76.46 |
| 2026-10-13 | v14.4 | 460 | 893 | −$6.01 | $1.42 | $0.00 | −$4.59 | $38.14 | −12.0% | +$71.71 |
| 2026-10-12 | v14.2 | 653 | 1,322 | +$2.86 | $2.58 | $0.00 | +$5.44 | $43.30 | +12.6% | +$76.30 |
| 2026-10-11 | v14.1 | 495 | 1,056 | −$1.01 | $1.66 | $0.00 | +$0.65 | $42.53 | +1.5% | +$70.86 |
| 2026-10-10 | v14.1 | 396 | 847 | +$1.19 | $1.58 | $0.00 | +$2.77 | $39.96 | +6.9% | +$70.22 |
| 2026-10-09 | v14 | 640 | 1,245 | −$1.49 | $2.37 | $0.00 | +$0.88 | $40.31 | +2.2% | +$67.45 |
| 2026-10-08 | v12.1 | 543 | 1,139 | +$2.54 | $1.64 | $0.00 | +$4.18 | $35.70 | +11.7% | +$66.57 |
| 2026-10-07 | v12.1 | 543 | 1,179 | +$6.60 | $2.45 | $0.00 | +$9.05 | $43.75 | +20.7% | +$62.39 |
| 2026-10-06 | v12.1 | 448 | 1,022 | −$6.58 | $1.73 | $0.00 | −$4.85 | $40.78 | −11.9% | +$53.34 |
| 2026-10-05 | v12.1 | 466 | 1,005 | −$1.10 | $1.75 | $0.00 | +$0.65 | $43.17 | +1.5% | +$58.19 |
| 2026-10-04 | v12 | 587 | 1,271 | +$4.32 | $2.83 | $0.00 | +$7.15 | $38.82 | +18.4% | +$57.53 |
| 2026-10-03 | v12 | 490 | 1,150 | +$2.92 | $1.96 | $0.00 | +$4.88 | $44.36 | +11.0% | +$50.38 |
| 2026-10-02 | v12 | 755 | 1,648 | +$1.32 | $3.16 | $0.00 | +$4.48 | $45.57 | +9.8% | +$45.50 |
| 2026-10-01 | v11 | 570 | 1,395 | +$4.97 | $2.56 | $0.00 | +$7.53 | $50.44 | +14.9% | +$41.03 |
| 2026-09-30 | v10 | 476 | 1,126 | +$2.70 | $2.31 | $0.00 | +$5.01 | $55.01 | +9.1% | +$33.50 |
| 2026-09-29 | v10 | 691 | 1,553 | −$8.10 | $3.16 | $0.00 | −$4.94 | $55.17 | −9.0% | +$28.48 |
| 2026-09-28 | v09 | 636 | 1,144 | −$3.90 | $1.98 | $0.00 | −$1.92 | $54.39 | −3.5% | +$33.43 |
| 2026-09-27 | v09 | 599 | 1,060 | +$5.71 | $1.87 | $0.00 | +$7.58 | $53.44 | +14.2% | +$35.34 |
| 2026-09-26 | v09 | 651 | 1,167 | +$5.45 | $2.18 | $0.00 | +$7.63 | $54.16 | +14.1% | +$27.76 |
| 2026-09-25 | v09 | 546 | 978 | −$12.23 | $1.61 | $0.00 | −$10.62 | $51.33 | −20.7% | +$20.13 |
| 2026-09-24 | v09 | 590 | 1,042 | −$0.21 | $1.84 | $0.00 | +$1.63 | $50.26 | +3.2% | +$30.76 |
| 2026-09-23 | v08 | 900 | 1,474 | −$0.31 | $2.25 | $2.49 | +$4.43 | $52.32 | +8.5% | +$29.13 |
| 2026-09-22 | v08 | 775 | 1,092 | +$4.45 | $1.60 | $0.00 | +$6.05 | $43.32 | +14.0% | +$24.70 |
| 2026-09-21 | v06 | 840 | 815 | −$7.80 | $0.00 | $0.00 | −$7.80 | $33.28 | −23.4% | +$18.65 |
| 2026-09-20 | v06 | 622 | 604 | +$1.11 | $0.00 | $0.00 | +$1.11 | $29.75 | +3.7% | +$26.46 |
| 2026-09-19 | v06 | 731 | 715 | −$0.29 | $0.00 | $0.00 | −$0.29 | $29.24 | −1.0% | +$25.34 |
| 2026-09-18 | v06 | 262 | 254 | +$2.81 | $0.00 | $0.00 | +$2.81 | $20.91 | +13.5% | +$25.63 |
| 2026-09-17 | v06 | 834 | 812 | +$7.44 | $0.00 | $1.26 | +$8.70 | $23.64 | +36.8% | +$22.82 |
| 2026-09-16 | v06 | 696 | 681 | +$2.40 | $0.00 | $0.00 | +$2.40 | $30.77 | +7.8% | +$14.12 |
| 2026-09-15 | v05 | 806 | 752 | −$5.02 | $0.00 | $0.00 | −$5.02 | $28.32 | −17.7% | +$11.72 |
| 2026-09-14 | v03 | 506 | 460 | +$6.10 | $0.00 | $0.00 | +$6.10 | $14.97 | +40.7% | +$16.74 |
| 2026-09-13 | v03 | 496 | 475 | −$1.58 | $0.00 | $0.00 | −$1.58 | $16.19 | −9.7% | +$10.64 |
| 2026-09-12 | v01 | 726 | 683 | +$1.17 | $0.00 | $0.00 | +$1.17 | $15.22 | +7.7% | +$12.22 |
| 2026-09-11 | v01 | 658 | 617 | +$2.83 | $0.00 | $0.00 | +$2.83 | $14.51 | +19.5% | +$11.05 |
| 2026-09-10 | v00 | 737 | 689 | −$1.94 | $0.00 | $1.76 | −$0.18 | $15.76 | −1.2% | +$8.22 |
| 2026-09-09 | v00 | 503 | 463 | +$2.62 | $0.00 | $1.54 | +$4.16 | $14.33 | +29.0% | +$8.40 |
| 2026-09-08 | v00 | 2 | 1 | −$0.06 | $0.00 | $0.00 | −$0.06 | $4.30 | −1.4% | +$4.24 |
| 2026-09-07 | v00 | 237 | 229 | −$3.62 | $0.00 | $0.00 | −$3.62 | $13.59 | −26.7% | +$4.30 |
| 2026-09-06 | v00 | 273 | 264 | +$4.78 | $0.00 | $0.00 | +$4.78 | $13.30 | +35.9% | +$7.93 |
| 2026-09-05 | v00 | 10 | 10 | −$1.16 | $0.00 | $4.31 | +$3.15 | $6.46 | +48.8% | +$3.15 |

## Notes

- The raw data is in [`data/daily.csv`](data/daily.csv), [`data/versions.csv`](data/versions.csv) and [`data/series.csv`](data/series.csv).
- P&L is booked to the day each trade happened and valued at the official settlement price. Taker fees count against trading. Liquidity rewards are paid per market day, so they're booked to that day. Each day appears the morning after, once it has settled.
- Returns are measured against the capital the bot actually had in use, not the cap. That is the money in open positions plus resting orders, each order counted at what it would cost if it filled, averaged over the hours the bot was running each day. Most mornings it comes within a few dollars of the cap, until the previous day's markets settle. The cap is $60 now, and the rest of the account sits idle.
- Each update is checked against the actual account balance before it's published. The last check was off by $0.15, which comes from open positions and rounding.
- This repository doesn't contain any code, keys, order data or strategy settings.
