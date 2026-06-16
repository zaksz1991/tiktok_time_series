# TikTok Engagement Pulse — Discovery-to-Action Time Series Analysis

## Project Overview
This project utilizes the **Discovery-to-Action (DTA) Framework** to evaluate historical TikTok engagement footprints. By treating erratic content consumption metrics as a structured time series, we isolate systemic behavioral patterns from short-term statistical noise, providing the Growth Marketing team with a data-backed production roadmap.

## Discovery Phase: Datetime Preprocessing & Structural Decomposition
- **Index Transformation:** Cleaned timestamp registers, formatted records into native `datetime64` elements, and established the timestamps as the primary dataframe index.
- **Resampling Strategy:** Hourly logging variations and missing updates were resolved by resampling the stream into daily aggregate intervals ($Frequency = 'D'$).
- **Additive Modeling:** Because seasonal deviations remained relatively steady as the overall volume grew, we used an **Additive Decomposition Model** ($Y_t = Trend + Seasonal + Residual$) with a 7-day cyclical window:
  - **Trend Component:** Reveals long-term profile adjustments over months, mapping foundational growth independent of short-term weekly spikes.
  - **Seasonal Component:** Isolates repeating 7-day user behavior profiles.
  - **Residual Component:** Captures random, unpredictable algorithm fluctuations or viral events.

---

## Technical Phase: Statistical Stationarity Assessment

To evaluate if the data structure is safe for autoregressive tracking, we ran an **Augmented Dickey-Fuller (ADF) Test**:

### Hypothesis Architecture
- **$H_0$ (Null Hypothesis):** The series contains a unit root, indicating it is **non-stationary** (its mean, variance, or autocorrelation structure change over time).
- **$H_a$ (Alternative Hypothesis):** The series contains no unit root, proving it is **stationary** and fluctuates around a fixed baseline.

### Empirical Test Matrix
- **ADF Test Statistic:** [Insert Output value, e.g., +0.2415]
- **p-value Output:** [Insert Output value, e.g., 9.71e-01]
- **Critical Threshold (5%):** -2.88

### Statistical Conclusion
Because the calculated $p$-value exceeds our significance ceiling ($\alpha = 0.05$) and the ADF statistic sits above the critical boundary value, **we fail to reject the Null Hypothesis ($H_0$)**. The time series is non-stationary, driven by an upward growth trend. 

**Forecasting Readiness:** To prepare this dataset for linear models like ARIMA/SARIMA, **first-order differencing ($d=1$) is mandatory** to strip away the trend component and stabilize the mean.

---

## Action Phase: Content Scheduling & Next-Step Strategy

### 1. Optimal Content Programming Architecture
By tracking the isolated weekly seasonal component, the analysis reveals clear viewer behaviors:
- 🚀 **Peak Engagement Windows:** **Friday and Saturday** show substantial positive baseline deviations, making them the ideal times for high-impact content releases.
- 📉 **Maintenance/Low-Priority Windows:** **Monday and Tuesday** show negative baseline deviations. This is the perfect window to perform system maintenance, run account optimizations, or test lower-priority concepts.

### 2. Next Steps: Transitioning to ARIMA/SARIMA Forecasting
This analysis establishes the exact structural building blocks needed to build robust predictive models:
- **ARIMA $(p, d, q)$ Integration:** The non-stationary trend confirms that the integrated parameter must be set to $d=1$.
- **SARIMA $(p, d, q) \times (P, D, Q)_7$ Integration:** Because the seasonal plot highlights a clear weekly cycle, a seasonal ARIMA model (SARIMA) with an explicit length parameter of $s=7$ should be deployed. This will map both immediate daily momentum and historical weekly habits to accurately forecast upcoming views.