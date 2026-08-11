---
description: The quote asset every ZircoFi market trades against.
---

# Quote asset: USDG

Every market is quoted against **USDG**, the Paxos Global Dollar. All spreads, fees and vault accounting are denominated in it, and token-to-token swaps route through it.

## Why USDG

| Reason | Detail |
| --- | --- |
| Native to the chain | USDG is issued natively on Robinhood Chain rather than bridged, which removes bridge risk from the settlement path. |
| The chain's dollar | Robinhood Earn and the chain's lending markets already run on USDG through Morpho, so the dollar traders arrive holding is the dollar the venue quotes in. |
| Regulatory footing | USDG is regulated under MiCA in the EU, which matters for a venue whose primary market is the Stock Token jurisdiction list. |
| Clean semantics | Standard ERC-20, no fee-on-transfer, no rebasing: the properties vault accounting assumes. |

## A single quote asset is a design choice

Routing everything through one quote asset keeps liquidity concentrated instead of fragmenting each Stock Token across several stablecoin pairs, makes every price directly comparable, and keeps two-leg [routing](../protocol/routing.md) simple enough to verify on-chain. The cost is a dependency on one issuer, which is disclosed in the [Risk framework](../risk/framework.md) alongside the others.

## Additional quote assets

The market configuration keys on the quote asset, so a second one (USDC via CCTP once bridged liquidity on the chain is confirmed, or USDe, which is present on the chain) would be a new, isolated set of markets rather than a change to existing ones. A new quote asset is only added once bridged liquidity is confirmed and the token has passed the same bytecode review as any listed asset, through the timelock like any other parameter.

## Decimals and precision

USDG uses 6 decimals. Stock Tokens use 18. `OracleRouter` quotes prices in quote-asset base units per whole token, vault value is computed as `usdgBalance + tokenBalance × price / 10^18`, and the invariant suite checks the arithmetic across both decimal regimes, including rounding direction on share mints and burns.
