---
description: The criteria an asset must meet before a market opens, and the tier system that sets its parameters.
---

# Listing framework

Listing is a checklist, not a judgement call. An asset either meets the criteria for a tier or it does not, the evidence is published with the listing, and the parameters follow from the tier mechanically.

## Requirements for any listing

Every market, regardless of tier, requires all of the following:

| Requirement | Why |
| --- | --- |
| Chainlink Data Feed live on chain 4663 for the exact token | Anchor pricing prices the token in the vault, never a wrapper or a derived rate |
| Data Streams coverage with market status, or an equivalent session signal | The [regime engine](../protocol/trading-regimes.md) needs an authoritative session source |
| Regulated issuer with published terms and 1:1 backing | The venue lists claims on real assets, not synthetics |
| Bytecode review of the deployed token | Pause, freeze, blacklist and forced-transfer roles are found before listing, not after; the finding is published |
| ERC-20 with standard semantics | No fee-on-transfer, no rebasing outside ERC-8056-style multipliers reflected in the feed |

An asset that fails any requirement is not listed at any tier. There is no discretionary override.

## Tiers

The tier sets every market parameter. Initial values:

| Parameter | Tier A | Tier B | Tier C |
| --- | --- | --- | --- |
| Base half-spread | 10 bps | 20 bps | 40 bps |
| Max skew term | 15 bps | 25 bps | 50 bps |
| Oracle band | 75 bps | 150 bps | 300 bps |
| Regular clip | 50,000 USDG | 20,000 USDG | 5,000 USDG |
| Daily volume cap (guarded launch) | 2M USDG | 750K USDG | 200K USDG |
| Vault TVL cap (guarded launch) | 5M USDG | 2M USDG | 500K USDG |

Tier assignment weighs the underlying's liquidity (average daily volume, typical spread on the primary market), the feed's update cadence, and the token's on-chain distribution. Assignments and the evidence behind them are published per market; reassignment is a timelocked parameter change like any other.

* **Tier A**: index ETFs and mega-caps (SPY, QQQ, AAPL, MSFT, NVDA). Deep underlying books, dense feed updates.
* **Tier B**: liquid single names outside the mega-caps.
* **Tier C**: the long tail. Wide underlying spreads and sparser updates are priced through the wider band and spread rather than excluded outright, because serving the long tail properly is a design goal; caps keep the exposure honest.

## Delisting

A market retires when its asset no longer meets the listing requirements: the feed is discontinued, the issuer winds the token down, or a bytecode-level change introduces a disqualifying control. Retirement is timelocked, published with its rationale, and moves the vault to withdrawals-only. See [Corporate actions and dividends](../protocol/corporate-actions.md).

## Adding new asset classes

Tokenized treasuries and gold follow the same checklist with one addition each: NAV-type assets require a NAV-consistent feed and trade within a tight band around it, and bridged assets require the bridge review described in the [Asset roadmap](asset-roadmap.md).
