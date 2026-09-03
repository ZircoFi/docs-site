---
description: The legal structure behind the protocol and the company that operates the platform.
---

# Corporate structure

## Two entities, one protocol

| Entity | Role |
| --- | --- |
| **ZircoFi Foundation** | Holds protocol governance and the immutable contracts. Controls the timelocked parameter multisig and the guardian until the governance module takes over. |
| **ZircoFi Labs** (operating company) | Builds and runs the platform, the quote service, the indexer and the keeper software. Employs the team. Holds the licence where one is required. |

## Why they are separate

* The contracts are immutable and non-custodial. Placing them under a foundation keeps protocol governance separate from the commercial incentives of the company that builds around it.
* The operating company is the entity that faces users through a front-end and is licensed where required. Keeping it distinct limits the regulatory surface of the protocol itself.
* If the operating company ceased, the venue would continue: the vaults quote from on-chain state, withdrawals are unconditional, and anyone can run the quote service, keeper, indexer and front-end from the open-source code. What would degrade is convenience and the pace of new listings, not the market.

## Governance path

1. **Today:** parameter changes go through a timelocked multisig held by the foundation, with every proposal published with a rationale.
2. **Next:** a governance module takes over parameter control. It preserves the properties that matter: the timelock, the public rationale, no fund access, no ability to gate withdrawals, and no ability to touch an individual fill.

The $ZIRCO token is live on Robinhood Chain at `0x13a03170db84842fa4c957bad44d852c3169e1e5`. See [Parameter governance](../transparency/governance.md) and [Business model](../resources/business-model.md).
