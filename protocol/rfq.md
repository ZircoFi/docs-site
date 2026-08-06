---
description: How block-size swaps are priced by competing makers and settled atomically on-chain.
---

# RFQ settlement

Above the vault clip, and for any trade where a maker beats the vault, execution moves to request-for-quote: professional market makers sign short-lived quotes off-chain, and the taker settles the best one on-chain in a single atomic transaction. Signing costs makers nothing, so they can stream competitive quotes continuously; the chain is only paid for the fill.

## The quote

A maker quote is an EIP-712 typed message:

| Field | Meaning |
| --- | --- |
| `maker` | The signing address, which must hold a `MAKER` attestation |
| `tokenIn`, `tokenOut` | The pair, one side always USDG |
| `amountIn`, `amountOut` | The exact size and price, no partial fills |
| `taker` | The requesting trader, or zero for an open quote |
| `expiry` | Unix seconds; quotes live for seconds, not minutes |
| `nonce` | Single use; also cancellable in bulk |

The quote service broadcasts a trader's request to all connected makers, collects signatures for a few hundred milliseconds, and returns the best one alongside the vault price. The router settles whichever is better for the trader.

## Settlement

`RfqSettlement.settle(quote, signature)` verifies the maker's signature and attestation, the taker's `TRADER` attestation, the nonce and expiry, and that the implied price sits inside the [oracle band](pricing-and-spreads.md). Assets then move directly: `tokenIn` from taker to maker, `tokenOut` from maker to taker, the RFQ protocol fee to the `FeeCollector`. The maker's assets are pulled from their own wallet via a standing Permit2 allowance; ZircoFi holds nothing before, during or after.

A quote that expires, or whose nonce is spent or cancelled, or whose price has fallen outside the band by inclusion time, reverts without cost to the maker beyond their own hedging decisions.

## Why makers come

* **Hedgeable flow.** RFQ flow arrives with size and direction known, against assets with a liquid underlying. A maker filling 200,000 USDG of tokenized NVDA can hedge on the underlying market within seconds during open hours, or price weekend gap risk explicitly when it is closed.
* **The skew subsidy.** Vault [inventory skew](pricing-and-spreads.md) publicly advertises which side each vault will pay to be lifted on. Makers who watch skew earn a spread rebalancing vaults, which is the protocol's rebalancing mechanism working as designed.
* **No exchange fees, no queue.** Winning is a signature. Losing costs nothing.

## Failure containment

Every guard the vault path has applies here: the band bounds any bad quote, attestations bound who can make, expiries bound how long a stale price can live, and atomic settlement means a failed leg reverts the whole fill. A malicious or compromised maker can at worst fill trades inside the band, which is the same worst case as an aggressive but honest one.

Maker onboarding, tooling and the quote protocol specification are in [For market makers](../users/market-makers.md) and [API and SDK](../architecture/api.md).
