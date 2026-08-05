---
description: The per-asset vaults that hold inventory and quote both sides of every market.
---

# Anchor vaults

An anchor vault is one market: one Stock Token, USDG, and the LPs who fund both sides. The vault quotes a bid and an ask around the oracle mid at all times except during a halt, fills swaps up to its clip size, and accrues the spread to its LPs.

## Inventory model

Each vault targets a configured inventory mix, 50/50 by value at launch, and operates inside a hard band around it.

| Parameter | Meaning | Tier A initial value |
| --- | --- | --- |
| `targetRatioBps` | Target share of vault value held in the Stock Token | 5,000 (50%) |
| `inventoryBandBps` | Maximum drift from target before one side stops quoting | 2,000 (±20%) |
| `maxClip` | Largest single swap against the vault | 50,000 USDG |
| `dailyVolumeCap` | Per-market volume ceiling during the guarded launch | 2,000,000 USDG |

When traders buy the token, the vault's token inventory falls and its USDG rises; the vault is now short its target and [skew pricing](pricing-and-spreads.md) makes its bid more attractive and its ask less so, which pays arbitrageurs and makers to bring inventory back. If flow is one-way enough to reach the band edge, the vault stops quoting that side entirely rather than take unbounded inventory risk. The other side keeps quoting.

## Why inventory, not a curve

A constant-product pool also holds inventory, but its price is a function of that inventory, so every reference-price move leaves it quoting stale prices that arbitrageurs collect. An anchor vault's price is a function of the oracle; inventory only moves the spread. The vault is never the last to know what the asset is worth, so its LPs earn the spread instead of paying the arbitrage. The trade-off is honest: the vault depends on the oracle, and everything about how that dependency is guarded is in [Oracles and market sessions](../risk/oracles.md).

## Value accounting

Vault value is `usdgBalance + tokenBalance × mid`, marked at the guarded oracle price. LP shares follow ERC-4626-style accounting against that value: deposits mint shares at the current value per share, spread revenue accrues into it, and withdrawals burn shares for a pro-rata, in-kind mix of both assets. Because withdrawals are in kind, an LP exit never forces the vault to trade, and the pause can never trap LP funds. Details and LP economics are in [Liquidity provision](liquidity-provision.md).

## Lifecycle

| State | Meaning |
| --- | --- |
| `ACTIVE` | Quoting both sides, subject to regime |
| `ONE_SIDED` | Inventory at band edge; quoting the rebalancing side only |
| `HALTED` | No quotes: oracle stale, `oraclePaused()`, corporate action, or guardian pause. Deposits and withdrawals remain open |
| `RETIRED` | Delisting: quoting off permanently, withdrawals only |

A vault is deployed by the `VaultFactory` from the parameters set at [listing](../assets/listing-framework.md), and every parameter is a `ParamController` value changeable only through the timelock.

## Invariants

The properties the contract enforces and the test suite proves:

* No fill ever clears outside the oracle band, in any state, under any parameter set.
* Share value per share never decreases from a swap (spread revenue is non-negative by construction).
* `HALTED` and `RETIRED` never restrict withdrawals.
* Inventory can never exceed the band through the vault's own quoting.
