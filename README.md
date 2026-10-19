# prediction-market-making

This was a market-making bot that traded on Polymarket US with real money. It ran from September 5 to October 18, 2026, and it used Kalshi's prices for the same markets as its fair value.

It traded the daily high-temperature markets. Each city gets one market a day, split into brackets a couple of degrees wide, and the bracket that holds the day's high pays $1. The bot traded New York, Miami, Chicago, Los Angeles and San Francisco.

The repository has no code, only the results. The bot was stopped on October 18 (see below) and its last positions settled on October 19, so these results are final and this page no longer updates.

Final results: 44 days, September 5 to October 18.

## Returns

| | |
|---|---|
| Total return on capital in use | **+203.1%** (+$68.64 on an average of $33.80 in use) |
| Average daily return | **+4.6%** (net per day over capital in use per day, all 44 days) |
| Last 7 days | **−1.0% per day** (−$2.22 total, on $33 in use) |
| Last version with a full day of data (v1.12.2) | **−8.0% per day** over 2.3 days, Oct 16 to Oct 18 |
| Days up | 29 of 44 (65.9%) |
| Days up, trading only | 23 of 44 (52.3%) |
| Best / worst day | +$9.05 (+20.7%) on Oct 7 / −$10.62 (−20.7%) on Sep 25 |
| Worst drawdown | −$10.62 (−20.7% of the capital in use at the time) |

![Cumulative net P&L](charts/cumulative.svg)

Where it came from:

| | |
|---|---|
| Trading | +$4.75 |
| Maker rebates | +$52.53 |
| Liquidity rewards (rare after Sep 23) | +$11.36 |
| Fills / contracts | 23,628 / 37,931 |
| Capital the bot could use | $60 at the end (earlier: $20 from Sep 5 to Sep 14, $40 from Sep 15 to Sep 21) |
| Capital in use | $30 on average under v1.12.2, $34 over all 44 days |

![Capital: the cap and what the bot used](charts/capital.svg)

## Why the bot stopped

Trading was roughly flat: +$4.75 over 44 days. The money came from the maker rebate ($52.53) and, early on, liquidity rewards ($11.36).

The rewards went first. On September 23 Polymarket cut the daily reward pool for weather markets from $750 to $185, without notice. A side only earns money once 7,500 contracts are resting on it, and after the cut live sides almost never got there, so the bot was paid $11.36 before the cut and nothing after it.

Then the rebate went. From October 19, makers that trade less than $10 million a month no longer get a maker rebate, and this bot was far below that line.

That left only flat trading, so on the afternoon of October 18 I stopped the bot. It closed out what it could, the rest settled the next morning, and it was shut down on October 19.

## About that return

The percentage return looks suspiciously high, and it is real, but it didn't scale with size. Trading was roughly flat, and the profit came from the maker rebate, which is paid per contract filled. Bigger orders barely added fills, so they barely added rebates.

## How it works

The bot kept buy and sell orders resting on both sides of every bracket. It made money from the spread when both sides filled and from the maker rebate the exchange paid on qualifying fills. Kalshi lists the same contracts with deeper books, so the bot took its fair value from the middle of Kalshi's price for each bracket instead of predicting anything itself. Its quotes were post-only, so 98% of its fills came as the maker, and from October 2 it quoted only the current day's markets.

Each quote was 1 contract at first, 2 from September 22 and 3 from September 29. A bracket could hold at most 2 contracts at first and 4 from September 22, and the bot had a hard cap on capital: $20, then $40 from September 15 and $60 from September 22.

Most of the losses came from getting picked off by traders who knew something before the market did, and much of the version history is about limiting that. Quotes were pulled as soon as Kalshi moved. A separate dead-man switch cancelled every order if the bot went quiet for 3 minutes, and a soft stop wound positions down after a sharp fall in the account ($10 in 24 hours or $8 in 4 hours, at the end). A watchdog checked on all of it every few minutes.

Every change shipped as a new version. Before a version went live, I wrote down what it should change and how I'd measure it, and each change to the trading logic ran for at least 48 hours before the next one.

