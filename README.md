# COMP4501-gp450025015

# Data-Driven Identification of Optimal Inflation Hedging Assets in Hong Kong - Codebase

## Overview
This repository contains the R Markdown (`.Rmd`) files used to generate the empirical results for the research project assessing the long-run inflation-hedging capabilities of ten distinct asset classes in Hong Kong. 

The econometric methodology relies on the Autoregressive Distributed Lag (ARDL) bounds testing approach, the Error Correction Model (ECM), and the Fisher Hypothesis. This repository provides complete transparency into the data processing, model selection, and diagnostic testing for each asset.

## Asset Universe
The analysis is applied individually across ten assets, converted to HKD where applicable. Each asset has its own dedicated `.Rmd` script to ensure clean processing and isolated environment variables. The assets evaluated include:
* **Equities:** Hang Seng Index (HSI), S&P 500 (SNP), NASDAQ Composite
* **Commodities:** WTI Crude Oil (WTI/HKD), Spot Gold (XAU/HKD)
* **Forex:** JPY/HKD, CHF/HKD
* **Fixed Income/Credit:** Bloomberg Global Aggregate Credit Index (Agg), HK 10-Year Government Bond
* **Real Estate:** Hang Seng Properties Index (HSP)

## Prerequisites & R Packages
To replicate the HTML outputs or run the `.Rmd` files locally, the following R packages are required:
* `tidyverse` (For data manipulation and transformation)
* `readxl` (For importing raw Excel datasets)
* `tseries` (For Augmented Dickey-Fuller Unit Root testing)
* `strucchange` (For Bai-Perron structural break detection)
* `ARDL` (For ARDL model estimation, optimal lag selection, and bounds testing)
* `car` (For Wald tests and linear hypothesis testing)

## Code Structure and Processing Pipeline
Each R Markdown file follows a strict, sequential pipeline to ensure statistical validity. Below is the documentation of how the data is processed in each script:

### Step 1: Data Initialization
The scripts begin by importing the raw time-series data using `read_excel()` and omitting any missing values (`na.omit()`). The dataset includes the timestamp, the natural logarithm of the asset price (`log_price`), and the natural logarithm of the Hong Kong Consumer Price Index (`cpi_month`).

### Step 2: Stationarity Checks (ADF Test)
The ARDL framework requires that no variables be integrated of order 2, I(2). 
* A custom function, `test_stationarity()`, is defined to execute the Augmented Dickey-Fuller (`adf.test()`) on both the levels and first differences of the asset and CPI data. 
* This confirms that all variables are either I(0) or I(1) before proceeding.

### Step 3: Structural Break Identification
To prevent false negatives in cointegration testing caused by major macroeconomic regime shifts:
* The Bai-Perron procedure is executed via the `breakpoints()` function to endogenously detect structural breaks in the long-run relationship.
* The script extracts the optimal break dates and generates deterministic dummy variables (`regime_2`, `regime_3`, etc.) to isolate and control for these specific market regimes in the final model.

### Step 4: Lag Selection and ARDL Specification
The core ARDL model is formulated.
* The script determines the optimal lag length using `auto_ardl()`, allowing for a maximum of 12 lags.
* The Akaike Information Criterion (AIC) is used to select the best-fitting model, ensuring a balance between model fit and parsimony.
* The `ardl()` function is then used to fit the model using the optimal lags and the previously generated structural break dummy variables.

### Step 5: ARDL Bounds Testing for Cointegration
To establish whether a long-run equilibrium exists:
* The `bounds_f_test()` function is executed on the fitted ARDL model. 
* The script specifies `case = 3` (unrestricted intercept, no deterministic time trend) to calculate the F-statistic. An F-statistic exceeding the upper critical bound (5.73 at the 5% level) confirms cointegration.

### Step 6: Long-Run Coefficients & The Fisher Hypothesis
Once cointegration is confirmed, the script evaluates the hedging efficacy:
* The `multipliers()` function extracts the long-run coefficient (Beta) representing the elasticity between the asset and CPI.
* The model is transformed into an Unrestricted Error Correction Model using `uecm()`.
* A Wald Test is performed via the `linearHypothesis()` function to test the strict Fisher Hypothesis (testing if the coefficient equals 1 by setting `L(cpi_month, 1) + L(log_price, 1) = 0`).

### Step 7: Short-Run Dynamics (Error Correction Model)
The script estimates the Restricted Error Correction Model to analyze short-term deviations:
* The `recm()` function is utilized to extract the short-run dynamics.
* The focus is on the Error Correction Term (`ect`), which represents the speed of adjustment back to the long-run equilibrium. A negative, statistically significant `ect` confirms the validity of the cointegrating relationship.

### Step 8: Diagnostic Testing
Finally, standard OLS diagnostic tests are run on the ECM residuals to ensure econometric robustness:
* **Breusch-Godfrey Test (`bgtest`):** Confirms the absence of serial correlation (a critical assumption for ARDL validity).
* **Breusch-Pagan Test (`bptest`):** Tests for heteroskedasticity.
* **Jarque-Bera Test (`jarque.bera.test`):** Evaluates residual normality. 

## Usage
To reproduce the findings, clone this repository, open the desired asset's `.Rmd` file in RStudio, and click **Knit -> Knit to HTML** to execute the pipeline and view the formatted results.
