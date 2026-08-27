---
description: The venue grades its own execution in public, against the reference price, on every fill.
---

# Execution quality

Execution quality is the venue's product, so it is measured and published the way infrastructure publishes uptime: continuously, unfiltered, and against a benchmark nobody at the venue controls.

## The metric

For every fill: the signed distance, in basis points, between the realised price and the guarded Chainlink mid recorded in the fill event's oracle round. Positive means the trader paid over mid (the normal case, by the spread), negative means price improvement (RFQ competition can beat the vault's spread). Because the mid used is the one the fill itself was checked against, the metric is exactly recomputable by anyone from chain data; the venue cannot pick a friendlier benchmark after the fact.

## What is published

| Cut | Contents |
| --- | --- |
| Live | Rolling distributions per market, venue and regime; median, p90 and worst fill |
| Daily | The full fill-level dataset, downloadable, no minimum size filter and no exclusions |
| By regime | `OPEN` versus `CLOSED` costs side by side, so the weekend premium is a published number rather than a suspicion |
| RFQ | Maker price improvement versus the vault quote at the same moment, aggregated |
| Reverts | Slippage-bound and band reverts as a rate, because a venue can flatter fills by failing them |

## How to read it

* The median `OPEN` fill for a Tier A market should sit near the base half-spread plus fee. Persistent drift above it means parameters need retuning, and the timelocked retune will cite this dataset.
* The `CLOSED` distribution is expected to be wider and costlier; that is the priced gap risk. What it must not show is fills near the band edge in calm conditions.
* Sudden compression of band margins across many fills is the early signature of an oracle problem, which is why the operations stack alerts on the same public numbers this page shows.

## Why publish the bad days too

A dataset with exclusions is marketing. The point of publishing every fill, including the expensive weekend ones and the reverts, is that an LP underwriting a vault, a maker calibrating quotes, and a trader deciding whether Saturday can wait for Monday are all working from the same record the venue itself is judged on. Venues that grade themselves honestly get better; the dataset is the venue's own tuning input, published.

The raw data is at `/v1/execution-quality` in the [API](../architecture/api.md), and every aggregate on this page links to the fills behind it in the [trade explorer](../users/trade-explorer.md).
