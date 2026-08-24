---
description: A practical guide to swapping on ZircoFi, from onboarding to reading a fill receipt.
---

# For traders

## Before your first swap

1. **Wallet.** Connect Robinhood Wallet, MetaMask or any standard wallet, or create an embedded account with a passkey on the [platform](https://zircofi.com/platform).
2. **Eligibility.** Complete the attestation flow once. The KYC provider verifies identity, sanctions status and jurisdiction and issues a `TRADER` attestation to your wallet; no personal data goes on-chain. The platform shows your eligibility state at all times. See [Eligibility registry](../architecture/eligibility.md).
3. **USDG.** Markets quote against USDG. Stock Tokens you hold already are equally good starting assets; token-to-token swaps route through USDG automatically.

## Reading the ticket

Every quote itemises the same four things:

| Line | What it is |
| --- | --- |
| Mid | The guarded Chainlink price this quote is built on, with its oracle round |
| Spread | The venue's charge for immediacy, in bps and USDG, including any inventory skew |
| Fee | The protocol fee, itemised separately |
| Regime badge | `OPEN`, `EXTENDED` or `CLOSED`, with the multiplier in force |

If the badge says `CLOSED`, you are paying roughly three times the regular spread for weekend liquidity, in smaller maximum size. That is the honest cost of trading an asset whose underlying market is shut; if your trade can wait for Monday's open, waiting is cheaper, and the ticket is telling you so.

## Slippage and expiry

The quote you sign carries a minimum-output bound and a deadline. If price or vault state moves against you beyond the bound before inclusion, the swap reverts rather than fills; about 100 ms preconfirmations make that rare in practice. RFQ quotes additionally expire in seconds; the platform re-quotes automatically.

## Large trades

Above the vault clip the router prices you through [RFQ](../protocol/rfq.md) without you doing anything different: same ticket, same itemisation, the venue line reads "maker" instead of "vault". For sizes that want working over time, splitting is currently manual; execution algorithms are on the [roadmap](../roadmap/phases.md).

## After the fill

The receipt shows the fill against the on-chain event: the same breakdown the ticket quoted, the venue that filled it, and a Blockscout link. The [execution quality](../transparency/execution-quality.md) page aggregates every fill on the venue against the oracle mid, including yours; you never have to take execution on faith.

## What you hold

A Stock Token is a tokenized debt claim on Robinhood Assets (Jersey) Ltd tracking the underlying share, not the share itself. Dividends accrete into the token via the ERC-8056 multiplier rather than arriving as cash. Read [Stock Tokens](../assets/stock-tokens.md) and [Issuer risk](../risk/issuer.md) once before trading size; the disclosure at first swap summarises both.

## When markets halt

A halt (oracle stale, corporate action, sequencer recovery) pauses quoting in that market only. Your assets are in your wallet; nothing is locked. The platform shows the halt reason and the state that clears it. See [Trading regimes](../protocol/trading-regimes.md).
