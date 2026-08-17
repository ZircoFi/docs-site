---
description: Reference details for Robinhood Chain.
---

# Network details

{% hint style="info" %}
RPC endpoints, explorer URLs and Robinhood's own contract addresses are published at [docs.robinhood.com/chain](https://docs.robinhood.com/chain/). ZircoFi contract addresses are recorded in `contracts/deployments/4663.json` and described on the [Deployment](../architecture/deployment.md) page. Always verify addresses against those sources before sending funds.
{% endhint %}

## Network

| Network         | Chain ID | Status                    | ZircoFi               |
| --------------- | -------- | ------------------------- | ---------------------- |
| Robinhood Chain | 4663     | Mainnet since 1 July 2026 | Production deployment  |

## Chain parameters

| Parameter                | Value                                                                                  |
| ------------------------ | -------------------------------------------------------------------------------------- |
| Execution                | Arbitrum Nitro, EVM-equivalent, ArbOS 40 (EIP-7702 supported)                          |
| Settlement layer         | Ethereum                                                                               |
| Data availability        | Ethereum blobs (rollup mode)                                                           |
| Block time               | About 250 ms                                                                           |
| Preconfirmation latency  | About 100 ms                                                                           |
| Gas token                | ETH                                                                                    |
| Median transaction cost  | About $0.001                                                                           |
| Canonical withdrawal     | 7 days                                                                                 |
| Sequencer                | Single, operated by Robinhood; sanctions screening at the sequencer                    |
| Forced inclusion         | L1 delayed inbox, 24-hour delay                                                        |
| Explorer                 | Blockscout                                                                             |

## Precompiles and conventions used by ZircoFi

| Item                      | Usage                                                                                   |
| ------------------------- | --------------------------------------------------------------------------------------- |
| `block.timestamp`         | All time-based logic: quote expiries, staleness bounds, grace periods, the timelock     |
| `block.number`            | Never used for time; on Arbitrum it reflects the L1 block                               |
| `ArbSys.arbBlockNumber()` | Only where an L2 block height is required, for example in event metadata                |
| Compressed calldata       | RFQ quote structs are packed and signatures compacted because L1 data cost dominates fees |

## Standards in use

| Standard   | Where                                                                                   |
| ---------- | --------------------------------------------------------------------------------------- |
| ERC-20     | Stock Tokens (18 decimals), USDG (6 decimals)                                           |
| ERC-8056   | Stock Token corporate actions, `uiMultiplier()`                                          |
| ERC-4626   | Vault share accounting (with dual-asset, in-kind withdrawal extensions)                 |
| EIP-712    | RFQ quote signing                                                                       |
| EIP-1271   | Smart-account signature validation for maker quotes                                     |
| ERC-4337   | Account abstraction entry points v0.6 to v0.8                                           |
| EIP-7702   | EOA delegation to smart-account code                                                    |
| Permit2    | Allowance-based pulls at settlement, for takers and makers                              |
| EAS        | Eligibility attestations                                                                |

## Chainlink services on chain 4663

| Service                    | Purpose in ZircoFi                                    |
| -------------------------- | ------------------------------------------------------ |
| Data Feeds (24/5 equities) | The mid for vault quoting and band checks              |
| Data Streams (v11 RWA)     | Session classification and fill cross-checks           |
| Sequencer Uptime Feed      | Post-outage trading grace                              |
| CCIP                       | Bridged treasuries and gold (roadmap)                  |
| ACE / CCID                 | Alternative eligibility adapter                        |
