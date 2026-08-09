---
description: How dividends, splits and other corporate actions flow through a market without breaking it.
---

# Corporate actions and dividends

Corporate actions are where naive RWA venues quietly lose money: a 10-for-1 split reprices a token overnight, and a pool that does not know donates the difference to the first arbitrageur awake. ZircoFi inherits most of the solution from the asset design and the oracle, and adds a halt for the remainder.

## Dividends: nothing to do, by design

Robinhood Stock Tokens implement ERC-8056: `uiMultiplier()` reports the number of underlying shares per token, and dividends are reinvested by increasing the multiplier rather than paid out as a separate distribution. The Chainlink price already includes the multiplier. The consequences for a trading venue are all favourable:

* There is no dividend token to claim, sweep or allocate between LPs and traders.
* There is no ex-dividend price cliff for the vault to misprice; value accretes continuously into the token and its feed.
* Vault accounting needs no dividend logic at all. An LP's share value accretes with the multiplier like any other price movement.

The trade explorer displays holdings in both token and share terms by reading `uiMultiplier()` alongside the price.

## Splits, mergers and delistings: halt, adjust, resume

Around a split, merger, symbol change or similar event, the Chainlink feed sets `oraclePaused()`. That flag flips the market to [`HALTED`](trading-regimes.md) automatically: no quotes from the vault, no RFQ settlement inside the market, deposits and withdrawals unaffected.

When the feed resumes, the price and the multiplier already reflect the action, and the market unhalts with quoting anchored to the post-event price. The vault held the same tokens throughout, so no inventory adjustment is needed; a split changes the number the feed reports, not what the vault owns.

| Event | During | After |
| --- | --- | --- |
| Dividend | Nothing; multiplier and price accrete | Nothing |
| Split or reverse split | `HALTED` while `oraclePaused()` | Resume on the adjusted feed; inventory unchanged |
| Merger, acquisition | `HALTED` | Resume, or [retirement](#delisting-and-retirement) if the token is wound down |
| Delisting of the underlying | `HALTED` | Retirement |

## Delisting and retirement

If an underlying is delisted or the issuer winds a token down, the market moves to `RETIRED` through the timelock: quoting off permanently, withdrawals only. LPs withdraw their pro-rata inventory in kind and deal with the issuer's redemption process directly if they choose; the protocol never takes a position in a wind-down. The retirement rationale is published in the governance log like any parameter change.

## The residual risk

The design depends on the feed pausing before stale prices can be hit. Chainlink's equity feeds set `oraclePaused()` during corporate actions, and the [staleness guard](../risk/oracles.md) backstops the case where a feed misbehaves instead of pausing: a feed that goes quiet trips the staleness halt on its own. The window that remains, a feed publishing confidently wrong prices around an event, is the same oracle risk every fill carries, and the [band](pricing-and-spreads.md) caps the damage of any single fill to the band width.
