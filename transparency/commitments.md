---
description: The specific, checkable promises the venue makes, and what checking each one looks like.
---

# Commitments

Transparency claims are cheap; commitments are checkable. Each promise below names its verification path. If any of them ever fails verification, that is an incident, handled as one.

## Pricing

* **Every fill is itemised on-chain.** The fill event carries mid, spread, skew and fee. Check: any fill on Blockscout against its receipt.
* **No fill outside the band.** A contract invariant, not a policy. Check: the invariant suite is public; the explorer flags band margins per fill.
* **Execution quality is published, unfiltered.** Every fill's distance from the oracle mid, aggregated daily, downloadable raw. Check: [Execution quality](execution-quality.md), recomputable from events.
* **No payment for order flow, no privileged routing.** The router settles the best verifiable price, on-chain, per fill. Check: re-derive any fill's venue comparison from the event's oracle round and vault state.

## Funds

* **No custody, ever.** Settlement is atomic; vault inventory belongs to LPs. Check: there is no contract function that moves user assets except settlement and withdrawal; the audits attest it.
* **Withdrawals are unconditional.** In every vault state, with every pause active. Check: invariant suite; halt-state withdrawals appear in the explorer like any others.

## Change control

* **Every parameter change is timelocked and published with a rationale before it executes.** Check: the governance log lists proposal, rationale, window and execution for the venue's entire history.
* **No retroactive fees.** The fee at fill time is the fee paid. Check: fill events carry the fee applied.
* **Settlement logic never changes under an existing deployment.** New logic means a new deployment users opt into. Check: bytecode verification against tagged source, asserted in CI, reproducible by anyone.

## Disclosure

* **Listings publish their evidence.** Tier assignment data, bytecode review findings and issuer disclosures, per market, before the market opens. Check: the listing record in the explorer.
* **Incidents produce public post-mortems within 14 days.** Check: the incident log; absence of a post-mortem after an incident is itself a breach.
* **These documents state risks plainly.** Issuer risk, gap risk and oracle risk are in the reading path of every participant, not in an appendix. Check: read [Risk](../risk/framework.md).

## What is deliberately not promised

Best execution against every venue everywhere, profitability of any position, or uptime of convenience services. The promises above are the ones the design can actually enforce, which is why they are the ones made.
