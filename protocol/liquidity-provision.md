---
description: How LPs fund the vaults, what they earn, and exactly what risks they carry.
---

# Liquidity provision

LPs fund anchor vaults and earn the spread on every fill the vault makes. The design goal is that an LP's return comes from providing a real service, immediacy in a market with an external reference price, rather than from being the unpaid counterparty to arbitrage.

## Depositing

Deposits are per market and open to holders of an `LP` attestation. A deposit can be USDG, the market's Stock Token, or both; it is valued at the guarded oracle mid and mints shares at the current value per share. There are no deposit fees and no lockups.

Shares follow ERC-4626-style accounting. Value per share is `(usdgBalance + tokenBalance × mid) / totalShares` and increases as spread revenue accrues. Shares are transferable only between attested LPs.

## Withdrawing

Withdrawals burn shares for a **pro-rata, in-kind** mix of the vault's current inventory: an LP holding 1% of shares receives 1% of the vault's USDG and 1% of its tokens. In-kind withdrawal is a deliberate property with three consequences:

* An LP exit never forces the vault to trade, so exits cannot move the market or be sandwiched.
* Withdrawals work in every vault state, including `HALTED` and `RETIRED`. No pause, halt or parameter can trap LP funds.
* The mix an LP receives reflects the vault's current skew. An LP who wants pure USDG swaps the token leg like any trader.

## What LPs earn

The vault's realised spread on every fill, minus the protocol's 10% [fee share](fees.md), accrues into value per share continuously. There are no emissions and no points; the yield is the market's actual cost of immediacy. Realised spread revenue per market is published live on the [trade explorer](../users/trade-explorer.md), so an LP can underwrite a vault from its record rather than a projection.

## What LPs risk

Stated plainly, because an LP who has not priced these should not deposit:

| Risk | Nature | Mitigation |
| --- | --- | --- |
| Price exposure | The vault holds the Stock Token; its value moves with the stock. LPs are long the asset roughly half the time by design | Per-market isolation; the LP chooses which assets to hold |
| Gap risk | Weekend and overnight fills happen at prices that can gap at the open | Closed-regime multipliers and clip reductions exist precisely to charge for this |
| Oracle risk | A wrong mid means wrong quotes | The guard stack in [Oracles and market sessions](../risk/oracles.md); the band caps the damage per fill |
| Issuer risk | Stock Tokens are issuer debt claims | See [Issuer risk](../risk/issuer.md); disclosed, not diversified away |
| Adverse flow | Sustained one-way flow parks the vault at its band edge | The vault goes one-sided rather than absorbing unbounded inventory |

What LPs do **not** carry: leverage, liquidation risk, socialised losses from other markets, or exposure to assets they did not choose. Each vault is an island.

## Guarded launch

During the guarded phase, per-market TVL caps and the daily volume caps bound total exposure while parameters are tuned against realised data. Caps are raised through the timelock as the [execution quality](../transparency/execution-quality.md) record accumulates, on the schedule in the [Roadmap](../roadmap/phases.md).
