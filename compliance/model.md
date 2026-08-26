---
description: The principle behind ZircoFi's compliance design and how it is enforced.
---

# Compliance model

{% hint style="info" %}
This section describes how compliance is enforced in the protocol. It is not legal advice.
{% endhint %}

## Principle

The protocol is non-custodial, and compliance is enforced at the **protocol boundary**, because the assets themselves carry no on-chain restrictions.

Stock Tokens are plain ERC-20s. Robinhood enforces eligibility at its interface and at KYC'd issuance and redemption. Anyone can hold the tokens on-chain, so a venue that lists them must decide for itself who may trade. ZircoFi makes that decision at every entry point through the [eligibility registry](../architecture/eligibility.md), and mirrors the issuer's own jurisdiction list rather than inventing a looser one.

## What is enforced

| Boundary | Rule |
| --- | --- |
| Swapping | `TRADER` attestation: KYC'd, non-restricted jurisdiction, mirroring the Stock Token eligibility list |
| Providing liquidity | `LP` attestation; professional clients at launch |
| Making markets | `MAKER` attestation; professional trading firms |
| Vault share transfers | Recipient must hold an `LP` attestation |
| Front-end access | Geo-fencing for restricted jurisdictions |

Withdrawal is never gated: an LP's exit is a property of holding shares, not a permission, because a compliance system that can trap funds has become a custodian.

## How it is enforced

1. **Attestations, not allowlists.** A KYC provider issues an EAS attestation to the wallet after identity, sanctions and residency checks. The attestation carries role, jurisdiction class and expiry, and no personal data.
2. **Continuous re-screening.** Attestations expire and are renewed; revocation is immediate and bites at the next action.
3. **Two screening layers.** The KYC provider screens at attestation and renewal; the sequencer applies its own sanctions screening (TRM Labs is integrated on the chain).
4. **Modular policy.** The registry is an adapter, so the policy engine (EAS, ONCHAINID, Chainlink ACE) and the accepted issuers can change through the timelock without redeploying the core. Rules can tighten quickly if a regulator or the issuer requires it.
5. **Permissionless reads.** Every view function stays open so that explorers and aggregators can index the protocol.

## What the protocol does not do

* It never holds user funds outside atomic settlement and the LP-owned vaults.
* It never converts fiat.
* It never exercises discretion over an individual fill. Quoting is a formula over public state, settlement is a signature check, and nobody at ZircoFi can improve, worsen or block a specific trade.

These properties are what keep the venue's regulatory posture narrow: the operating company faces users through the front-end and is assessed for authorisation where required, while the protocol itself remains non-custodial, non-discretionary software.

## Privacy

Personal data lives with the KYC provider and never touches the chain. The optional zero-knowledge credential path lets a wallet prove eligibility without revealing which provider checked it or any underlying attribute.

## Relationship to the issuer's rules

ZircoFi's trader eligibility mirrors the Stock Token eligibility list. If Robinhood tightens its list, the attestation issuer's rules follow. If Stock Tokens later gain on-chain transfer hooks (ERC-7943 or ERC-3643), the vaults and settlement contracts will honour them and will themselves be allowlisted. See [Asset roadmap](../assets/asset-roadmap.md).