## Timeline

Outside events that changed the economics. They are the purple lines on the charts.

| Date (ET) | What happened |
|---|---|
| Sep 22 | Switched to collecting Polymarket's maker rebate on qualifying fills. |
| Sep 23 | Polymarket cut the liquidity reward pool for weather markets from $750 to $185 a day. The bot rarely qualified after that. |
| Oct 9 | Started testing a way to qualify for liquidity rewards again, beginning with tiny orders. |
| Oct 18 | Stopped the bot in the afternoon. |
| Oct 19 | The last positions settled and the bot was shut down. Polymarket's maker rebate for small makers ended the same day. |

## Charts

![Daily P&L](charts/daily.svg)

![Where the money came from](charts/sources.svg)

![Contracts traded per day](charts/volume.svg)

## Versions

| Version | Started (ET) | What changed | Days | Net | Net/day | In use | Return/day | Trading | Rebates | Contracts |
|---|---|---|---:|---:|---:|---:|---:|---:|---:|---:|
| v1.0 | Sep 5, 11:34 AM | First version: a $20 test run that quotes both sides of every bracket off Kalshi prices. | 5.6 | +$7.13 | +$1.27 | $13 | +9.7% | −$0.48 | $0.00 | 1,709 |
| v1.1 | Sep 11, 2:33 AM | Joins the queue at the best price and goes after the liquidity rewards. | 1.8 | +$4.56 | +$2.55 | $15 | +17.4% | +$4.56 | $0.00 | 1,200 |
| v1.2 | Sep 12, 9:28 PM | Detects exchange halts and settled markets, and reports its safety limits to the monitor. | 0.2 | +$0.55 | too short | $17 | too short | +$0.55 | $0.00 | 91 |
| v1.3 | Sep 13, 1:39 AM | Waits for fill confirmation before taking on more risk (added after an exchange data outage). | 1.8 | +$5.35 | +$3.03 | $15 | +19.7% | +$5.35 | $0.00 | 863 |
| v1.4 | Sep 14, 8:03 PM | Combines the fixes above into a single build. | 0.3 | −$5.58 | too short | $18 | too short | −$5.58 | $0.00 | 87 |
| v1.5 | Sep 15, 4:04 AM | Raises the cap to $40 to cover more markets at the same order size. | 0.5 | −$0.72 | too short | $27 | too short | −$0.72 | $0.00 | 504 |
| v1.6 | Sep 15, 3:48 PM | Pulls resting quotes as soon as Kalshi moves. | 6.1 | +$8.91 | +$1.46 | $28 | +5.2% | +$7.65 | $0.00 | 3,961 |
| v1.7 | Sep 21, 6:08 PM | Restarts the bot as a long-running service, with no logic changes. | 0.7 | −$2.79 | too short | $33 | too short | −$2.79 | $0.00 | 359 |
| v1.8 | Sep 22, 10:11 AM | Uses larger orders and raises the cap to $60. | 1.7 | +$11.08 | +$6.34 | $52 | +12.2% | +$4.63 | $3.96 | 2,440 |
| v1.9 | Sep 24, 4:07 AM | Adds a soft stop that winds positions down after a losing stretch. | 5.2 | +$0.78 | +$0.15 | $53 | +0.3% | −$8.99 | $9.77 | 5,569 |
| v1.10 | Sep 29, 8:51 AM | Uses larger orders again. | 2.1 | +$3.14 | +$1.52 | $53 | +2.9% | −$2.62 | $5.76 | 2,799 |
| v1.11 | Oct 1, 10:16 AM | Drops an outside model input, so fair value comes from Kalshi alone. | 1.0 | +$5.53 | +$5.71 | $55 | +10.4% | +$3.08 | $2.45 | 1,328 |
| v1.12 | Oct 2, 9:32 AM | Quotes only same-day markets, so nothing is held overnight. | 2.4 | +$18.51 | +$7.73 | $42 | +18.3% | +$11.37 | $7.14 | 3,605 |
| v1.12.1 | Oct 4, 7:00 PM; again Oct 14, 11:00 AM | Adds a small safety fix for wind-downs. Back in use from Oct 14 to Oct 16, after the v1.14 trading changes measured worse, with the rewards test running at full size overnight. | 6.4 | +$10.35 | +$1.62 | $38 | +4.3% | −$1.52 | $11.87 | 6,683 |
| v1.14 | Oct 9, 11:16 AM | Fixes a few small bugs, reads prices faster, and starts a small test of a way back into the liquidity rewards. | 0.1 | −$5.07 | too short | $52 | too short | −$5.55 | $0.48 | 201 |
| v1.14.1 | Oct 9, 12:40 PM | Changes only when the rewards test pulls its orders. | 2.1 | +$5.34 | +$2.59 | $41 | +6.2% | +$1.44 | $3.90 | 2,097 |
| v1.14.2 | Oct 11, 2:13 PM | Adds safety rules to the rewards test. | 1.0 | +$7.19 | +$7.07 | $45 | +15.8% | +$5.31 | $1.88 | 1,160 |
| v1.14.3 | Oct 12, 2:37 PM | Runs the rewards test on one market at a time. | 0.0 | +$0.39 | too short | $34 | too short | +$0.10 | $0.29 | 126 |
| v1.14.4 | Oct 12, 3:13 PM | Keeps the rewards test's orders in place without gaps. | 1.8 | −$1.01 | −$0.56 | $37 | −1.5% | −$3.81 | $2.80 | 1,666 |
| v1.12.2 | Oct 16, 4:00 AM | Stops trading San Francisco, which lost money on trading in both halves of the record. The overnight rewards test now only uses same-day markets. | 2.3 | −$5.59 | −$2.41 | $30 | −8.0% | −$7.47 | $1.88 | 1,324 |
| v1.15 | Oct 18, 11:34 AM | The final version. It ran for a few hours on October 18 before the bot was stopped. | 0.2 | +$0.60 | too short | $12 | too short | +$0.25 | $0.35 | 157 |

