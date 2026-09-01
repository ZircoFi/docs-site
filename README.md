---
description: Swap tokenized real-world assets on Robinhood Chain, with every quote, fill and fee settled and inspectable on-chain.
---

# What is ZircoFi

ZircoFi is a non-custodial swap venue for tokenized real-world assets on Robinhood Chain. Traders swap Stock Tokens (tokenized AAPL, NVDA, SPY and so on), and over time tokenized treasuries and gold, against USDG at prices anchored to the same Chainlink feeds that price the assets everywhere else. Every quote, every fill and every fee settles on Robinhood Chain, where anyone can inspect it.

## The idea in one paragraph

A generic AMM discovers price from its own reserves. That is the right design for assets whose price lives on-chain, and the wrong one for assets whose authoritative price lives on an exchange. ZircoFi quotes the other way around: each market is an [anchor vault](protocol/anchor-vaults.md) that prices every swap at the Chainlink mid plus a spread that reflects the market session, the vault's inventory and the asset's tier. When the underlying market is closed, spreads widen and trade sizes shrink, and the ticket says so. Trades above the vault's clip route to professional market makers through [RFQ](protocol/rfq.md), signed off-chain and settled atomically on-chain. The [router](protocol/routing.md) picks the better price per trade, and the trader sees one quote with the mid, the spread and the fee shown separately.

## What makes it different

| Property           | Generic AMM pool                                | ZircoFi                                                              |
| ------------------ | ----------------------------------------------- | -------------------------------------------------------------------- |
| Price discovery    | Reserve ratio                                   | Anchored to the Chainlink mid, bounded by guards                     |
| Closed markets     | Quotes Saturday as if it were Tuesday           | Wider spreads and smaller clips, disclosed on the ticket             |
| Corporate actions  | Pool breaks or needs a migration                | ERC-8056 multiplier in the price; trading halts during the event     |
| LP economics       | Adverse selection on every reference-price move | Spread income from oracle-banded quotes                              |
| Large trades       | Price impact along the curve                    | Competitive RFQ from attested market makers                          |
| Compliance         | None                                            | Eligibility enforced at the protocol boundary                        |
| Fees               | Inside the curve                                | Itemised on every quote, published on-chain                          |

Constant-product pools are the right tool for crypto-native pairs. For assets with an authoritative external price, defined trading hours and corporate actions, they leak value to arbitrageurs and drift from fair value on thin weekends. RWA-native market structure is what those assets need, and that gap is ZircoFi.

## Key facts

| | |
| --- | --- |
| Chain | Robinhood Chain (Arbitrum Nitro L2, chain ID 4663) |
| Quote asset | USDG (Paxos Global Dollar) |
| Launch assets | Robinhood Stock Tokens |
| Next assets | Long-tail Stock Tokens, then tokenized treasuries and gold (see the [Asset roadmap](assets/asset-roadmap.md)) |
| Execution | Oracle-anchored vaults for standard size, RFQ for block size, one router |
| Pricing | Chainlink Data Feeds and Data Streams, session-aware, guarded |
| Custody | None. Funds move only at settlement; vault inventory is held by immutable contracts |
| Fees | A single itemised protocol fee; no token emissions, no hidden spread share |

## What ZircoFi does not do

* Hold custody of user assets or convert fiat.
* Issue, mint or redeem RWAs. ZircoFi is secondary-market infrastructure over assets issued by regulated issuers.
* Offer margin, leverage or perpetuals.
* Issue a protocol token.

## Where to go next

* New to the venue: start with [Why now](introduction/why-now.md) and [Design principles](introduction/design-principles.md).
* Want the mechanics: read the [Protocol overview](protocol/overview.md), then [Anchor vaults](protocol/anchor-vaults.md), [Pricing and spreads](protocol/pricing-and-spreads.md) and [Trading regimes](protocol/trading-regimes.md).
* Building on or integrating with ZircoFi: see [System overview](architecture/overview.md), [Smart contracts](architecture/smart-contracts.md) and [API and SDK](architecture/api.md).
* Assessing risk: see the [Risk framework](risk/framework.md) and [Lessons from RWA trading](risk/lessons.md).
* Trading or providing liquidity: see [For traders](users/traders.md) and [For liquidity providers](users/liquidity-providers.md).
