---
description: The properties of Robinhood Chain that make it the right home for ZircoFi.
---

# Why Robinhood Chain

The decisive point is simple: Robinhood Chain's RWAs are plain, self-custodied ERC-20s with a regulated issuer and Chainlink feeds. That is the combination an oracle-anchored swap venue needs, and no other chain currently offers it with a broker's own distribution behind it.

## Property by property

Facts below are taken from Robinhood's documentation and the Arbitrum Foundation.

| Property | Robinhood Chain | Why it matters to ZircoFi |
| --- | --- | --- |
| Stack | Arbitrum Nitro dedicated chain (Orbit), rollup mode settling to Ethereum with blob data availability; 7-day canonical withdrawal | Ethereum security; standard Arbitrum tooling |
| Status | Mainnet since 1 July 2026 (chain ID 4663); about $540M TVL by mid-August 2026 (figures range from $536M to $1.4B across sources) | Live, with real assets and real users |
| Deployment | Permissionless, EVM-equivalent; Solidity and Vyper, Foundry and Hardhat; Stylus (Rust and WASM) available | No gatekeeping for venue contracts |
| Block time and fees | About 250 ms blocks, 100 ms latency through preconfirmations; median transaction about $0.001; gas paid in ETH | Quotes that are seconds old are still fresh; sub-cent settlement makes small swaps economic |
| Sequencer | Single Robinhood-operated sequencer, first-come-first-served, sanctions screening at the sequencer; forced inclusion through the L1 delayed inbox after 24 hours | Design for a 24-hour outage; Chainlink Sequencer Uptime Feed available |
| Native RWAs | **Stock Tokens**: ERC-20, 18 decimals, issued by Robinhood Assets (Jersey) Ltd, 1:1 backed by shares in US custody, 120+ countries | The tradeable universe, issued by a listed broker |
| Corporate actions | ERC-8056 `uiMultiplier()`: dividends reinvested, splits handled as a shares-per-token ratio | No dividend administration; no ex-dividend cliffs to misprice |
| Oracles | Chainlink Data Feeds (24/5), Data Streams (v11 RWA schema with `marketStatus`), CCIP, Sequencer Uptime Feed; multiplier baked into the price | Anchor pricing and session awareness from a production-grade source on day one |
| Stablecoins | USDG (Paxos, MiCA-regulated) native and already the chain's lending dollar on Morpho; USDe present; USDC via CCTP *unconfirmed* | The quote asset |
| Account abstraction | ERC-4337 EntryPoints v0.6 to v0.8, EIP-7702 (ArbOS 40); Alchemy Gas Manager, ZeroDev | Gasless onboarding; approve-and-swap in one user operation |
| Incumbent DeFi | Morpho Blue, Uniswap v2, v3 and v4, Lighter, Arcus, 1inch | Arbitrage participants and aggregator flow already on-chain |
| Compliance layer | **None on-chain** for Stock Tokens: no allowlist or transfer hook; eligibility enforced at Robinhood's UI and at KYC'd primary issuance and redemption | ZircoFi enforces eligibility itself at the protocol boundary |
| Ecosystem economics | Robinhood pays 10% of chain net revenue to the Arbitrum DAO; $1M Arbitrum Open House 2026 sponsorship | Grants and audit subsidies available to builders |

## What no other chain offers today

* Tokenized stocks on Solana (xStocks via Kraken distribution) trade mostly on centralised books and generic AMMs, and the issuers are third parties rather than the broker with the users.
* Tokenized stocks on Base (Coinbase, since 24 August 2026) are newer, and the venue layer around them is not yet built.
* Ethereum mainnet has the deepest DeFi but no native broker-issued equities, and gas costs that make small RWA swaps uneconomic.

Robinhood Chain has the assets, the pricing, the quote asset, the account abstraction and the fee profile in one place, plus the distribution of the broker that issues the assets.

## What the chain does not do for us

Being clear about the gaps is part of the case:

* It does not enforce Stock Token eligibility on-chain. ZircoFi does, through the [eligibility registry](../architecture/eligibility.md).
* It does not protect against a sequencer outage. ZircoFi does, through the recovery grace and the delayed inbox path. See [Sequencer and chain risk](../risk/sequencer-and-chain.md).
* It does not price closed-market gaps. ZircoFi does, through the regime engine. See [Trading regimes](../protocol/trading-regimes.md).
* Its incumbent DEXs price RWAs like any other token. That gap is the venue's reason to exist. See [Why now](../introduction/why-now.md).
