# London Air Pollution Regression

A notebook-first, leakage-safe time-series regression case study that forecasts **next-day PM2.5** at the Camden–Bloomsbury urban background monitoring station.

## Project question

Can tomorrow's `UB_BL0_pm25` be predicted using only information available yesterday or earlier—recent pollution, weather, and calendar features?

## Dataset

- **Source:** AIR-01 London dataset, Imperial College London
- **Period:** 1 January 2019 – 31 December 2022
- **Size:** 1,461 daily observations and 75 source columns
- **Target:** `UB_BL0_pm25`
- **Source / citation:** Zenodo record 19163632
- **Dataset licence:** CC BY 4.0; retain attribution when reusing the data

## What this project teaches

The notebook is deliberately complete rather than compressed. It walks through:

1. Understanding the dataset and data quality
2. Exploratory data analysis and time patterns
3. Date/time features and lag features
4. Feature engineering from past information
5. Explicit data-leakage demonstrations and prevention
6. Chronological train / validation / untouched test splitting
7. Training-only preprocessing
8. Mean and persistence baselines
9. Linear Regression, Random Forest, and HistGradientBoosting
10. MAE, MSE, RMSE, and R²
11. Time-aware `TimeSeriesSplit`
12. Small `GridSearchCV` hyperparameter tuning
13. Final model selection without using the test year
14. Actual-vs-predicted analysis and residual/error analysis
15. Permutation feature importance
16. Risk, limitations, and an honest final conclusion

## Final test result

| Model | MAE (µg/m³) | RMSE (µg/m³) | R² |
|---|---:|---:|---:|
| Mean baseline | 5.20 | 7.58 | −0.00 |
| Persistence baseline | 3.26 | 4.95 | 0.57 |
| **Tuned Random Forest** | **3.23** | **4.85** | **0.59** |

The tuned Random Forest clearly beats the mean baseline, but its improvement over the simple persistence forecast (“tomorrow = today”) is small—about **1% in MAE**. That is an important result, not something to hide. The model is strongest on ordinary days and tends to under-predict sharp high-pollution episodes.

## Most important finding

`pm25_lag1`—yesterday's PM2.5—is by far the most useful feature. Wind speed and wind direction are the next important predictors, followed by lagged city-wide pollution measures. Rolling means, calendar features, and satellite indices contribute very little once recent pollution history is available.

Feature importance is interpreted as **predictive usefulness, not causation**.

## Folder structure

```text
London-Air-Pollution-Regression/
├── 01_Notebook/
│   └── London_Air_Pollution_Regression.ipynb
├── 02_HTML_Report/
│   └── London_Air_Pollution_Regression.html
├── 03_Data/
│   └── Air-01-London_data.csv
├── 04_Figures/
│   └── 13 analysis figures
├── 05_Documentation/
│   └── README.md
├── requirements.txt
└── LICENSE
```

The organization follows the same clean portfolio philosophy as the Apple App Store project while allowing the visual/report layer to be more advanced.

## Run the notebook

From the project root:

```bash
pip install -r requirements.txt
cd 01_Notebook
jupyter notebook London_Air_Pollution_Regression.ipynb
```

Use **Kernel → Restart & Run All**. The notebook reads from `../03_Data/` and saves figures to `../04_Figures/`.

To regenerate the HTML export:

```bash
jupyter nbconvert --to html London_Air_Pollution_Regression.ipynb \
  --output ../02_HTML_Report/London_Air_Pollution_Regression.html
```

The delivered HTML is a portfolio-style report with a hero, result cards, workflow ribbon, section navigation, search, dark/light mode, responsive layout, figure viewing, back-to-top control, and print-friendly styling.

## Limitations

This is a predictive—not causal—analysis of one London station over four years. The period includes COVID-era disruption, weather variables represent observed prior-day conditions rather than forecasts, daily averages hide within-day behavior, and the largest errors occur on unusually high-pollution days. The small dataset also limits generalization to other stations or cities. This notebook is a learning/portfolio project, not a production forecasting service.