Versions that ran for less than about 18 hours say "too short" instead of per-day figures and aren't in the chart, since stretching a few hours into a full day isn't meaningful. Return per day is measured against the average capital the bot had in use while that version ran.

![Net P&L per day by version](charts/versions.svg)

## By city

Each city is one daily market on that day's high temperature. These totals include rebates and rewards.

| City | Fills | Contracts | Trading | Rebates | Rewards | Net |
|---|---:|---:|---:|---:|---:|---:|
| Chicago | 4,211 | 6,810 | −$10.10 | $10.33 | $2.09 | +$2.32 |
| Los Angeles | 5,926 | 9,701 | +$25.97 | $12.31 | $0.74 | +$39.02 |
| Miami | 4,351 | 6,681 | −$3.37 | $9.75 | $0.90 | +$7.27 |
| New York | 4,910 | 8,128 | +$7.57 | $11.33 | $0.64 | +$19.54 |
| San Francisco | 4,230 | 6,611 | −$15.31 | $8.81 | $6.99 | +$0.49 |

![Net P&L by city](charts/series.svg)

Los Angeles came out best, but over 44 days that could just have been luck.

## Notes

- The raw data is in [`data/daily.csv`](data/daily.csv), [`data/versions.csv`](data/versions.csv) and [`data/series.csv`](data/series.csv).
- P&L is booked to the day each trade happened and valued at the official settlement price. Taker fees count against trading. Liquidity rewards are paid per market day, so they're booked to that day. Every day here has settled.
- Returns are measured against the capital the bot actually had in use, not the cap. That is the money in open positions plus resting orders, each order counted at what it would cost if it filled, averaged over the hours the bot was running each day. Most mornings it came within a few dollars of the cap, until the previous day's markets settled. The cap was $60 at the end, and the rest of the account sat idle.
- The results were checked against the actual account balance. The final check was off by $0.23, which comes from rounding.
- This repository doesn't contain any code, keys, order data or strategy settings.
