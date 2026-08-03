---
description: The market conditions that make an RWA-native swap venue viable in 2026.
---

# Why now

Three things had to be true before a venue like ZircoFi could work: tokenized stocks had to exist as ordinary on-chain assets with reliable pricing, real trading demand had to exist for them, and the existing venue designs had to leave a gap worth filling. All three are true today.

## Tokenized stocks are now a real asset class

Non-stablecoin, transferable RWAs on public chains went from roughly $7.9B at the end of 2024 to about $21B at the start of 2026 and about $38.7B today. Tokenized stocks are the fastest-growing category within that.

| Category (rwa.xyz, 28 Aug 2026) | On-chain, transferable value | Notes                                                                                   |
| ------------------------------- | ---------------------------- | --------------------------------------------------------------------------------------- |
| Stablecoins                     | ~$303B                       | The settlement layer                                                                    |
| Tokenized US Treasuries         | ~$16.0B                      | USYC $2.9B, BUIDL $2.8B, USDY $2.2B, BENJI ~$2.4B                                       |
| Tokenized credit                | ~$7.5B distributed           | About $35B if non-transferable "represented" assets such as Figure HELOCs are counted   |
| Commodities                     | ~$3.1B                       | XAUT, PAXG                                                                              |
| **Tokenized stocks**            | **~$2.6B**                   | Ondo Stocks above $1B TVL; xStocks above $25B cumulative volume; Robinhood, Coinbase, Binance |
| Private equity and VC           | ~$1.6B                       |                                                                                         |
| Real estate                     | ~$175M                       |                                                                                         |

Tokenized stocks alone went from about $424M in mid-2025 to about $2.59B in August 2026. Figures vary by around 20 percent between sources depending on whether non-transferable assets are counted.

## Issuance is solved. Trading is not

Almost all tokenized-stock volume today happens on centralised exchange order books: custodial, off-chain, opaque. The on-chain alternatives are generic AMMs that were designed for crypto-native pairs and mishandle RWAs in predictable ways:

* **Reserve-ratio pricing drifts.** A constant-product pool has no idea what the asset is worth. On thin books, tokenized stocks have traded at material premiums to the underlying, and launch-week dislocations on tokenized-equity pools in 2025 are a matter of record. See [Lessons from RWA trading](../risk/lessons.md).
* **LPs are adversely selected.** Every reference-price move on the underlying exchange is arbitraged against the pool before the curve catches up. For an asset whose price is set elsewhere, passive curve liquidity is a subsidy to arbitrageurs.
* **Nights and weekends are unpriced.** Equity markets are open about 32 hours a week in regular session. A pool that quotes the same spread on Saturday as on Tuesday is mispricing gap risk about 80 percent of the time.
* **Corporate actions break pools.** Splits and reverse splits reprice a token overnight; a pool that does not know about them donates the difference.

Primary mint and redeem against the issuer provides exit liquidity, not a market. The venue that treats RWAs as what they are, assets with an authoritative external price, defined sessions and corporate actions, does not yet exist on-chain at scale.

## Robinhood Chain removes the last blocker

Robinhood Chain reached mainnet on 1 July 2026. It is the only L2 where a regulated broker issues 1:1-backed tokenized stocks as plain ERC-20s, with Chainlink 24/5 price feeds and Data Streams carrying market status, sub-cent transactions, ERC-4337 account abstraction and permissionless contract deployment. The assets, the pricing and the users are in one place; the RWA-native venue is the missing piece. See [Why Robinhood Chain](../chain/why-robinhood-chain.md).

## The incumbents are pointed the other way

* Centralised books (Kraken xStocks, Bybit, Binance) keep the volume off-chain and custodial, which is exactly what tokenization was meant to end.
* Ondo Global Markets routes to US exchange liquidity through its own broker pipe: deep pricing, but only through the issuer, and only when US markets are open.
* Uniswap v4 added permissioned pools in July 2026, which solves who may trade but not how RWAs should be priced.

An oracle-anchored, session-aware, non-custodial venue is complementary to all of these and competitive with none of their strengths. That is the position ZircoFi occupies. See [Design principles](design-principles.md).
