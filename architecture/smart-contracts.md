---
description: Every contract in the protocol, its single job, and its mutability.
---

# Smart contracts

The contract set is deliberately small. Each contract has one job, logic is immutable per version, and everything that can change lives in `ParamController` behind the timelock.

## The set

| Contract | Job | Mutability |
| --- | --- | --- |
| `SwapRouter` | Sole trader entry point: eligibility, venue selection, two-leg composition, band and bound enforcement | Immutable |
| `AnchorVault` (one per market) | Hold inventory, quote around the mid, mint and burn LP shares | Immutable; parameters via `ParamController` |
| `VaultFactory` | Deploy vaults from listed configurations | Immutable |
| `RfqSettlement` | Verify maker EIP-712 quotes and settle them atomically | Immutable |
| `OracleRouter` | The only price door: Chainlink adapters, guards, session and regime state | Immutable; adapters via `ParamController` |
| `EligibilityRegistry` | The only permission door: role checks against attestations | Immutable; policy adapters via `ParamController` |
| `ParamController` | Every tunable value, behind the timelock | Timelocked writes, public reads |
| `FeeCollector` | Accrue protocol fees | Immutable |
| `Guardian` | Pause quoting and settlement in an incident | Can only halt; cannot move funds or block withdrawals |

## Interfaces that matter to integrators

```solidity
// SwapRouter: the one call traders make
function swap(SwapParams calldata p) external returns (uint256 amountOut);
struct SwapParams {
    address tokenIn; address tokenOut;
    uint256 amountIn; uint256 minAmountOut;
    uint256 deadline;
    MakerQuote[] quotes;     // optional RFQ candidates, verified on-chain
    bytes permit;            // Permit2 pull for tokenIn
}

// AnchorVault: LP side
function deposit(uint256 usdgAmount, uint256 tokenAmount, address receiver) external returns (uint256 shares);
function withdraw(uint256 shares, address receiver) external returns (uint256 usdgOut, uint256 tokenOut);
function quote(bool isBuy, uint256 amountIn) external view returns (uint256 amountOut, QuoteBreakdown memory b);

// OracleRouter: the state everyone can read
function priceOf(address token) external view returns (uint256 mid, uint8 regime, uint64 updatedAt);
```

`QuoteBreakdown` itemises mid, spread, skew and fee, and is emitted on every fill, so the on-chain record carries the same decomposition the ticket showed.

## Events

Every fill emits one event with the pair, size, venue (vault or maker), the full breakdown, and the oracle round used. Every parameter change emits the old value, the new value and the timelock proposal hash. The [trade explorer](../users/trade-explorer.md) is a pure function of these events; nothing it shows comes from anywhere else.

## Upgrade philosophy

There are no proxies. A new protocol version is a new deployment; markets migrate by LPs withdrawing in kind and redepositing, at their own choice. This costs migration convenience and buys the property that the code holding funds today is the code that was audited, forever. The one concession is the adapter pattern inside `OracleRouter` and `EligibilityRegistry`, where the set of accepted adapters is a timelocked parameter, because oracle products and attestation schemes evolve faster than settlement logic should.

## Access control summary

| Role | Held by | Powers |
| --- | --- | --- |
| Timelock proposer | Foundation multisig | Propose parameter changes, published with rationale |
| Guardian | Foundation multisig (smaller quorum) | Pause; unpause is timelocked |
| Keeper functions | Nobody: permissionless | Regime pokes, halt pokes |
| Everything else | Nobody | There are no other privileged functions |
