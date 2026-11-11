# Crypto Market Risk: GARCH + Monte Carlo VaR

Volatility modelling and 1-day Value at Risk (VaR) for four cryptocurrencies (BTC, BNB, XRP, SOL) and an equally weighted portfolio of them, using GARCH(1,1) with Student-t innovations.

This is an academic / learning project, not a production risk model.

## Data

- Source: Yahoo Finance via `yfinance`
- Assets: `BTC-USD`, `BNB-USD`, `XRP-USD`, `SOL-USD`
- Period: 2021-01-01 to 2026-11-04 (daily closing prices, 2,134 prices / 2,133 log returns)
- Saved locally as `crypto_dataset.csv`

## Method

1. **Data extraction**: download closing prices, compute daily log returns.
2. **Descriptive analysis**: summary statistics, skewness, kurtosis, return histograms, return plots, correlation heatmap.
3. **Normality**: Jarque-Bera test.
4. **Stationarity**: Augmented Dickey-Fuller (ADF) test.
5. **ARCH effects**: PACF of squared residuals to pick lags, then Engle's ARCH-LM test (7 lags).
6. **Modelling**: GARCH(1,1) and GJR-GARCH(1,1,1), both with Student-t errors, estimated on returns in percent. Compared by AIC/BIC; plain GARCH(1,1) was kept for simplicity.
7. **Diagnostics**: ARCH-LM test on the standardized GARCH residuals.
8. **Risk**: 1-day 95% VaR by Monte Carlo (20,000 draws of Student-t shocks, rescaled to unit variance, scaled by the last estimated conditional volatility).
9. **Portfolio**: equal weights (25% each), GARCH(1,1)-t on the portfolio return series, residual diagnostics, 10-day variance forecast, 1-day 95% VaR.

## Key results

**Returns are heavy-tailed and non-normal.** Jarque-Bera rejects normality for all four assets (p ≈ 0).

| Asset | Skewness | Excess kurtosis |
|-------|---------:|----------------:|
| BTC   | -0.13    | 4.20            |
| BNB   | 0.87     | 24.80           |
| SOL   | -0.36    | 9.30            |
| XRP   | 1.25     | 17.79           |

**All return series are stationary** (ADF p < 0.05 for every asset) and **all show significant ARCH effects** (ARCH-LM p < 0.05), which justifies a GARCH-type model.

**Residual check after GARCH(1,1)-t:** ARCH effects are removed for BNB (p = 0.92), SOL (p = 0.09) and XRP (p ≈ 1.0). They remain for BTC (p = 0.0003), meaning a (1,1) specification does not fully capture BTC's volatility dynamics.

**1-day 95% Monte Carlo VaR:**

| Asset | VaR (95%) |
|-------|----------:|
| BTC   | 2.83%     |
| BNB   | 4.06%     |
| XRP   | 4.64%     |
| SOL   | 5.10%     |
| Equal-weight portfolio | TBD |

The portfolio GARCH residuals show no remaining ARCH effects (ARCH-LM p = 0.86). The 10-day variance forecast rises from about 7.5 to 9.9 (%)

## Limitations

- BTC residuals still show ARCH effects under GARCH(1,1).
- VaR is a single-day, in-sample estimate. There is no backtesting (e.g. Kupiec / Christoffersen tests)
- The Monte Carlo step uses a closed-form distribution, so the simulation only approximates what the Student-t quantile gives directly.
- Portfolio returns are built as a weighted sum of log returns, which is an approximation (exact only for simple returns).
- Equal weights, no transaction costs, no rebalancing assumptions.


```bash
git clone https://github.com/MrX1710/Projects.git
cd Projects/<this-project-folder>
pip install numpy pandas matplotlib seaborn scipy statsmodels arch yfinance
jupyter notebook crypto_project.ipynb
```

## Stack

Python, pandas, NumPy, statsmodels, arch, SciPy, Matplotlib, Seaborn
