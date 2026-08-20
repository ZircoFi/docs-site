---
description: The public quote API, the TypeScript SDK, and the maker protocol.
---

# API and SDK

The API exists so that ZircoFi can be a backend, not just a website: wallets, portfolio apps, aggregators and agents integrate the same pricing the platform uses, and settle through the same public router. There is no partner tier and no privileged endpoint; the reference front-end consumes exactly what is documented here.

## Quote API (REST)

```
GET /v1/quote?tokenIn=USDG&tokenOut=AAPL&amountIn=2000000000
```

```json
{
  "amountOut": "9873400000000000000",
  "breakdown": {
    "mid": "202.41",
    "spreadBps": 10,
    "skewBps": -2,
    "feeBps": 2,
    "regime": "OPEN",
    "oracleRound": "0x…"
  },
  "venue": "vault",
  "rfq": { "quote": { "…": "…" }, "signature": "0x…" },
  "validUntil": 1788316201,
  "tx": { "to": "0xSwapRouter…", "data": "0x…" }
}
```

The response always itemises the breakdown, names the venue that priced best, includes any winning RFQ quote with its signature for on-chain verification, and returns ready-to-sign calldata with the trader's bound applied. Quotes are previews: the chain re-derives or re-verifies everything, so integrators cannot be misquoted into a bad fill, only into a revert.

Other endpoints: `/v1/markets` (listings, tiers, parameters, regimes), `/v1/fills` (paginated fill history with breakdowns), `/v1/execution-quality` (the published dataset behind the [transparency page](../transparency/execution-quality.md)), `/v1/eligibility/:address` (role check preview).

## WebSocket

`wss://api.zircofi.com/v1/stream` pushes regime changes, per-market quote updates at feed cadence, and fill events. The same stream feeds the platform's tickets.

## TypeScript SDK

```ts
import { ZircoFi } from "@zircofi/sdk";

const z = new ZircoFi({ chainId: 4663, signer });
const quote = await z.quote({ tokenIn: "USDG", tokenOut: "AAPL", amountIn: 2_000_000000n });
console.log(quote.breakdown);          // mid, spread, skew, fee, regime
const receipt = await z.swap(quote, { maxSlippageBps: 15 });
```

The SDK wraps quoting, Permit2 signing, eligibility preflight and receipt decoding, and ships typed events for indexer consumers. It is versioned semantically with a published changelog; breaking changes never ship silently.

## Maker protocol

Makers connect over an authenticated WebSocket, receive request broadcasts, and answer with EIP-712 signatures:

```
→ { "type": "rfq", "id": "…", "tokenIn": "USDG", "tokenOut": "NVDA", "amountIn": "250000000000" }
← { "type": "quote", "id": "…", "quote": { …EIP-712 fields… }, "signature": "0x…" }
```

Admission requires a `MAKER` attestation; the message schema, signing domain and nonce-management guidance are in the maker guide at [For market makers](../users/market-makers.md). Losing costs nothing; there are no quoting obligations at launch, though sustained quoting earns priority in request routing.

## Rate limits and keys

Quote endpoints are open and rate-limited by IP; authenticated keys raise limits and add the WebSocket. Keys identify integrators for support and abuse control only; they confer no pricing difference, which is checkable, since vault pricing is a public contract read.

## Versioning

`/v1` is stable. Deprecations are announced in the changelog at least 90 days ahead, and the SDK pins API versions explicitly. Contract addresses per deployment are in [Deployment](deployment.md).
