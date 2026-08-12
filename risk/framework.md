---
description: Every material risk in the venue, named, owned and mitigated.
---

# Risk framework

A venue that lists real-world assets carries real-world risks, and the honest posture is to enumerate them, say who bears each one, and document the mitigation. This section is that enumeration. Anything not listed here that should be is a documentation bug; report it.

## The map

| Risk | Borne by | Mitigation | Detail |
| --- | --- | --- | --- |
| Oracle failure or manipulation | LPs, traders | Guard stack, staleness bounds, cross-checks, the band, halts | [Oracles and market sessions](oracles.md) |
| Weekend and overnight gaps | LPs | Closed-regime spread multipliers and clip reductions | [Trading regimes](../protocol/trading-regimes.md) |
| Issuer credit and freeze rights | Everyone holding a Stock Token | Disclosure, bytecode review, tier caps, single-issuer exposure reporting | [Issuer risk](issuer.md) |
| Sequencer outage or censorship | Traders mid-flow, LPs | Recovery grace, forced inclusion via L1, halts | [Sequencer and chain risk](sequencer-and-chain.md) |
| Contract bugs | Everyone | Audits, invariant suite, bounty, guarded caps, minimal upgradeability | [Security programme](../architecture/security.md) |
| Adverse or toxic flow | LPs | Inventory bands, skew pricing, one-sided quoting, clips | [Anchor vaults](../protocol/anchor-vaults.md) |
| Quote-asset depeg | Everyone | Native MiCA-regulated issuer; disclosed as a concentration | [Quote asset: USDG](../assets/usdg.md) |
| Parameter error | LPs, traders | Timelock, published rationale, the band as backstop, guarded caps | [Parameter governance](../transparency/governance.md) |
| Maker misbehaviour | Takers | Signature verification, expiry, nonces, the band, attestation revocation | [RFQ settlement](../protocol/rfq.md) |
| Regulatory change | The venue | Boundary enforcement that can tighten by parameter, jurisdiction-gated access | [Compliance model](../compliance/model.md) |

## The band is the common backstop

Most failures above end at the same place: the oracle band. Whatever goes wrong upstream (a bad parameter, a compromised maker key, a misrouted quote), no fill can clear more than the band width from the last guarded oracle price. The band converts a long list of tail scenarios into a bounded per-fill cost, which is what makes the guarded launch caps meaningful: worst-case exposure is arithmetic, not hope.

## What the venue does not protect against

Being explicit about the residual is part of the framework:

* **Market risk.** A trader who buys tokenized NVDA owns NVDA exposure. The venue prices transactions; it does not insure positions.
* **Issuer insolvency.** Tier caps and disclosure bound and surface the exposure; they do not remove it.
* **A correct oracle reporting a violent market.** Halts trigger on staleness, pauses and implausible jumps, not on legitimate volatility. A crash prices through the venue as it prices through the underlying.

## How this section is maintained

Every incident, wherever it lands on this map, produces a published post-mortem and, where warranted, a parameter or design change through the timelock. The [Lessons from RWA trading](lessons.md) page records the industry's incidents and this venue's answers to each; the intent is that ZircoFi's own record is maintained to the same standard.
