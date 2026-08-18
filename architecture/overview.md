---
description: The full system, on-chain and off, and the trust relationships between the parts.
---

# System overview

ZircoFi is a small set of immutable contracts on Robinhood Chain, surrounded by stateless services that make them convenient. The rule that shapes everything: **the chain enforces, services only assist.** Every service can disappear and every guarantee still holds; what is lost is convenience, never funds or fairness.

## The pieces

```
          Traders, LPs, Makers, Integrators
                        │
        ┌───────────────┼────────────────────┐
        │               │                    │
   Platform UI     Quote service        Maker gateways
   (Next.js)       (stateless)          (RFQ streaming)
        │               │                    │
        └───────────────┼────────────────────┘
                        │  signed transactions only
   ┌────────────────────▼─────────────────────────┐
   │              Robinhood Chain                  │
   │                                               │
   │  SwapRouter ── AnchorVaults ── RfqSettlement  │
   │       │              │               │        │
   │  EligibilityRegistry │        FeeCollector    │
   │       │         OracleRouter                  │
   │  ParamController (timelock)                   │
   └───────────────────────────────────────────────┘
                        │
        Indexer (Ponder / Envio) ── Trade explorer,
        execution-quality dashboards, keeper service
```

## On-chain

The contract set and each contract's single job are documented in [Smart contracts](smart-contracts.md). The design properties that hold across all of them:

* **Immutable logic, timelocked parameters.** Vault and settlement logic does not upgrade; behaviour changes only through `ParamController` values behind the timelock, each with a published rationale.
* **One oracle door.** No contract reads a price except through `OracleRouter` and its guards.
* **One eligibility door.** No contract decides who may act except through the `EligibilityRegistry`.
* **Pause can only stop.** The guardian can halt quoting and settlement; nothing can block withdrawals or move funds.

## Off-chain

Three services, all stateless with respect to funds:

* **Quote service**: simulates router pricing and aggregates maker quotes for the UI and API. It cannot alter a fill; the router re-derives or re-verifies everything on-chain. See [Off-chain services](off-chain-services.md).
* **Keeper service**: pokes regime transitions and halt conditions. Anyone can run one; the functions are permissionless and the reference implementation is open source.
* **Indexer**: builds the trade explorer and execution-quality datasets from chain events. Wrong indexing can misdisplay, never mis-settle.

## Trust map

| Party | Must be trusted for | Cannot do |
| --- | --- | --- |
| Chainlink | Correct prices and market status, within the guard stack | Move funds; a bad price is bounded by the band and halts |
| Robinhood (sequencer) | Liveness and ordering | Steal funds; censorship is bounded by forced inclusion |
| Robinhood Assets (issuer) | Stock Token backing and terms | Touch the venue's contracts; see [Issuer risk](../risk/issuer.md) |
| Attestation issuer | Correct eligibility decisions | Price, settle or hold anything |
| ZircoFi Labs (services) | Convenience and uptime | Misprice a fill, take custody, or block a withdrawal |
| Timelock multisig | Parameter changes, after the delay, in public | Move funds, block withdrawals, or act instantly |

## Failure stance

Every component has a designed failure response: oracles halt, the sequencer has a grace path, services degrade to direct contract interaction, parameters are bounded by the band. The complete enumeration is the [Risk framework](../risk/framework.md).
