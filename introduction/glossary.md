---
description: Terms used throughout this documentation, defined once.
---

# Glossary

| Term | Definition |
| --- | --- |
| **Anchor vault** | A per-asset contract holding Stock Token and USDG inventory that quotes both sides of the market around the oracle mid. See [Anchor vaults](../protocol/anchor-vaults.md). |
| **Attestation** | An Ethereum Attestation Service record binding a wallet to a role (trader, LP, maker) after off-chain verification. Carries no personal data. See [Eligibility registry](../architecture/eligibility.md). |
| **Band** | The maximum distance from the oracle mid at which any fill may clear. A quote outside the band reverts. |
| **Clip** | The maximum size of a single swap against a vault. Above the clip, the router quotes through RFQ. |
| **Data Feeds** | Chainlink's push-based on-chain price feeds; the 24/5 equity feeds price Stock Tokens through the week. |
| **Data Streams** | Chainlink's pull-based low-latency price service (v11 RWA schema). Reports carry a `marketStatus` field used for session classification. |
| **ERC-8056 multiplier** | The `uiMultiplier()` value on a Stock Token reporting underlying shares per token. Dividends and splits are expressed through it, and Chainlink prices already include it. |
| **Execution quality** | The signed difference between a fill price and the oracle mid at inclusion, in basis points. Published for every fill. See [Execution quality](../transparency/execution-quality.md). |
| **Half-spread** | The distance between the mid and one side of a quote. ZircoFi parameters are stated as half-spreads in basis points. |
| **Inventory skew** | How far a vault's holdings have drifted from its target mix. Skew moves quotes to attract rebalancing flow. |
| **Maker** | An attested market maker that signs RFQ quotes off-chain and settles them atomically on-chain. |
| **Mid** | The current guarded oracle price for an asset, from `OracleRouter`. All quoting starts here. |
| **Quote asset** | USDG, the asset every market trades against and every fee is denominated in. |
| **Regime** | The state a market is in: `OPEN`, `EXTENDED`, `CLOSED` or `HALTED`. Determines spread multipliers and clip sizes. See [Trading regimes](../protocol/trading-regimes.md). |
| **RFQ** | Request for quote. Signed EIP-712 quotes from makers, valid for seconds, settled atomically by the taker. See [RFQ settlement](../protocol/rfq.md). |
| **Router** | The single entry point (`SwapRouter`) that checks eligibility, compares vault and RFQ pricing and settles the better one. |
| **Session** | The underlying market's state (regular, extended hours, closed), as reported by the oracle. Regimes are derived from sessions. |
| **Stock Token** | A tokenized equity issued by Robinhood Assets (Jersey) Ltd: an ERC-20 debt security backed 1:1 by shares in US custody. See [Stock Tokens](../assets/stock-tokens.md). |
| **Tier** | An asset's listing class (A, B or C), which sets its base spread, clip, band and caps. See [Listing framework](../assets/listing-framework.md). |
| **USDG** | The Paxos Global Dollar, a MiCA-regulated stablecoin native to Robinhood Chain. See [Quote asset: USDG](../assets/usdg.md). |
| **Vault share** | The ERC-4626-style token an LP receives on deposit, representing a pro-rata claim on a vault's inventory and accrued spread revenue. |
