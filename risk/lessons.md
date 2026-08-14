---
description: What the first years of tokenized-asset trading taught, and how each lesson is built into the venue.
---

# Lessons from RWA trading

On-chain rails change settlement, not market structure. The record from 2020 to 2026 shows precisely what happens when tokenized assets are traded on venues that ignore what they are. ZircoFi is designed around this record rather than in spite of it.

## Case studies

### FTX and CM-Equity tokenized stocks, 2020 to 2022

| | |
| --- | --- |
| What happened | FTX offered tokenized stocks through a custodial arrangement with CM-Equity. When FTX collapsed in November 2022, holders' "tokenized stocks" were claims in a bankruptcy, and the backing arrangements proved opaque. |
| Lesson | The venue's custody is the risk. A tokenized share held by a custodial exchange is only as good as the exchange. |
| In ZircoFi | The venue never takes custody. Assets sit in user wallets or in immutable vault contracts whose LPs can withdraw in kind in every state. There is nothing at the venue to lose. |

### Tokenized-equity launch dislocations, July 2025

| | |
| --- | --- |
| What happened | In the first weeks of tokenized stocks on Solana, thin DEX pools let tokens trade at large premiums to the underlying, in some episodes multiples of fair value, because pool prices were set by reserves and nothing connected them to the reference price. |
| Lesson | Reserve-ratio pricing has no opinion about fair value. On thin books it will quote anything. |
| In ZircoFi | Quotes are built from the Chainlink mid outward and the band makes it a contract-level invariant that no fill clears more than the band width from the reference price, however thin the market. |

### The standing cost of curve liquidity on reference-priced assets

| | |
| --- | --- |
| What happened | Not one incident but a structural drain: LPs in constant-product RWA pools are arbitraged on every reference-price move, a cost the literature calls loss-versus-rebalancing. For assets repriced continuously on an external exchange, passive curves systematically transfer LP value to arbitrageurs. |
| Lesson | For an asset priced elsewhere, a venue must consume the reference price, not rediscover it. |
| In ZircoFi | Anchor vaults quote around the oracle, so LP revenue is the spread on real flow rather than the residue after arbitrage. Skew pays for rebalancing explicitly instead of leaking it. |

### Edel Finance and wGOOGLx, July 2026

| | |
| --- | --- |
| Loss | About $403K of bad debt |
| What happened | The Chainlink oracle for tokenized Google stock was correct. A wrapper token's exchange rate was manipulated 78 times higher and the derived value was trusted. |
| Lesson | Price the exact token you hold. Any derived rate is an attack surface. |
| In ZircoFi | `OracleRouter` is configured with the feed for the token in the vault and nothing else. Wrappers, vault shares and derived rates are never listed and never priced. |

### Weekend gaps and 24/7 tokens

| | |
| --- | --- |
| What happened | Tokens that trade 24/7 over underlyings that trade 32 hours a week drift on weekends and gap at the open. Venues that quoted weekends at weekday spreads put their LPs on the wrong side of every Monday morning; venues that closed simply handed the hours back to custodial books. |
| Lesson | Sessions are a property of the asset. A venue must price them. |
| In ZircoFi | The regime engine widens spreads and shrinks clips when the underlying is closed, disclosed on the ticket, and halts on the oracle's own market status rather than a hard-coded calendar. |

## The common thread

Each failure came from one of three sources: custody at the venue, prices disconnected from the reference, or values derived instead of fed. ZircoFi removes all three at the design level:

1. **No custody**, ever: atomic settlement and in-kind withdrawal.
2. **No price without an anchor**: every fill bounded against the guarded Chainlink mid.
3. **No derived values**: the exact token, its own feed, or no listing.

The remaining risks (issuer, gaps, sequencer, contracts) are real, and they are documented in the rest of this section with the mitigation for each.
