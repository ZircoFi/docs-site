---
description: The asset classes ZircoFi will list after Stock Tokens, and what each requires first.
---

# Asset roadmap

The venue expands by asset class, in the order that the listing requirements can actually be met on Robinhood Chain. Each stage is gated on infrastructure facts, not announcements.

## Stage 1: the Stock Token long tail (live and expanding)

Tier A opened first; Tier B and C markets open in batches as feeds are confirmed and bytecode reviews complete. The long tail is where an RWA-native venue matters most: these are exactly the assets that pooled designs cap aggressively or exclude, and that thin generic pools misprice worst. See the [Listing framework](listing-framework.md).

## Stage 2: tokenized treasuries

Tokenized US Treasuries are the largest transferable RWA category after stablecoins (~$16B), and the natural second asset class: a trader rotating out of equity exposure should be able to rotate into yield without leaving the venue.

Requirements before the first treasury market opens:

* A treasury token deployed on Robinhood Chain, natively or bridged via CCIP, from a regulated issuer with a freely transferable design (USDY-class rather than allowlist-gated).
* A NAV-consistent Chainlink feed for the exact token on chain 4663.
* The [listing checklist](listing-framework.md), plus the NAV addendum: NAV-type assets quote within a tight band around NAV, with the closed-regime logic replaced by the fund's own accrual behaviour, since treasuries have no weekend gap in the equity sense.

If the token is transfer-restricted at the contract level, the vault deploys in its permissioned variant and is allowlisted by the issuer, with eligibility enforced by both the issuer's hooks and ZircoFi's registry.

## Stage 3: tokenized gold

PAXG-class tokenized gold trades 24/7 against a spot reference with no session structure, which makes it the simplest asset the venue will ever list: `OPEN` around the clock, spread driven by feed cadence and inventory alone. The requirement is the same as everywhere: the exact token bridged to chain 4663 with its own Chainlink feed, and a bridge review covering the mint path, the custodian attestations and pause behaviour.

## Stage 4: whatever the chain issues next

Robinhood has signalled private-market tokens and further RWA classes on its chain. Anything that arrives is evaluated against the same checklist. Assets that cannot meet it (no per-token feed, no session signal, undisclosed issuer terms) are not listed, whatever their demand.

## What will not be listed

* Wrappers, vault shares or receipt tokens whose price would be derived rather than fed. Pricing the exact token is a hard rule; the July 2026 wGOOGLx incident is the case study in [Lessons from RWA trading](../risk/lessons.md).
* Synthetics without a regulated issuer and 1:1 backing.
* Any asset whose bytecode review finds undisclosed transfer controls.
