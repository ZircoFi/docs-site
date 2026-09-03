---
description: The eight principles every ZircoFi design decision is tested against.
---

# Design principles

## 1. The reference price is the truth

For an RWA, the authoritative price is made on the venue where the underlying trades. ZircoFi never pretends otherwise. Every quote is built from the Chainlink mid outward, every fill is bounded against it, and [execution quality](../transparency/execution-quality.md) is measured and published against it. The protocol's job is not to discover the price of Apple; it is to deliver that price on-chain, around the clock, with the cost of doing so stated plainly.

## 2. Honest quotes

A quote shows the mid, the spread and the protocol fee as separate, labelled numbers. The session badge sits on the ticket, not in a tooltip: when US markets are closed, the trader reads "closed session, spreads widened" before signing, never after. There is no payment for order flow, no hidden markup and no fee inside the curve. If the venue's economics cannot survive being itemised, they are the wrong economics.

## 3. Non-custodial, always

ZircoFi never holds user assets. Traders self-custody and sign every swap; vault inventory belongs to LPs and is held by immutable contracts; RFQ settlement moves assets directly between maker and taker. The emergency pause can stop new swaps; it can never touch funds or block an LP withdrawal.

## 4. Compliance at the protocol boundary

Stock Tokens carry no on-chain transfer restrictions, so a venue that lists them must decide for itself who may trade. ZircoFi enforces eligibility at every entry point through attestations, without putting personal data on-chain, and tells an ineligible user why before they sign rather than reverting after. See the [Compliance model](../compliance/model.md).

## 5. Sessions are first-class

An equity token is a different asset at 3pm on Tuesday and 3am on Sunday, and the protocol prices the difference instead of ignoring it. Spreads, clip sizes and halts are all functions of the [trading regime](../protocol/trading-regimes.md), driven by the oracle's market-status field, never by guesswork.

## 6. Isolation per market

Each listed asset is its own vault with its own inventory, parameters and LPs. A dislocation, halt or delisting in one market cannot touch another. There are no shared pools and no socialised losses.

## 7. Everything is inspectable

Every fill links to its transaction on Blockscout. Every parameter is a public contract read. Every parameter change goes through a timelock with a published rationale. The [trade explorer](../users/trade-explorer.md) exists so that "transparently on-chain" is a verifiable claim rather than a slogan.

## 8. Design for the chain's failure modes

A single sequencer can go down while the underlying market keeps moving. Oracles can go stale. Corporate actions pause feeds. Each of these has a designed response (halt, widen, grace period) documented in the [Risk](../risk/framework.md) section, because a venue that only works when everything works is not infrastructure.

## What these principles rule out

* No emissions-funded liquidity. Liquidity is paid from real spread revenue or not at all.
* No leverage, margin or liquidations. The venue does one thing.
* No discretionary intervention in pricing. Parameters change through the timelock; quotes come from the formula.
