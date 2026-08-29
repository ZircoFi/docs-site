---
description: How the venue makes money, and why the model needs no token.
---

# Business model

ZircoFi earns a small, itemised share of the value it delivers: fees on fills and a share of vault spread revenue. Nothing else. No token, no emissions, no payment for order flow, no market-making against users, no data sales.

## Revenue lines

| Line | Rate (initial) | Scales with |
| --- | --- | --- |
| Vault swap fee | 2 bps of notional | Volume |
| RFQ settlement fee | 2 bps of notional | Block volume |
| Vault spread share | 10% of realised spread | Volume × spread, so it pays more precisely when LPs earn more |

All three are `ParamController` values, timelocked, itemised on every ticket, and recorded in every fill event. See [Fees](../protocol/fees.md).

## The arithmetic

At 10M USDG average daily volume and a 12 bps average all-in half-spread: about 730K USDG per year from fees and about 438K from the spread share, roughly 1.2M USDG per year. The same arithmetic at 50M daily volume is about 5.8M per year. For calibration, tokenized stocks did about $9B of on-chain volume in the first eight months of 2026 across all venues; the model works at a modest share of a market growing several hundred percent a year.

## Why volume should come

* **Structural pricing edge on RWAs.** Anchored quotes do not drift and do not donate LP value to arbitrage, so the venue can sustain tighter spreads than generic pools at the same LP return. On aggregator-routed flow, the better quote simply wins.
* **The only 24/7 venue that prices sessions.** Weekend and overnight flow exists (the chain's tokens trade around the clock) and ZircoFi serves it at disclosed premiums instead of refusing it or mispricing it.
* **Distribution through the funnel.** The chain's users self-custody Stock Tokens out of a broker app; the wallet directory, aggregators and the API are the paths to them. See [Ecosystem integrations](../chain/ecosystem.md).
* **Infrastructure compounding.** Every wallet or agent that integrates the [API](../architecture/api.md) routes future flow without acquisition cost.

## Costs

Audits and the bounty, oracle and data services, RPC and indexing, the KYC provider's per-attestation fees, and the team. Fees accrue to the `FeeCollector` and fund these first; the [governance](../transparency/governance.md) log shows allocation once the module takes over.

## Why no token

A venue token would add a subsidy the pricing does not need, a regulatory surface the venue does not want, and an incentive to route decisions toward token holders rather than execution quality. The model above is a toll on real activity; it is small per fill, honest on every ticket, and it scales with exactly the thing the venue exists to maximise.
