---
description: The formula behind every vault quote, and every input to it.
---

# Pricing and spreads

Every vault quote is built the same way. Nothing about a price is discretionary; each term is a public contract read, so anyone can reproduce any quote from on-chain state.

## The formula

```
ask = mid × (1 + (baseHalfSpread × regimeMultiplier + skewTerm + feeBps) / 10_000)
bid = mid × (1 − (baseHalfSpread × regimeMultiplier − skewTerm + feeBps) / 10_000)
```

| Term | Source | What it prices |
| --- | --- | --- |
| `mid` | `OracleRouter`, guarded Chainlink price | The asset's current value |
| `baseHalfSpread` | Tier parameter | Inventory holding cost and residual oracle latency in the regular session |
| `regimeMultiplier` | [Regime](trading-regimes.md) state | Gap risk while the underlying market is closed or in extended hours |
| `skewTerm` | Vault inventory | The cost of taking the vault further from target (signed: negative on the side that rebalances) |
| `feeBps` | `ParamController` | The protocol fee, itemised separately on the ticket |

## Initial parameters

Half-spreads in basis points; set per [tier](../assets/listing-framework.md) and changeable only through the timelock.

| Tier | Base half-spread | Regular clip | Examples |
| --- | --- | --- | --- |
| A | 10 bps | 50,000 USDG | SPY, QQQ, AAPL, MSFT, NVDA |
| B | 20 bps | 20,000 USDG | Liquid single names outside Tier A |
| C | 40 bps | 5,000 USDG | Long-tail Stock Tokens |

| Regime | Multiplier | Clip factor |
| --- | --- | --- |
| `OPEN` | 1.0 | 1.0 |
| `EXTENDED` | 1.5 | 0.75 |
| `CLOSED` | 3.0 | 0.5 |
| `HALTED` | No quotes | 0 |

A Tier A trade in regular hours pays about 10 bps over mid plus the protocol fee. The same trade on a Saturday pays about 30 bps, and the ticket says why. These are launch values; realised numbers are published on the [execution quality](../transparency/execution-quality.md) page, and parameters are retuned against them through the timelock.

## The skew term

`skewTerm = maxSkewBps × (currentRatio − targetRatio) / inventoryBand`

Linear in drift, capped at `maxSkewBps` (initially 15 bps for Tier A) at the band edge. The sign convention does the work: a vault long of tokens quotes a cheaper ask and a meaner bid, so the flow that helps the vault pays less and the flow that hurts it pays more. At the band edge the harmful side stops quoting entirely. This is the entire rebalancing mechanism; there are no rebalancing auctions, keeper trades or discretionary interventions. Makers and arbitrageurs are paid by the skew to do it.

## The band

Independently of the spread arithmetic, `SwapRouter` enforces a hard band around the mid (initially 75 bps for Tier A, wider for B and C) outside which no fill can clear, from a vault or from RFQ. The band is the last line against a mispriced quote, a manipulated maker signature or a parameter mistake: whatever goes wrong upstream, a fill more than the band away from the guarded oracle price reverts.

## What the trader sees

The ticket itemises exactly the formula: mid (with the oracle round it came from), spread in bps and USDG, protocol fee in bps and USDG, regime badge, and the all-in price against which the slippage bound applies. The quote the trader signs is the quote the chain enforces; if state moves so that the fill would be worse than the signed bound, it reverts rather than fills.
