---
description: The public window onto everything the venue does.
---

# Trade explorer

The trade explorer is the venue's public record: every fill, every market's live state, every parameter and every change to one, rendered from chain events with a link back to each. It loads without a wallet, because none of it is anyone's private information. "Transparently on-chain" is checkable here or it is nothing.

## Per market

| Panel | Contents |
| --- | --- |
| Live quote | Current bid, ask and mid with the full breakdown, exactly as the router would price it now |
| Regime | Current regime, its source signals (feed timestamp, stream market status), and time in state |
| Inventory | Vault mix against target and band; skew direction and size |
| Parameters | Tier, spreads, clip, caps, band, every one a `ParamController` read |
| History | Fills with itemised breakdowns; spread revenue by regime; halt log with reasons |
| Listing record | Tier evidence, bytecode review finding, issuer disclosure, proof-of-reserve status where a feed exists |

## Venue-wide

* Volume, fill count and revenue by market, venue (vault or RFQ) and regime.
* Total vault inventory exposed to Robinhood Assets (Jersey) Ltd as a single number, per [Issuer risk](../risk/issuer.md).
* The [execution quality](../transparency/execution-quality.md) dataset: every fill's distance from the oracle mid, aggregated and downloadable.
* The governance log: every parameter proposal, its rationale, its timelock window and its execution, past and pending.

## Every number is a citation

Each figure links to the event, transaction or contract read it came from on Blockscout. The explorer is built on the open-source indexer, so anyone can rerun it against any RPC and diff the output; a discrepancy between the explorer and the chain is a reportable bug, and the chain wins.

## For auditors and integrators

Everything rendered is available raw through the [API](../architecture/api.md) (`/v1/markets`, `/v1/fills`, `/v1/execution-quality`) and as CSV exports, including complete per-position LP histories for account holders. Researchers are welcome to the data; the venue's position is that an RWA market whose record cannot be independently reconstructed has no record at all.
