---
description: How each market's state is derived from the underlying session, and what each state changes.
---

# Trading regimes

Equity markets are open about 32 hours a week in regular session. Stock Tokens trade around the clock. The regime engine is how ZircoFi prices the difference instead of pretending it does not exist.

## The four regimes

| Regime | Derived from | Quoting |
| --- | --- | --- |
| `OPEN` | Regular session per the oracle's market status | Tightest spreads, full clips |
| `EXTENDED` | Extended-hours session | Spreads ×1.5, clips ×0.75 |
| `CLOSED` | Underlying market closed | Spreads ×3.0, clips ×0.5 |
| `HALTED` | Oracle stale, `oraclePaused()`, corporate action, sequencer recovery or guardian pause | No quotes; deposits and withdrawals unaffected |

Regime state is per market, held in `OracleRouter`, and read by every quote. The multipliers are `ParamController` values per tier.

## How regime is derived

`OracleRouter` classifies each market from two independent signals:

* the Chainlink Data Streams `marketStatus` field (v11 RWA schema; a value of 5 means closed), and
* the Data Feed's `updatedAt` timestamp against the session's staleness bound (1 hour in regular session, 2 in extended, longer bounds when closed).

Either signal can only make the regime stricter, never looser: a feed that looks live while the stream says closed is treated as closed. Anyone can poke the transition function; the keeper service does it within seconds of a session boundary, and a swap that arrives before the poke recomputes the regime in the same transaction, so there is no window in which stale-session pricing can be used.

## Why `CLOSED` trades at all

The alternative designs are both worse. Refusing to quote on weekends recreates market hours on a 24/7 chain and pushes users back to custodial venues. Quoting weekend trades at weekday spreads makes LPs the counterparty to every Monday gap for free. ZircoFi's position is the honest middle: weekend liquidity exists, costs more, in smaller size, and the ticket says exactly how much more. The closed-session multiplier is the price of gap risk, set conservatively at launch and retuned against realised Monday-open gaps, with every change through the timelock.

## `HALTED` in detail

A market halts when any of the following holds, and unhalts when none does:

| Trigger | Cleared by |
| --- | --- |
| Feed staleness beyond the session bound | A fresh oracle round |
| `oraclePaused()` on the feed (corporate action in progress) | The feed resuming; see [Corporate actions](corporate-actions.md) |
| Single-round price move above the 25% guard | Timelocked review, published finding |
| Sequencer recovery grace (1 hour after an outage) | Grace expiry; see [Sequencer and chain risk](../risk/sequencer-and-chain.md) |
| Guardian pause | Unpause, with a published incident note |

A halt stops quoting and nothing else. LP deposits and withdrawals, RFQ cancellations and every view function keep working. The trade explorer shows the halt reason live, drawn from the same contract state the router enforces.

## Calendar independence

There is no hard-coded market calendar anywhere in the protocol. Holidays, half-days and unscheduled closures all arrive through the oracle's market status, so the venue is never open when the data says the market is closed, and never stuck closed on data that says otherwise.
