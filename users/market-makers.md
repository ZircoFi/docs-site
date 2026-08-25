---
description: A practical guide for professional makers: admission, quoting, settlement and the vault-rebalancing trade.
---

# For market makers

Makers are the venue's professional liquidity: they win block flow through [RFQ](../protocol/rfq.md), compete with the vaults for any flow they can beat, and are paid by [skew pricing](../protocol/pricing-and-spreads.md) to keep vault inventory balanced. Quoting is free to lose and atomic to win.

## Admission

RFQ requires a `MAKER` attestation, issued to professional trading firms through the standard verification flow. There are no quoting obligations, no membership fees and no volume commitments at launch; sustained quoting earns priority in request routing, and that is the only tiering that exists.

Operationally you need: an EIP-712 signing key (EOA or EIP-1271 smart account), a standing Permit2 allowance from your settlement wallet to `RfqSettlement`, inventory in USDG and the tokens you quote, and a connection to the maker gateway.

## The loop

1. Connect to the gateway WebSocket ([protocol spec](../architecture/api.md)) and receive request broadcasts: pair, size, side, taker attestation class.
2. Price it. During `OPEN` regime you can hedge on the underlying within seconds; during `CLOSED` you are pricing weekend gap risk explicitly, which is exactly when your quotes are most valuable and the vault's are widest.
3. Sign and return within the collection window (a few hundred milliseconds). Best quote wins; the chain verifies your signature, attestation, nonce, expiry and the oracle band at settlement.
4. Settlement pulls `tokenOut` from your wallet and delivers `tokenIn` atomically. No fill, no movement.

Nonces support bulk cancellation on-chain, and cancellation is reachable through the L1 delayed inbox even against a censoring sequencer. Keep quote expiries short (seconds); the band means a stale quote reverts rather than fills at a bad price, but your hedging assumptions are your own.

## The rebalancing trade

Vault skew is public state: `AnchorVault.quote()` shows which side each vault improves to attract rebalancing flow, up to the max skew term at the band edge. Trading against a skewed vault is riskless spread capture relative to the oracle mid, bounded in size by the clip, and it is the protocol's intended rebalancing mechanism, not a loophole. Makers running this systematically keep vaults two-sided and earn the skew for doing it.

## What settlement enforces on you

| Check | Consequence |
| --- | --- |
| Signature, nonce, expiry | Standard; replays impossible |
| `MAKER` attestation live at fill time | Revoked attestation means no settlement, immediately |
| Oracle band per fill | You cannot be filled, or fill anyone, outside the band |
| Atomicity | A failed pull on either side reverts the whole fill |

The band cuts both ways deliberately: it caps what a compromised maker key can do to takers, and what a stale quote can cost you.

## Reporting

Your fills appear in the [trade explorer](trade-explorer.md) like all others, maker-anonymised by default. The API's authenticated endpoints give you your own fill and quote-performance history, including win rate and price improvement versus the vault, the number the routing priority is built on.
