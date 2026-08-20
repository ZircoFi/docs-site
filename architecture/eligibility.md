---
description: How the venue enforces who may trade, provide liquidity and make markets, without putting personal data on-chain.
---

# Eligibility registry

Stock Tokens carry no on-chain transfer restrictions. Robinhood enforces eligibility at its own interface and at KYC'd issuance and redemption. A venue that lists those tokens therefore has to enforce eligibility itself, and ZircoFi does so at the **protocol boundary**: every swap, every deposit, every RFQ settlement and every share transfer is checked.

## Roles

| Role | Who | Status |
| --- | --- | --- |
| `TRADER` | KYC'd individuals and entities in non-restricted jurisdictions, mirroring the Stock Token eligibility list | Live |
| `LP` | Liquidity providers; professional clients at launch, given the inventory exposure vaults carry | Live |
| `MAKER` | Professional market makers admitted to RFQ | Live |
| `RELAYER` | Services that submit swaps on behalf of users (account-abstraction bundlers, integrators) | Live |

## Attestations

Eligibility is backed by **Ethereum Attestation Service** attestations, compatible with ONCHAINID, issued by the KYC provider after identity, sanctions and residency checks.

An attestation contains:

* the wallet address,
* the role,
* a jurisdiction class (not the country, unless the user opts to disclose it),
* an investor class where relevant,
* an expiry.

It contains no name, no document and no personal identifier. Attestations expire and are renewed on continuous re-screening; revocation takes effect immediately.

### Zero-knowledge path

An optional credential path (Privado ID or zkPass) lets a wallet prove "eligible in jurisdiction class X, investor class Y" without revealing which provider checked it or any underlying attribute. The registry accepts either form.

## The check

```solidity
function isEligible(address account, bytes32 role) external view returns (bool);
```

`SwapRouter`, `AnchorVault` and `RfqSettlement` call this for every relevant party. A failing check reverts, and the front-end resolves the same view before signing so an ineligible user is told why up front rather than paying gas to find out. View functions stay permissionless so that indexers, explorers and aggregators can read everything.

### Where the check runs

| Action | Checked parties |
| --- | --- |
| `swap` | Trader (`TRADER`); recipient if different |
| RFQ `settle` | Taker (`TRADER`), maker (`MAKER`) |
| Vault `deposit` | Depositor (`LP`) |
| Vault `withdraw` | Never gated beyond share ownership: withdrawal is a property, not a permission |
| Vault share transfer | Recipient (`LP`) |
| Relayed submission | Relayer (`RELAYER`), plus the underlying user's own role |

## Adapters

The registry is an adapter interface so that the policy engine can change without touching the core:

* **EAS adapter** (launch): reads attestations by schema and issuer.
* **ONCHAINID adapter**: for ERC-3643-compatible identities.
* **Chainlink ACE / CCID adapter**: an alternative policy engine that can be enabled through the timelock.

The list of accepted issuers and schemas is a `ParamController` value, so tightening or replacing a provider is a timelocked, logged change rather than a redeploy.

## Permissioned assets

If Stock Tokens or bridged RWAs adopt ERC-7943 (uRWA) or ERC-3643 hooks, the vaults and `RfqSettlement` call `canTransfer` and `canReceive` defensively and are themselves allowlisted by the issuer. The token's own rules and the venue's rules are both satisfied without either having to trust the other. See [Asset roadmap](../assets/asset-roadmap.md).

## Geo-fencing

Restricted jurisdictions are excluded in layers:

1. Attestations are not issued to wallets in restricted jurisdictions.
2. A jurisdiction self-attestation is required on every wallet at onboarding.
3. The front-end applies IP-based geo-fencing for restricted jurisdictions.
4. The sequencer applies its own sanctions screening.

The restricted list mirrors the Stock Token issuer's own availability and is maintained by the attestation issuer; see the [Compliance model](../compliance/model.md).
