---
description: A practical guide to funding vaults: depositing, earning, monitoring and exiting.
---

# For liquidity providers

Providing liquidity on ZircoFi means funding one or more [anchor vaults](../protocol/anchor-vaults.md) and earning the spread on the fills they make. It is closer to underwriting a market-making book than to farming: the return is real flow revenue, and the risks are inventory risks, stated in full in [Liquidity provision](../protocol/liquidity-provision.md). Read that page first; this one is the practical sequence.

## Onboarding

LP participation requires an `LP` attestation, issued to professional clients at launch through the same flow as trading eligibility. The vaults you fund hold Stock Tokens, so the [issuer-risk disclosure](../risk/issuer.md) is part of onboarding.

## Choosing markets

Each vault is an isolated decision, and the [trade explorer](trade-explorer.md) publishes what you need to underwrite it:

* realised spread revenue and volume, by regime, since listing,
* current inventory mix and skew against the band,
* the market's tier parameters and caps,
* halt history with reasons.

Tier A markets turn over more at tighter spreads; Tier C earns wider spreads on thinner, gappier flow. There is no pooled product that chooses for you, deliberately.

## Depositing

Deposit USDG, the market's token, or both; the vault values the deposit at the guarded mid and mints shares. No lockups, no deposit fees. During the guarded launch each vault has a TVL cap, visible on its page; a full vault accepts no deposits until the cap is raised through the timelock.

## While deposited

Value per share accrues with every fill, live on your position page along with your share of current inventory. Things worth watching:

* **Skew.** A vault pinned at its band edge is one-sided and earning less; skew that never mean-reverts means flow in that market is persistently directional.
* **Regime mix.** Closed-regime fills carry the gap risk premium you are being paid; the explorer shows your vault's weekend fill share.
* **Halts.** A halted vault earns nothing while halted, and your withdrawal rights are unaffected.

## Exiting

Withdrawal burns shares for a pro-rata, in-kind mix of the vault's current USDG and tokens, in any vault state, always. Two consequences to plan for:

* You may receive more token and less USDG than you deposited (or the reverse), depending on the vault's mix at exit. Swapping the token leg back is an ordinary trade with ordinary costs.
* Exiting during `CLOSED` regime hands you inventory you cannot cheaply flatten until the underlying opens. Exits are always possible; well-timed exits are still worth timing.

## Taxes and accounting

Share value accrues price movement and spread revenue together, and in-kind withdrawal is a disposal event in most regimes. The explorer exports full per-position history as CSV; interpretation is between you and your adviser. Nothing in these documents is tax advice.
