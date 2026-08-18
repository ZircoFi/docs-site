---
description: The protocols and services ZircoFi integrates with on Robinhood Chain.
---

# Ecosystem integrations

ZircoFi is designed to interoperate with what already exists on Robinhood Chain rather than rebuild it. Each integration below is either in the settlement path or in the surrounding services.

## Chainlink

**Role:** pricing, market status, sequencer status, and a candidate eligibility policy engine.

Data Feeds and Data Streams provide 24/5 equity pricing with the ERC-8056 multiplier baked in, plus the `marketStatus` field the regime engine runs on. The Sequencer Uptime Feed drives the post-outage grace. CCIP is the bridge path for the treasury and gold listings on the [Asset roadmap](../assets/asset-roadmap.md). Chainlink ACE and CCID are a candidate adapter for the eligibility registry once generally available on chain 4663. See [Oracles and market sessions](../risk/oracles.md).

## Paxos and USDG

**Role:** quote asset.

USDG is native to the chain and MiCA-regulated, and it is already the dollar of the chain's lending markets, so traders arrive holding it. Co-marketing with Paxos is part of the go-to-market plan. See [Quote asset: USDG](../assets/usdg.md).

## Uniswap, 1inch and aggregators

**Role:** arbitrage flow and aggregator distribution.

Uniswap v2, v3 and v4 and 1inch are deployed on the chain. Arbitrageurs keeping generic pools in line with ZircoFi's anchored quotes are welcome flow, and skew pricing pays them to rebalance vaults. Aggregator integration through the public router interface is a distribution goal: ZircoFi wins routed flow whenever its anchored quote beats a curve.

## Morpho Blue

**Role:** the chain's lending layer, and a source of natural flow.

Morpho runs the chain's USDG lending markets (about $80M to $90M of RWA deposits, behind Robinhood Earn). Borrowers and lenders moving between yield positions and Stock Token exposure are exactly the rotation flow a swap venue serves.

## Account abstraction providers

**Role:** gasless onboarding and batched transactions.

ERC-4337 entry points and EIP-7702 are live. The front-end uses Alchemy Account Kit for embedded accounts and gas sponsorship, with ZeroDev as an alternative. A first swap is one user operation: approve via Permit2 and swap, with gas sponsored. Smart accounts sign RFQ quotes through EIP-1271.

## Robinhood Wallet

**Role:** primary trader wallet.

The front-end deep-links to Robinhood Wallet, and listing in the wallet's dApp directory is a distribution target: users who self-custody Stock Tokens out of the Robinhood app are ZircoFi's first audience.

## Blockscout

**Role:** transaction transparency.

Every fill, parameter change and fee accrual in the trade explorer links to the corresponding transaction on Blockscout.

## TRM Labs

**Role:** sanctions and AML screening.

TRM is integrated on the chain at the sequencer level. The KYC provider that issues ZircoFi eligibility attestations performs its own screening at attestation time and on renewal, giving two independent layers.

## Indexing

Ponder (self-hosted, TypeScript) or Envio HyperIndex over chain 4663 powers the trade explorer, the execution-quality dashboards and the keeper service. See [Off-chain services](../architecture/off-chain-services.md).

## Arbitrum ecosystem

Robinhood Chain is an Arbitrum Orbit chain. ZircoFi plans to apply for Arbitrum Foundation audit subsidies and to participate in the Arbitrum Open House 2026 buildathon track and Chainlink BUILD.
