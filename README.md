# COMP4501-gp450025015

## Data-Driven Identification of Optimal Inflation Hedging Assets in Hong Kong

This repository contains the code and supporting datasets for a research project on the inflation-hedging properties of major asset classes in Hong Kong.

The repository is intentionally organized around two working areas:

- `ARDL/`: final econometric work in R Markdown for the asset-level ARDL, ECM, and Fisher Hypothesis analysis
- `EDA/`: exploratory Python notebooks and local supporting data used for inspection, cleaning, and early-stage analysis

## Repository Policy

The `ARDL/` directory is treated as a stable analysis area. Its internal file and data layout is preserved to avoid breaking existing R Markdown scripts that rely on the current relative paths.

This means the repository does not yet follow a fully normalized `analysis/` and `data/` structure. That is a deliberate tradeoff to protect reproducibility of the existing final analysis code without editing the scripts themselves.

## Current Structure

```text
.
├── ARDL/
│   ├── *.Rmd
│   ├── README.md
│   └── FYP data/
│       ├── raw price data/
│       └── log_level data/
├── EDA/
│   ├── *.ipynb
│   ├── *.csv
│   └── README.md
└── docs/
    └── repo-structure.md
```

## Research Scope

The asset universe includes:

- Equities: Hang Seng Index (HSI), S&P 500, NASDAQ Composite (QQQ)
- Commodities: WTI Crude Oil, Spot Gold
- Foreign Exchange: JPY/HKD, CHF/HKD
- Fixed Income and Credit: Bloomberg Global Aggregate, HK 10-Year Government Bond
- Real Estate: Hang Seng Properties Index (HSP)

## Econometric Methodology

The final R Markdown files in `ARDL/` implement a consistent asset-by-asset econometric pipeline designed to test long-run inflation-hedging behavior in Hong Kong.

The workflow combines:

- ARDL bounds testing for cointegration
- unrestricted and restricted ECM estimation
- long-run coefficient interpretation
- Fisher Hypothesis testing
- standard post-estimation diagnostics

## ARDL Pipeline

Each R Markdown file follows a strict sequential pipeline to preserve statistical validity and make the outputs comparable across assets.

### Step 1: Data Initialization

Each script imports the relevant processed dataset with `read_excel()` and removes missing observations using `na.omit()`.

The working dataset typically contains:

- a timestamp or date column
- `log_price`, the natural logarithm of the asset price
- `cpi_month`, the natural logarithm of the Hong Kong CPI series

### Step 2: Stationarity Checks

The ARDL framework requires that no variable be integrated of order 2.

Each script defines a helper such as `test_stationarity()` to run the Augmented Dickey-Fuller test with `adf.test()` on:

- the level series
- the first-differenced series

This is used to confirm that the variables are admissible for ARDL estimation and are I(0) or I(1), not I(2).

### Step 3: Structural Break Identification

To reduce the risk of misleading cointegration results under regime change, the scripts estimate structural breaks using the Bai-Perron procedure through `breakpoints()`.

The resulting break dates are used to construct deterministic regime dummy variables such as:

- `regime_2`
- `regime_3`
- `regime_4`

These dummies are then included in the model specification to control for shifts in the long-run relationship.

### Step 4: Lag Selection and ARDL Specification

The core ARDL model is selected using `auto_ardl()` with a maximum lag search, typically up to 12 lags.

The selection criterion is AIC, which is used to balance fit and parsimony. The chosen lag orders are then passed to `ardl()` together with the CPI regressor and the structural break dummy variables.

### Step 5: Bounds Testing for Cointegration

After the ARDL model is fitted, `bounds_f_test()` is used to test for a long-run equilibrium relationship.

The scripts use `case = 3`, corresponding to an unrestricted intercept with no deterministic trend. If the F-statistic exceeds the relevant upper critical bound, the asset is treated as cointegrated with CPI.

### Step 6: Long-Run Coefficients and Fisher Hypothesis

When cointegration is established, the scripts extract long-run elasticities using `multipliers()`.

They then transform the model into an unrestricted ECM with `uecm()` and apply `linearHypothesis()` to test the Fisher Hypothesis restriction on the long-run CPI coefficient.

This stage addresses two questions:

- whether the long-run hedge coefficient is statistically different from zero
- whether it is statistically consistent with a one-for-one inflation hedge

### Step 7: Short-Run Dynamics

The short-run adjustment process is examined through the restricted ECM produced by `recm()`.

The main quantity of interest is the error-correction term, which measures the speed at which deviations from the long-run equilibrium are corrected. A negative and statistically significant adjustment term supports the validity of the cointegrating relationship.

### Step 8: Diagnostic Testing

The final step checks whether the ECM residuals satisfy standard assumptions used to assess econometric robustness.

The scripts run:

- `lmtest::bgtest()` for serial correlation
- `lmtest::bptest()` for heteroskedasticity
- `tseries::jarque.bera.test()` for residual normality

Together, these diagnostics provide a basic model adequacy check for each asset-level specification.

## Reproducing the ARDL Analysis

Open the required file in `ARDL/` with RStudio and knit the `.Rmd` document.

Required R packages include:

- `tidyverse`
- `readxl`
- `tseries`
- `strucchange`
- `ARDL`
- `car`
- `lmtest`

## Reproducing the EDA Notebooks

The notebooks in `EDA/` are exploratory and may depend on a local Python environment with packages such as:

- `pandas`
- `numpy`
- `matplotlib`
- `seaborn`
- `plotly`
- `scipy`
- `statsmodels`
- `ruptures`

These notebooks are not currently standardized into a locked Python environment.

## Professionalization Status

This repository has been cleaned up at the documentation and project-hygiene level without changing analysis code. The main remaining technical debt is path-coupled code inside the ARDL scripts. That has been intentionally left unchanged to reduce risk.

See [docs/repo-structure.md](/Users/uddashyakumar/Desktop/COMP4501-gp450025015/docs/repo-structure.md) for the detailed structure rationale.
