---
description: The stateless services around the contracts, and why none of them is trusted.
---

# Off-chain services

Three services surround the contracts. The design constraint on all of them is the same: a service may make the venue faster or more convenient, never more trusted. Each one's failure mode is degraded convenience with unchanged guarantees.

## Quote service

The quote service answers "what would this swap cost right now": it simulates `SwapRouter` against current oracle and vault state, broadcasts RFQ requests to connected makers, collects signatures for a few hundred milliseconds, and returns the best composite quote with its full breakdown. It backs the platform ticket and the public [API](api.md).

What keeps it honest:

* It holds no keys and touches no funds; its output is a preview plus, for RFQ, maker signatures that the chain verifies independently.
* The router re-derives vault pricing on-chain at fill time, so a wrong preview produces a revert against the trader's signed bound, not a bad fill.
* It cannot suppress the vault path: the router settles the vault whenever the vault beats every supplied quote.

If the service is down, vault swaps work from any interface that can call the contract, at exactly the same prices; only RFQ aggregation and convenience are lost.

## Keeper service

Regime transitions, staleness halts and sequencer-grace expiries are permissionless pokes; the keeper service is simply the process that calls them promptly. Because every quote recomputes regime in-transaction anyway, a slow keeper cannot cause stale-session pricing; it can only delay the explorer's display catching up. The reference keeper is open source, and anyone may run one.

## Indexer

Ponder (self-hosted, TypeScript) or Envio HyperIndex over chain 4663 turns fill and parameter events into the [trade explorer](../users/trade-explorer.md), the [execution quality](../transparency/execution-quality.md) dataset, and per-market LP statistics. Every displayed figure links to the event it came from, so a reader who distrusts the indexer can recompute anything from Blockscout.

## Maker gateways

Market makers connect to the quote service over a WebSocket protocol documented in [API and SDK](api.md): they receive request broadcasts and respond with signed quotes. Gateways are the makers' own infrastructure; ZircoFi's side only relays and ranks. A maker's connection dropping removes their quotes from the auction and nothing else.

## Monitoring

The operations stack watches the same public state everyone else can: oracle staleness and divergence, vault skew against bands, halt states, sequencer uptime, fill-versus-band margins, and execution-quality drift. Alerts route to the guardian on-call. There is no privileged telemetry; anything worth monitoring is on-chain, which is a design statement as much as an operational one.

## Running your own

Everything above is open source. An integrator who wants independence can run the quote service, keeper and indexer from the public repositories against any RPC provider, and the [platform](platform.md) front-end is a reference client of the same public APIs it documents.
