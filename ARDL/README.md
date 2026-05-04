# ARDL Analysis

## Purpose

This directory contains the final R Markdown files used for the econometric analysis of inflation-hedging assets in Hong Kong.

The methods used here include:

- ARDL bounds testing
- unrestricted and restricted ECM estimation
- long-run multiplier extraction
- Fisher Hypothesis testing
- standard residual diagnostics

## Workflow

Each asset-level `.Rmd` file follows the same broad econometric pipeline so that results remain comparable across the full asset universe.

### Step 1: Data Initialization

The script loads the relevant processed dataset with `read_excel()` and removes missing values with `na.omit()`.

### Step 2: Stationarity Checks

The script applies Augmented Dickey-Fuller testing with `adf.test()` on both levels and first differences to verify that the series are I(0) or I(1), rather than I(2).

### Step 3: Structural Break Identification

The long-run relationship is inspected for structural breaks using `breakpoints()`. Break dates are then converted into regime dummy variables used in the model specification.

### Step 4: Lag Selection and ARDL Estimation

The preferred lag structure is selected using `auto_ardl()`, usually with an AIC-based search over lag orders up to 12. The selected specification is then estimated with `ardl()`.

### Step 5: Bounds Testing

The fitted model is evaluated with `bounds_f_test()` under `case = 3` to assess whether a cointegrating relationship exists between the asset and CPI.

### Step 6: Long-Run Interpretation

If cointegration is present, the long-run coefficient is extracted with `multipliers()`, and the Fisher Hypothesis is tested through `uecm()` plus `linearHypothesis()`.

### Step 7: Short-Run Dynamics

The restricted error-correction representation is estimated with `recm()`, with particular attention to the sign and significance of the error-correction term.

### Step 8: Diagnostic Testing

Residual diagnostics are then performed using:

- `lmtest::bgtest()`
- `lmtest::bptest()`
- `tseries::jarque.bera.test()`

This gives each asset-level report the same high-level methodological structure even where specific breakpoints or lag choices differ.

## Stability Policy

The internal structure of this directory is intentionally preserved.

The existing `.Rmd` files reference datasets through the current relative paths inside `FYP data/`. Because of that coupling, this directory should be treated as a stable unit unless the code is explicitly refactored.

## Contents

- `*.Rmd`: asset-level econometric reports
- `FYP data/raw price data/`: raw market and source price files
- `FYP data/log_level data/`: processed input files used by the ARDL scripts

## Usage

Open the relevant `.Rmd` file in RStudio and knit it directly from this directory layout.

Required R packages include:

- `tidyverse`
- `readxl`
- `tseries`
- `strucchange`
- `ARDL`
- `car`
- `lmtest`

## Note

If a future cleanup is approved, this directory should be migrated only together with a coordinated path refactor of all dependent scripts.
