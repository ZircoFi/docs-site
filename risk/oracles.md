---
description: How assets are priced, and how the venue behaves when the underlying market is closed or the feed is unreliable.
---

# Oracles and market sessions

Every price in ZircoFi comes through one contract, `OracleRouter`, which wraps Chainlink and applies the protocol's guards before a price reaches a quote, a fill check or the regime engine. The venue's whole design stands on the anchor price, so this page is the load-bearing one.

## Sources

| Source | Used for | Notes |
| --- | --- | --- |
| Chainlink **Data Feeds** (24/5 equities) | The mid for vault quoting and band checks | Push-based, on-chain, updated on deviation or heartbeat |
| Chainlink **Data Streams** (v11 RWA schema) | Session classification; sub-second cross-check on RFQ fills | Pull-based; carries `marketStatus` (a value of 5 means closed) |
| Chainlink **Sequencer Uptime Feed** | Post-outage trading grace | See [Sequencer and chain risk](sequencer-and-chain.md) |
| ERC-8056 `uiMultiplier()` | Share-denominated display in the explorer | Read from the token; the multiplier is already reflected in the Chainlink price |

Using both feeds and streams gives a cross-check: a fill cannot clear at a price that diverges from the other source beyond a configured tolerance, and the stream's market status can only make the regime stricter than the feed alone would.

## Sessions

`OracleRouter` classifies every market as **regular**, **extended** or **closed** from the stream's `marketStatus` and the feed's `updatedAt` timestamp, and the [regime engine](../protocol/trading-regimes.md) maps that to spreads and clips.

| Session | Staleness bound | Effect |
| --- | --- | --- |
| Regular | 1 hour | Tier spreads as published |
| Extended | 2 hours | Spreads ×1.5, clips ×0.75 |
| Closed | 4 days | Spreads ×3.0, clips ×0.5; the Friday close anchors weekend quoting |

The closed-session multiplier exists because the last Friday close can be far from Monday's open. LPs quoting through the weekend are charging for that gap explicitly, and a trader who wants to transact at 3am on Sunday pays a disclosed premium for a real service instead of an undisclosed one for a mispriced service.

## Guards

Before any price is used, `OracleRouter` rejects or halts on:

| Condition | Action |
| --- | --- |
| Price is zero or negative | Revert |
| `updatedAt` older than the session's staleness bound | Market `HALTED` until a fresh round |
| Single-round move above the 25% cap | Market `HALTED` pending timelocked review with a published finding |
| Feed reports `oraclePaused()` | Market `HALTED` (corporate action in progress); see [Corporate actions](../protocol/corporate-actions.md) |
| Stream and feed diverge beyond tolerance | Fill reverts |

A halt stops quoting and settlement in that market and nothing else: withdrawals, cancellations and reads all keep working.

## Price the exact token

`OracleRouter` is configured per market with the feed for the token actually held in the vault. It never derives a price through a wrapper, a vault share, or an exchange rate between two tokens. This is the lesson of the Edel Finance wGOOGLx incident of July 2026, where the oracle for the underlying was correct but a wrapper's exchange rate was pushed 78 times higher and trusted. See [Lessons from RWA trading](lessons.md).

## Changing an oracle

Oracle adapters are `ParamController` values per market. Changing one is a timelocked action with a published rationale. `OracleRouter` exposes a view of the current feed, session, staleness, multiplier and pause flag for every market, and the [trade explorer](../users/trade-explorer.md) displays it live, so the state the router enforces is always the state the public can see.
