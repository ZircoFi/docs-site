---
description: What the protocol charges, who pays it and where it goes.
---

# Fees

ZircoFi charges explicit fees at the point where value is delivered, itemised on every ticket. There is no hidden markup, no payment for order flow and no token emission.

| Fee | Rate (initial) | Paid by | When |
| --- | --- | --- | --- |
| Vault swap fee | 2 bps of notional, inside the quoted all-in price but itemised | Trader | Per fill |
| Vault spread share | 10% of the vault's realised spread | LPs (netted from spread revenue) | Per fill |
| RFQ settlement fee | 2 bps of notional | Trader | Per RFQ fill |
| Deposit and withdrawal | None | | |

A two-leg token-to-token swap pays each leg's fee. All rates are `ParamController` values, changeable only through the timelock with a published rationale, and never applied retroactively: the fee in force at fill time is the fee paid.

## The quote is the invoice

Every ticket shows mid, spread and fee as separate numbers in bps and USDG before signing, and the fill event records the same breakdown on-chain. If a fee changes through the timelock, the change is visible in the governance log before it is visible on a ticket.

## Where fees go

Fees accrue to the `FeeCollector` contract and fund audits, the bug bounty, oracle and infrastructure costs, and operations. The governance module directs allocation once it takes over parameter control. See [Parameter governance](../transparency/governance.md).

## Economics at scale

At 10M USDG average daily volume with an average all-in half-spread of 12 bps:

* Swap and RFQ fees: 10M × 2 bps × 365 ≈ 730K USDG per year.
* Spread share: 10M × 12 bps × 10% × 365 ≈ 438K USDG per year.

Roughly 1.2M USDG per year at volumes that are modest against the asset class's growth. The same arithmetic at 50M daily is about 5.8M USDG per year. See [Business model](../resources/business-model.md).
