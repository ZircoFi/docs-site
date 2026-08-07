---
description: The single entry point that composes vaults and RFQ into one quote.
---

# Routing

`SwapRouter` is the only contract traders interact with. It owns four jobs: eligibility, venue selection, multi-leg composition, and the enforcement of every bound a fill must respect.

## Venue selection

For a given size, the router considers the anchor vault (if the size is within clip and the regime allows quoting) and any valid maker quote supplied with the call. It settles the path with the better all-in output for the trader. The comparison is on-chain and exact: the vault quote is re-derived from oracle and vault state in the same transaction, not trusted from the front-end, and the maker quote is verified by signature. A front-end cannot misroute a trade to a worse price, because the router will not settle a supplied path that the vault beats.

## Two-leg swaps

Token-to-token swaps execute as two legs through USDG, atomically:

1. Leg one sells `tokenIn` to its vault or maker for USDG.
2. Leg two spends that USDG on `tokenOut` from its vault or maker.

Each leg is venue-selected independently, so one leg can fill against a vault and the other against a maker. Each leg respects its own market's band and regime; if either fails, both revert. The fill event records both legs with their itemised pricing, and the trader's slippage bound applies to the end-to-end rate.

## What the router enforces on every fill

| Check | Failure mode prevented |
| --- | --- |
| `TRADER` attestation on the caller | Ineligible participation |
| Oracle band per leg | Any fill away from the guarded reference price |
| Regime permits quoting | Trading through a halt |
| Clip and daily volume caps | Oversized flow during the guarded launch |
| Trader's slippage bound and deadline | Filling on moved state |
| Permit2 pull matches the signed amounts | Allowance misuse |

## Ordering and MEV

Robinhood Chain runs a first-come-first-served sequencer with about 100 ms preconfirmations, which removes the public-mempool sandwich as it exists on Ethereum today. The design does not rely on that staying true: quotes are already bounded by the signed slippage limit and the oracle band, so the worst any ordering advantage can extract is the width the trader already agreed to. If the chain enables Timeboost or otherwise changes ordering policy, the review is published in the governance log. See [Sequencer and chain risk](../risk/sequencer-and-chain.md).

## Integration surface

Anyone can call the router; it is a public contract with a stable interface, and eligibility is per-caller, not per-front-end. Wallets, portfolio apps and agents integrate through the [quote API and SDK](../architecture/api.md) for pricing and submit through the router directly. The reference front-end at [zircofi.com/platform](https://zircofi.com/platform) has no privileged path.
