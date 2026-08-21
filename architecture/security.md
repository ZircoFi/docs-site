---
description: How the contracts and services are secured, tested and watched.
---

# Security programme

The security posture follows from the design: immutable logic, a small privileged surface, and a band that bounds the blast radius of almost anything. The programme's job is to make the remaining surface as small and as watched as possible.

## Audits

Two independent audits before mainnet exposure grows past the guarded caps: one firm focused on economic and pricing logic (the spread formula, share accounting, band enforcement), one general firm on the full contract set. Reports are published unredacted. Every subsequent change that touches settlement logic is a new deployment and gets its own review; parameter changes do not, which is much of why behaviour lives in parameters.

## Invariant and fuzz suite

The Foundry suite treats the protocol's promises as machine-checked properties, run in CI on every commit and fuzzed continuously:

| Invariant | Meaning |
| --- | --- |
| Band safety | No fill clears outside the oracle band, under any state, regime or parameter set the fuzzer can reach |
| Share monotonicity | Value per share never decreases from a swap; mint and burn rounding always favours the vault |
| Withdrawal liveness | `withdraw` succeeds in every vault state, including `HALTED` and `RETIRED`, with the pause active |
| Inventory bounds | Vault quoting can never push inventory past the band |
| Conservation | Every fill conserves value across trader, vault or maker, and `FeeCollector` exactly |
| Decimals | All arithmetic holds across the 6/18 decimal split, at the extremes of size |

Oracle-failure drills are first-class scenarios: stale rounds, paused feeds, divergent stream and feed, 25% jumps, and sequencer restarts each have expected halt behaviour asserted, not assumed.

## Bug bounty

A public programme on an Immunefi-style platform, live from mainnet, with the top tier reserved for anything that breaks an invariant above. The scope includes the quote service and SDK, since a client that misleads signing is a real attack even when the chain would enforce correctly.

## Operational security

* The timelock multisig and guardian keys are hardware-held by named signers with published thresholds; the guardian can only pause, and unpausing is timelocked, so a compromised guardian is a denial of quoting, not a loss.
* Services hold no keys that touch funds. The only hot keys in the system belong to makers, who bear their own key risk and whose damage is band-bounded.
* Monitoring alerts on band-margin compression, oracle divergence, skew saturation and abnormal fill patterns; the incident runbook's first step is always the same, pause quoting, because pausing is guaranteed harmless.

## Disclosure

Vulnerabilities: security@zircofi.com, with a published PGP key. Incidents produce a public post-mortem within 14 days, and any resulting change lands through the timelock with the post-mortem as its rationale. The venue's credibility is the [transparency commitments](../transparency/commitments.md) kept under pressure; this page is where that is tested.
