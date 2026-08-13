---
description: How the venue behaves when the chain itself misbehaves.
---

# Sequencer and chain risk

Robinhood Chain is an Arbitrum Nitro rollup with a single sequencer operated by Robinhood. That is normal for an L2 at this stage, and it comes with specific failure modes that a 24/7 trading venue has to design for rather than hope around.

## What can go wrong

| Scenario | Effect on users |
| --- | --- |
| Sequencer outage | No transactions are included on the L2 for the duration. Prices keep moving on the underlying market. |
| Sequencer censorship | Specific transactions (for example an LP withdrawal or a maker's quote cancellation) are not included. |
| Delayed L1 finality | Withdrawals to Ethereum take the canonical 7 days; this does not affect on-L2 trading. |
| Chain configuration change | DA mode, validator set, or the enabling of Timeboost or BoLD could change finality and ordering assumptions. |

## Mitigations

### Trading grace after an outage

`OracleRouter` reads the Chainlink L2 Sequencer Uptime Feed. When the feed reports that the sequencer has just come back, every market stays `HALTED` for a grace period of **one hour**. During an outage the underlying market kept moving while LPs could not withdraw and parameters could not react; the grace period lets oracle rounds refresh and participants act before anyone can trade against a vault that was frozen mid-move. Withdrawals and cancellations work immediately on recovery; only quoting waits.

### Everything is reachable through the L1 delayed inbox

Every state-changing function in ZircoFi can be submitted through Arbitrum's delayed inbox on Ethereum. If the sequencer does not include a transaction within 24 hours, it can be force-included. In practice this means:

* a censoring sequencer cannot prevent an LP from withdrawing,
* a censoring sequencer cannot prevent a maker from cancelling quotes by nonce,
* the guardian's pause and the timelock's parameter changes cannot be censored either.

Swaps themselves are not meaningfully forced-included, since a quote is stale long before 24 hours, but nothing that protects funds depends on the sequencer's cooperation.

### Timestamps, not block numbers

On Arbitrum-stack chains `block.number` returns an L1-derived value. Quote expiries, staleness bounds, grace periods and the timelock all use `block.timestamp` only. If an L2 block height is ever needed, the protocol calls `ArbSys.arbBlockNumber()`.

### Calldata cost

The L1 data component of gas is priced on compressed calldata size. RFQ quote structs are packed tightly and signatures passed in compact 64-byte form where possible, so that settlement stays sub-cent even when blob prices spike.

## Ordering policy watch

The sequencer is first-come-first-served today, with about 100 ms preconfirmations. Whether Timeboost (express-lane ordering) or BoLD (permissionless validation) are enabled on Robinhood Chain affects how quote competition and forced inclusion behave. ZircoFi tracks both; the design already assumes no favourable ordering (fills are bounded by the signed slippage limit and the oracle band regardless of position in a block), and any configuration change triggers a published review in the governance log.
