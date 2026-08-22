---
description: The reference front-end at zircofi.com/platform, and the experience standards it is held to.
---

# Platform

The platform is the reference client: a Next.js application at [zircofi.com/platform](https://zircofi.com/platform) consuming the same public [API](api.md) and contracts available to every integrator. It holds no keys, sees no funds, and has no privileged path; its job is to make the honest mechanics legible.

## The ticket

The swap ticket is the product's centre, and it renders the [pricing formula](../protocol/pricing-and-spreads.md) rather than hiding it:

* Mid, spread, and fee as separate labelled numbers, in bps and USDG, with the oracle round linkable.
* The regime badge on the ticket face: "US markets closed. Weekend spread ×3 applies." A trader never learns the session after the fill.
* The all-in rate the slippage bound protects, and a countdown on RFQ quote validity.
* For two-leg swaps, both legs shown, both itemised.

Fills land as a receipt with the on-chain breakdown and a Blockscout link. What was quoted is provably what was charged, every time, because the event carries the same decomposition.

## Onboarding

Connect Robinhood Wallet, MetaMask or any standard wallet, or create an embedded smart account (Alchemy Account Kit) with a passkey. Eligibility onboarding runs the attestation flow with the KYC provider; the platform shows eligibility state before any signing, and an ineligible user is told which rule applies, not shown a revert. First swaps bundle Permit2 approval and the swap into one sponsored user operation.

## Portfolio

A wallet-centric view of listed holdings: balances in token and share terms (via `uiMultiplier()`), valuations from the same guarded oracles the vaults quote from, cost basis and realised PnL reconstructed from on-chain history, and LP positions with value per share, accrued spread income and current inventory mix per vault.

## Explorer surfaces

The [trade explorer](../users/trade-explorer.md) and [execution quality](../transparency/execution-quality.md) pages are part of the platform and load without a wallet. Transparency surfaces are public by definition; nothing about the venue's record sits behind a login.

## Experience standards

The bar is the one set in [Design principles](../introduction/design-principles.md): calm, technical, precise. Concretely:

* Numbers in tabular figures, aligned; bps and USDG never mixed in one column; timestamps in the user's zone with UTC on hover.
* Empty, loading and error states written in full sentences that say what happened and what to do.
* Degradation is explicit: if the quote service is down, the platform quotes vault-only from the RPC and says so; if the RPC is down, it says that instead of spinning.
* No dark patterns of any kind: no preselected slippage above default, no urgency theatre, no obscured fees. The venue's economics survive being understood.

## Self-hosting

The platform is open source. Running `pnpm dev` against any RPC and the public API reproduces the entire experience except the hosted KYC flow, which is replaceable with any issuer the registry accepts.
