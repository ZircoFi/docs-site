---
description: What is live, what is capped, and the order in which the venue grows.
---

# What is live and what is next

The venue expands in phases, and each phase gate is a published criterion rather than a date: caps rise and features ship when the record supports them. Dates below are targets; the gates are the commitments.

## Phase 0: testnet (complete)

Full protocol on Robinhood Chain testnet against Robinhood's testnet Stock Tokens and faucet: vaults, RFQ, router, eligibility, explorer. The testnet deployment persists as staging; every release passes through it. See [Deployment](../architecture/deployment.md).

## Phase 1: guarded mainnet (live)

* Tier A markets: tokenized SPY, QQQ, AAPL, MSFT, NVDA against USDG.
* Anchor vaults with launch parameters, TVL and daily volume caps per the [listing framework](../assets/listing-framework.md).
* RFQ live with founding makers; trader, LP and maker attestations issuing.
* Trade explorer and the [execution quality](../transparency/execution-quality.md) dataset publishing from the first fill.

**Gate to Phase 2:** two clean audits published, 60 days of execution-quality history with median `OPEN` fills inside tier targets, and no band or withdrawal invariant events.

## Phase 2: catalogue and caps

* Tier B and C Stock Token markets in batches, as feeds and bytecode reviews complete: the long tail is the point.
* Cap raises through the timelock, each citing the accumulated record.
* Public LP onboarding beyond the founding group, per-jurisdiction as the [compliance](../compliance/model.md) work lands.
* Portfolio surfaces complete: cost basis, share-term display, LP analytics, CSV export.

**Gate to Phase 3:** sustained two-sided vault performance across tiers through at least one high-volatility week and one long weekend, without manual intervention.

## Phase 3: infrastructure

* Public [API and SDK](../architecture/api.md) out of beta; aggregator and wallet integrations; agentic order flow, given the chain's direction, treated as a first-class integrator.
* Cross-chain listings begin per the [asset roadmap](../assets/asset-roadmap.md): tokenized treasuries, then gold, via CCIP, each through the full listing checklist.
* Execution algorithms for working large orders over time (TWAP against the vault path).

## Phase 4: governance handover

The governance module takes over parameter control from the foundation multisig, preserving the timelock, the published rationale and the [prohibitions](../transparency/governance.md). The handover changes who proposes, not what proposing can do.

## What is deliberately not on this roadmap

Leverage, margin, perpetuals, lending, or a protocol token. Adjacent products are other protocols' jobs; the venue's ambition is to be the place where RWAs trade properly, and the roadmap spends every phase on exactly that.
