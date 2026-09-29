# Delta Hedging SPY Options

A delta-hedged short option makes or loses money depending on implied versus
realised volatility. This notebook checks it on simulated prices, then on
real SPY data from 2010 to 2025.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/gianni04/spy-delta-hedging/blob/main/delta_hedging.ipynb)

## Method

- Black-Scholes price and delta (with dividend yield) for a one-month
  at-the-money straddle.
- Sell the straddle, hold its delta in shares, rebalance at the close, pay
  costs on share trades, receive dividends, earn interest on cash.
- **Simulation:** 5,000 price paths, realised vol 20%, sold at 20% or 25%,
  hedged 1 to 252 times over the month, with and without costs.
- **Real data:** every 21 trading days, sell a 21-day ATM straddle on SPY and
  hedge it daily (191 non-overlapping trades). Implied vol from VIX, rate from
  the 13-week T-bill, actual SPY dividends, 1 bp per share trade.

## Results

**Simulation**

![Simulation](figures/simulation.png)

- Sold at the true vol, the hedged P&L averages zero. Its standard deviation
  falls from 3.51 (hedged once) to 0.26 (252 rebalances over the month),
  about 1 / square root of the number of rebalances.
- Sold at 25% with 20% realised, the seller keeps about 1.15, the price
  difference between the two vols, at every hedging frequency.
- With 0.1% costs, rebalancing 252 times eats most of that edge.

**SPY, 2010-2025**

![SPY](figures/spy_hedged_pnl.png)

| | Sold at VIX | Sold at 0.8 x VIX |
|---|---|---|
| Mean P&L per trade | +0.79% of spot | -0.03% of spot |
| Winning trades | 83% | 62% |
| Worst trade | -7.5% (March 2020) | -8.2% (March 2020) |

- The P&L of each trade follows implied minus realised variance: correlation
  0.78 (0.77 without Feb-Apr 2020, rank correlation 0.80).
- VIX averaged 18.4% against 14.5% realised volatility. But VIX is a variance
  swap rate and sits above the at-the-money option vol because of the skew.
  With a more realistic ATM vol (0.8 x VIX) the average profit disappears.

## Limitations

- No historical option prices: implied vol is approximated from VIX.
- Option bid-ask spreads are ignored.
- SPY options are American and priced here as European.
- Hedging at the daily close only.

## Run

```bash
pip install -r requirements.txt
jupyter notebook delta_hedging.ipynb
```
