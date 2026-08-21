---
description: Where the contracts live, how deployments are verified, and how addresses are published.
---

# Deployment

{% hint style="warning" %}
ZircoFi contract addresses are published only in `contracts/deployments/4663.json` in the open-source repository, on this page's linked registry, and at [zircofi.com](https://zircofi.com). Addresses circulating anywhere else should be treated as hostile. Verify before approving anything.
{% endhint %}

## Deployments

| Network | Chain ID | Status |
| --- | --- | --- |
| Robinhood Chain mainnet | 4663 | Production |
| Robinhood Chain testnet | Published in the repository | Persistent staging; every release runs here first |

The testnet deployment tracks mainnet parameters and runs against Robinhood's testnet Stock Tokens and faucet assets, so integrators can exercise the full path (attestation, quote, swap, withdrawal) without value at risk.

## The deployment record

`contracts/deployments/4663.json` records, for every contract: address, source commit, compiler settings, constructor arguments, and the deployment transaction. All contracts are verified on Blockscout, and a CI job asserts that on-chain bytecode matches the tagged source on every release, so "the audited code is the deployed code" is a checkable claim rather than a promise.

## Release process

1. Tag, audit or review delta, and publish the source.
2. Deploy to testnet; run the invariant suite and a scripted end-to-end pass against the live testnet oracles.
3. Deploy to mainnet with `CREATE2` for address stability across environments.
4. Wire parameters through the timelock (never in constructors, so the governance log is complete from block one).
5. Update the deployment record and verification, then announce.

Because logic is immutable, a "release" after launch means either a new market from the factory (parameters only, no new code) or a new protocol version deployed alongside the old one, with LPs migrating by choice as described in [Smart contracts](smart-contracts.md). Old versions are never switched off remotely; there is nothing to switch.

## Configuration authenticity

Everything a client needs (addresses, market list, tiers, oracle adapters) is readable from `ParamController` and the factory on-chain. The SDK ships with no baked-in market list; it reads the chain, so a stale or tampered client configuration cannot point users at wrong markets without failing signature and address checks.
