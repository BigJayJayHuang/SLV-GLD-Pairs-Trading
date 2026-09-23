# SLV/GLD Pairs Trading Strategy

An out-of-sample statistical-arbitrage backtest developed for FIN 533 at Duke University. The project evaluates whether silver and gold exchange-traded funds (SLV and GLD) exhibit a sufficiently stable relationship to support a mean-reversion trading strategy.

## Project overview

SLV and GLD are influenced by overlapping macroeconomic drivers, including interest rates, inflation expectations, U.S. dollar strength, and safe-haven demand. Silver is generally more volatile than gold, creating a potential relative-value relationship between the two assets.

The strategy models the relationship during a formation period and then tests the trading rules on a separate out-of-sample period. This separation reduces look-ahead bias, which occurs when a strategy uses information that would not have been available when a historical trade was placed.

## Methodology

The notebook:

1. Downloads adjusted daily SLV and GLD prices through ShinyBroker.
2. Uses June 2024 through May 2025 as the formation period.
3. Uses June 2025 through March 25, 2026 as the trading period.
4. Estimates an OLS hedge ratio using formation-period prices.
5. Tests the relationship with Engle–Granger cointegration and Augmented Dickey–Fuller tests.
6. Converts the spread into a z-score using formation-period statistics.
7. Executes trades at the next day's open using the prior day's closing signal.

## Trading rules

- Enter a short spread when the prior-close z-score is above +2.0.
- Enter a long spread when the prior-close z-score is below −2.0.
- Exit when the z-score returns to 0.0.
- Stop out when the z-score reaches ±3.0.
- Close positions after 20 trading days if neither exit condition occurs.
- Close any remaining position at the end of the backtest.
- Allocate 30% of NAV to each paired trade's position-sizing budget.

In this context, a spread is the difference between SLV and the hedge-ratio-adjusted value of GLD. A z-score measures how far the current spread is from its historical average in standard-deviation units. The hedge ratio estimates how much GLD exposure is used to offset SLV exposure.

## Results

The generated out-of-sample results contain 95 trades from June 2025 through March 2026.

| Metric | Result |
|---|---:|
| Initial capital | $1,000,000 |
| Ending NAV | $907,259.18 |
| Total return | −9.27% |
| Total trade P&L | −$92,740.82 |
| Win rate | 40.0% |
| Average holding period | 1.1 trading days |
| Completed trades | 95 |

The results are presented as a research backtest, not as evidence of a profitable live-trading strategy. Most recorded exits were stop-loss exits, which highlights the risk of relying on a static hedge ratio when the underlying relationship changes.

## Repository contents

```text
slv-gld-pairs-trading/
├── README.md
├── requirements.txt
├── slv_gld_pairs_trading.ipynb
└── results/
    ├── ledger.csv
    └── trades.csv
```

## Setup and usage

This project requires Python 3.12 or newer.

```bash
conda create -n pairs-trading312 python=3.12
conda activate pairs-trading312
python -m pip install -r requirements.txt
```

Open `slv_gld_pairs_trading.ipynb` in VS Code, select the `pairs-trading312` Jupyter kernel, and run all cells from top to bottom.

The notebook saves the trade blotter and daily portfolio ledger to the `results/` directory.

## Limitations and future improvements

- Cointegration can weaken during macroeconomic shocks or periods when gold and silver decouple.
- The hedge ratio is static and is estimated only during the formation period.
- The transaction-cost estimate does not fully capture bid–ask spreads, slippage, or market impact.
- The backtest evaluates only one pair and does not measure portfolio-level diversification.
- Future improvements could include a rolling or Kalman-filter hedge ratio, rolling cointegration checks, volatility-scaled position sizing, and testing additional cross-asset pairs.

A future version could also incorporate macroeconomic features such as the U.S. Dollar Index, Japanese yen movements, 10-year Treasury yields, real interest rates, inflation expectations, and Federal Reserve policy language. These variables could provide directional context for precious metals and help identify macroeconomic regimes in which the SLV/GLD relationship may strengthen or weaken. A regime-aware model could then adjust hedge ratios, entry thresholds, or position sizes. These variables would be treated as explanatory signals and risk-management inputs rather than guaranteed predictors of market direction.

## Disclaimer

This project is for educational and research purposes only. It is not investment advice or a recommendation to trade any security.
