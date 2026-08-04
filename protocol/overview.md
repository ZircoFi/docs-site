---
description: How a swap moves through ZircoFi, from quote to on-chain settlement.
---

# Protocol overview

ZircoFi is three mechanisms behind one entry point: oracle-anchored vaults for standard size, RFQ for block size, and a router that checks eligibility and settles whichever venue prices the trade better.

## The path of a swap

1. **Quote.** The trader asks for a price on, say, 2,000 USDG of tokenized NVDA. The quote service simulates the router: it reads the guarded Chainlink mid from `OracleRouter`, the current [regime](trading-regimes.md), and the [anchor vault's](anchor-vaults.md) inventory, and in parallel collects signed [RFQ quotes](rfq.md) from makers if the size warrants it. The trader sees one price, itemised into mid, spread and fee.
2. **Sign.** The trader signs a single transaction: a `SwapRouter` call carrying the swap parameters, a slippage bound, a deadline, and a Permit2 pull for the input asset.
3. **Verify.** On-chain, the router checks the trader's `TRADER` attestation, re-derives the vault quote from the oracle and vault state (or verifies the maker's EIP-712 signature), and confirms the fill respects the trader's bound and the protocol's oracle band.
4. **Settle.** Assets move atomically: input from the trader, output from the vault or the maker, fee to the `FeeCollector`. One transaction, one fill event, visible on Blockscout seconds later.

There is no order book to maintain, no deposit step and no withdrawal step. Custody exists for the duration of one transaction, in immutable contracts.

## Multi-asset swaps

Every market is quoted against USDG. A swap between two listed assets (tokenized AAPL into tokenized SPY, a Stock Token into tokenized gold once listed) is two legs through USDG executed atomically in one router call. The trader sees a single price; the fill event records both legs. If either leg cannot clear inside its band, the whole swap reverts.

## The division of labour

| Size | Venue | Why |
| --- | --- | --- |
| Up to the vault clip | Anchor vault | Instant, no counterparty coordination, spread set by formula |
| Above the clip | RFQ | Makers can hedge on the underlying market and price size better than a formula |
| Any size, maker quote better | RFQ | The router always settles the better price; makers compete with the vault tick by tick |

The clip is a per-market parameter, not a hard split: makers may quote inside the clip and win retail flow whenever they beat the vault.

## What is deliberately absent

* **No pooled cross-asset exposure.** Each vault holds one asset against USDG. LPs choose their exposure per market.
* **No protocol discretion at fill time.** The quote is a formula over public state; the RFQ path is a signature check. Nobody at ZircoFi can improve, worsen or block an individual fill.
* **No netting or deferred settlement.** Every fill is final at inclusion.

## Reading order

The mechanics are documented inside-out: [Anchor vaults](anchor-vaults.md) for the inventory model, [Pricing and spreads](pricing-and-spreads.md) for the quote formula, [Trading regimes](trading-regimes.md) for sessions and halts, [RFQ settlement](rfq.md) for block flow, [Routing](routing.md) for how they compose, [Liquidity provision](liquidity-provision.md) for the LP side, [Corporate actions](corporate-actions.md) for events, and [Fees](fees.md) for what the protocol charges.
