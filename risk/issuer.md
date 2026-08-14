---
description: The counterparty risk embedded in every tokenized security, and how the venue accounts for it.
---

# Issuer risk

A Stock Token is not a share. It is a tokenized debt security issued by Robinhood Assets (Jersey) Ltd that tracks a share held in US custody. That distinction is inside every position traded or held on ZircoFi, and the venue treats it as a first-class risk.

## What the risk is

| Component | Description |
| --- | --- |
| Credit risk | If the issuer became insolvent, token holders would be creditors with a claim on the custodied shares. Recovery could be delayed or partial. |
| Freeze and restriction | The issuer's terms reserve the right to suspend, freeze or restrict tokens in certain circumstances. If exercised, vault inventory and trader holdings could become untransferable. |
| Redemption terms | Primary redemption is KYC-gated at the issuer. The venue never relies on redemption; it relies on secondary-market pricing through the oracle. |
| Regulatory action | A regulator could require the issuer to change terms, restrict jurisdictions or halt issuance. |

## How the venue accounts for it

### Disclosure before exposure

The listing page for every market states the issuer, the legal nature of the token and the bytecode review finding. A trader's first swap and an LP's first deposit both pass a plain-language disclosure of the debt-claim structure. Issuer risk cannot be engineered away by a venue; it can be made impossible to hold unknowingly.

### Bytecode review before a market opens

The published documentation shows no on-chain freeze function on Stock Tokens. That is not enough. The deployed bytecode of every listed token is reviewed for pause, freeze, blacklist or forced-transfer roles before its market opens, and the finding is published on the listing page. Where such a role exists it is reflected in the tier assignment and the caps.

### Proof of reserve

Where a Chainlink Proof-of-Reserve feed or an equivalent attestation exists for the issuer's custodied shares, `OracleRouter` reads it and the listing page displays it next to the market. Markets without such a feed show that explicitly.

### Caps and a single-issuer number

Per-market TVL and volume caps bound each exposure. Because all Stock Tokens share one issuer, the [trade explorer](../users/trade-explorer.md) also reports total vault inventory exposed to Robinhood Assets (Jersey) Ltd as a single number, so the concentration is a published fact rather than a discovery.

### Isolation

An issuer event on one token, or on all Stock Tokens, cannot affect a market whose asset is a bridged treasury or gold token, because markets share nothing. Diversifying the issuer set is an explicit goal of the [Asset roadmap](../assets/asset-roadmap.md).

## What LPs and traders should know

Holding a Stock Token, in a wallet or through a vault share, is holding a claim on a regulated broker's custody arrangement, not the share itself. In an issuer failure the venue's contracts keep working (withdrawals in kind always function), but what is withdrawn is the claim, and its value is a legal question rather than a market one. The caps and disclosures are the venue's opinion of how to bound that; participants are free to be more conservative.
