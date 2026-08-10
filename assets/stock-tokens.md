---
description: What Robinhood Stock Tokens are, and the properties that matter when they are traded.
---

# Stock Tokens

Robinhood Stock Tokens are the launch assets for ZircoFi. They are the reason the venue exists on Robinhood Chain rather than elsewhere: tokenized equities issued by a listed broker, backed 1:1, tradeable 24/7 as ordinary ERC-20s, and priced by Chainlink.

## What they are

| Property          | Detail                                                                                                                                                       |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Issuer            | Robinhood Assets (Jersey) Ltd                                                                                                                                |
| Legal nature      | Tokenized **debt securities**. Each token is a claim on the issuer that tracks the underlying share, not the share itself.                                   |
| Backing           | 1:1 by shares held in US custody                                                                                                                             |
| Standard          | ERC-20, 18 decimals, plus ERC-8056 corporate-action interface                                                                                                |
| Availability      | 120+ countries.                                                                                                                                               |
| Transfer controls | **None on-chain.** No allowlist, no transfer hook, no on-chain freeze function visible in the documentation. Eligibility is enforced at Robinhood's UI and at KYC'd primary issuance and redemption. |
| Pricing           | Chainlink Data Feeds (24/5) and Data Streams with market status; the ERC-8056 multiplier is baked into the price                                              |

## What the properties mean for trading

* **Plain ERC-20s** means the vaults and RFQ settlement need no issuer allowlisting to hold and move them, and any attested wallet can trade without per-token ceremony.
* **ERC-8056 dividends and splits** mean value accretes into the token and its feed, so there are no dividend claims to administer and no ex-dividend cliffs to misprice. See [Corporate actions and dividends](../protocol/corporate-actions.md).
* **Chainlink pricing with market status** is what makes anchor quoting and [trading regimes](../protocol/trading-regimes.md) possible at all: the mid and the session arrive from the same guarded source.
* **No on-chain compliance** cuts both ways. Any address can hold the tokens, including addresses that would not pass Robinhood's own screen, which is precisely why ZircoFi enforces eligibility itself at the protocol boundary through the [eligibility registry](../architecture/eligibility.md). Every trader, LP and maker is checked on every relevant action.

If Stock Tokens later adopt ERC-7943 (uRWA) or ERC-3643 hooks, the vaults and settlement contracts are written to call `canTransfer` and `canReceive` defensively and will themselves need to be allowlisted by the issuer. See [Asset roadmap](asset-roadmap.md).

## Issuer and freeze risk

Two risks are inside every Stock Token position, and the venue discloses and prices them rather than ignoring them:

* **Issuer credit risk.** The token is a debt claim on Robinhood Assets (Jersey) Ltd. If the issuer fails, the token's value depends on recovery from custody, not on the share price. Vault LPs and traders holding tokens both carry this claim.
* **Freeze rights.** The issuer's terms reserve the right to suspend, freeze or restrict tokens in certain circumstances. The published documentation shows no on-chain freeze function. The deployed bytecode of every listed token is reviewed for pause, freeze, blacklist and forced-transfer roles before its market opens, and the finding is published on the listing page. Where such a role exists it is reflected in the tier assignment and caps.

See [Issuer risk](../risk/issuer.md).

## What is listed

Tier A includes tokenized SPY, QQQ, AAPL, MSFT and NVDA. The full tier assignment, and the criteria a token must meet before a market opens, are in the [Listing framework](listing-framework.md).
