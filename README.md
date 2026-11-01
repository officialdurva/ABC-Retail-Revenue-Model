# ABC Retail Revenue Prediction Model

Predicting a retailer's reported quarterly `revenue_index` from noisy order-number data and a consumer transaction panel. The model is trained on reported periods up to 2022 Q3 and evaluated out of sample on 2022 Q4.

## Results

**Chosen model:** linear regression of revenue on **panel-adjusted spend** (daily panel spend ÷ weekly active users, summed over each reporting period):

`revenue_index ≈ 4.34 + 3.617 × spend_adj`

| Metric | Result |
|---|---|
| In-sample R² (19 training periods) | **0.945** (adjusted 0.942) |
| 2022 Q4 prediction (out of sample) | **485.7** vs actual **512.1** → **−5.1%** |
| 80% / 95% prediction interval for 2022 Q4 | 440–531 / 414–558 (actual inside both) |
| Walk-forward backtest error (6 periods) | **6.55%** vs 11.90% naive benchmark and 15.45% SARIMA |
| Direction vs 2021 Q4 | Decline called correctly (predicted −12.1%, actual −7.3%) |

## Key findings

- **Raw panel spend is misleading.** The panel grew 22× while revenue grew 1.8×. Normalising by active users raised correlation with revenue from 0.81 to 0.97.
- **Order numbers needed heavy cleaning.** 211 impossible decreases plus near-zero and stale values. A layered cleaning (gross errors → asymmetric trend filter → monotonicity) raised agreement with the panel's independent order counts from 0.08 to 0.92.
- **Model selected on a walk-forward backtest, not the test period.** 2022 Q4 was used only once, after selection.

## Notebook structure

1. Exploratory data analysis
2. Order data cleaning
3. Transaction and reported data cleaning
4. Feature table construction
5. Model fitting and evaluation
6. Backtesting and model selection
7. Summary, limitations and next steps

## Running it

The source dataset (`data_task.xlsx`) is not included in this repository. With the file placed next to the notebook:

```bash
pip install -r requirements.txt
jupyter notebook ABC_Retail_Revenue_Model.ipynb
```

Then run all cells. Tested with Python 3.10+, pandas 2.x and statsmodels 0.14. The notebook also runs in Google Colab with the data file in `MyDrive/`.

## Tech

Python · pandas · NumPy · statsmodels (OLS, SARIMAX, LOWESS, ADF/KPSS) · Matplotlib
