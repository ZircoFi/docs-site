---
description: Direct answers to the questions traders, LPs and integrators actually ask.
---

# FAQ

## Trading

**Is ZircoFi an exchange?**
It is a non-custodial swap venue: quotes come from oracle-anchored vaults and competing makers, settlement is atomic on Robinhood Chain, and your assets never sit with the venue. There is no order book and no account to fund.

**Why is the spread wider tonight than this afternoon?**
The underlying market is closed. Closed-regime quotes carry a ×3 spread multiplier and smaller clips because LPs bear the gap to the next open. The ticket's regime badge says exactly which multiplier is in force. See [Trading regimes](../protocol/trading-regimes.md).

**Can I trade when a market is halted?**
No, and neither can anyone else; that is the point. Halts follow the oracle (staleness, corporate actions, implausible jumps) and clear automatically with it. Your assets are in your wallet throughout.

**Do I own Apple shares after buying tokenized AAPL?**
No. You hold a Stock Token: an ERC-20 debt security issued by Robinhood Assets (Jersey) Ltd that tracks the share, with dividends accreting into the token via its multiplier. Read [Stock Tokens](../assets/stock-tokens.md) before trading size.

**Why was my swap rejected before I could sign?**
Your wallet lacks a live `TRADER` attestation, or your jurisdiction is restricted for that asset. The platform states the failing rule. See [Eligibility registry](../architecture/eligibility.md).

**Where are dividends?**
In the token. Stock Tokens reinvest dividends by increasing `uiMultiplier()`, and the price reflects it. There is nothing to claim. See [Corporate actions and dividends](../protocol/corporate-actions.md).

## Liquidity

**What do LPs actually earn?**
The realised spread on their vault's fills, minus the protocol's 10% share, accruing into value per share. No emissions; the yield is the market's cost of immediacy. Realised numbers per market are public in the [trade explorer](../users/trade-explorer.md).

**Can my funds be locked by a pause or halt?**
No. Withdrawal works in every vault state, including halts and the guardian pause, as an invariant-protected property. You withdraw a pro-rata, in-kind mix of the vault's assets. See [Liquidity provision](../protocol/liquidity-provision.md).

**Is this impermanent loss?**
Not in the AMM sense. Anchored vaults do not bleed to reference-price arbitrage, which is where curve LPs lose. Vault LPs hold real inventory exposure (the token can fall) and gap risk on closed-regime fills, both stated plainly and both compensated through the spread.

## The venue

**Who sets the prices?**
A formula over public state: Chainlink mid, tier spread, regime multiplier, inventory skew, itemised fee. Nobody at ZircoFi can touch an individual quote or fill. See [Pricing and spreads](../protocol/pricing-and-spreads.md).

**What happens if ZircoFi the company disappears?**
The contracts keep quoting from on-chain state, withdrawals remain unconditional, and the services are open source for anyone to run. See [Corporate structure](../compliance/corporate-structure.md).

**Is there a token or airdrop?**
No, and none is planned. See [Business model](business-model.md).

**Why should I trust the execution?**
You should not have to: every fill's itemised breakdown is on-chain, and the venue publishes its distance from the reference price on every fill, unfiltered, at [Execution quality](../transparency/execution-quality.md).

**Can I integrate ZircoFi into my app?**
Yes: public [API and SDK](../architecture/api.md), a permissionless router, and no partner tier. Eligibility follows your users' wallets, not your app.
