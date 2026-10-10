# Improving Pairs Trading with Machine Learning

Research project (Queen Mary University of London, 2021) testing whether **Fama–French three-factor betas, clustered with DBSCAN**, select better trading pairs than the standard **Engle–Granger cointegration** approach.

## Research question
Can the beta coefficients of the Fama–French three-factor model improve pair selection, and in turn portfolio profitability, compared with a cointegration baseline?

## Data
- Daily adjusted closing prices of S&P 500 stocks (Yahoo Finance), 2007–2020
- Daily Fama–French three factors (Kenneth French's data library)
- After missing-data handling: **431 stocks, 3,412 trading days**
  - Formation (training): Jun 2007 – Dec 2016
  - Trading (test): Jan 2017 – Dec 2020

## Method
1. **Factor regression** – OLS of each stock's returns on the three factors (training period only); stocks with all three betas significant are kept.
2. **Clustering** – standardised betas clustered with DBSCAN (`eps` via elbow method, `min_samples = 2·dim − 1`).
3. **Pair selection** – Engle–Granger cointegration tests within each cluster; the baseline applies the same test across the whole universe.
4. **Trading strategy** – rolling hedge ratio and z-score of the spread; enter at |z| > 2, exit at a range of thresholds.
5. **Evaluation** – 234 trading rules (13 portfolio sizes × 3 rolling windows × 6 exit thresholds); cluster vs baseline portfolios compared with Welch's t-tests on average daily returns.

All estimation uses formation-period data only, and trading-period measures are rolling, so there is no look-ahead bias.

## Results
Portfolios from three clusters earned significantly higher average daily returns than the baseline:

| Portfolio | Rules significantly better than baseline (of 234) | Avg daily return* |
|---|---|---|
| Cluster 4 | 141 | 0.16% |
| Cluster 1 | 136 | 0.14% |
| Cluster 2 | 81 | 0.12% |
| Baseline | – | 0.11% |

\*Averaged over the statistically significant rules; before transaction costs.

## Limitations and next steps
- Transaction costs are not modelled.
- A single train/test split; results should be validated over other periods.
- The stock universe is taken from the current S&P 500 constituent list, which introduces survivorship bias.
- Extend to other stock universes.

## Project structure
```
run_software.py                    # entry point
modules/
  m1_data_collection.py            # S&P 500 prices + Fama–French factors
  m2_data_preprocessing.py         # missing data, returns
  m3_clustering.py                 # factor regression + DBSCAN
  m4_pairs_trading_strategy.py     # cointegration, signals, back-test, t-tests
  m5_extract_insights.py           # summary of significant portfolios
  default.py                       # configuration / hyperparameters
  utils.py, plotting.py
docs/
  pairs_trading.pdf                # presentation (Feb 2021)
```

## Running
```bash
pip install -r requirements.txt
python run_software.py
```
The code reflects the 2021 setup; external data sources (Yahoo Finance, Wikipedia) may have changed since.

## Author
Efstathios Xanthopoulos
