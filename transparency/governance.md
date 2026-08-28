---
description: How parameters change, who can change them, and the limits on that power.
---

# Parameter governance

Everything tunable in ZircoFi is a `ParamController` value: spreads, multipliers, clips, caps, bands, fees, oracle adapters, eligibility issuers, tier assignments. Governance is the process for changing those values, and it is deliberately narrow: it can tune the venue, and it cannot touch anything else.

## The process

1. **Proposal.** The foundation multisig proposes a change with a written rationale: what changes, why, and the data behind it (usually the [execution quality](execution-quality.md) record or a listing review).
2. **Timelock.** The proposal is public and pending for the timelock period (48 hours for routine parameters; 7 days for oracle adapters, eligibility issuers and fee changes). Anyone affected can see it coming, and an LP who dislikes a pending change can withdraw before it executes, unconditionally.
3. **Execution.** The change executes on-chain, emitting old value, new value and proposal hash. The governance log in the [trade explorer](../users/trade-explorer.md) is the venue's complete parameter history.

## The emergency path

The guardian can pause quoting and settlement immediately, because a live incident cannot wait 48 hours. The asymmetry is the safety property: pausing is instant and harmless (withdrawals unaffected), while unpausing and every substantive change go through the timelock. A guardian action always produces an incident note, and an unexplained pause is itself a [commitments](commitments.md) breach.

## What governance cannot do

| Cannot | Because |
| --- | --- |
| Move or freeze user funds | No such function exists in any contract |
| Gate or delay withdrawals | Withdrawal is invariant-protected in every state |
| Change settlement logic | Logic is immutable; new logic is a new deployment users opt into |
| Apply fees retroactively | Fill-time fees are read at fill time |
| Bypass the band | The band check has no override path |
| Act instantly on substance | Everything but pause is timelocked |

## Who governs

Today, the foundation multisig, with named signers and published thresholds (see [Corporate structure](../compliance/corporate-structure.md)). The planned governance module inherits exactly the powers above and the same prohibitions; it is a change of who proposes, not of what proposing can do. There is no protocol token, and governance is not a yield product: the only thing at stake in a ZircoFi parameter vote is the quality of the venue.
