# TikTok Engagement Pulse — Discovery-to-Action Time Series Analysis

## Project Overview
This project utilizes the **Discovery-to-Action (DTA) Framework** to evaluate historical TikTok engagement footprints. By treating erratic content consumption metrics as a structured time series, we isolate systemic behavioral patterns from short-term statistical noise, providing the Growth Marketing team with a data-backed production roadmap.

## Discovery Phase: Datetime Preprocessing & Structural Decomposition
- **Index Transformation:** Cleaned timestamp registers, formatted records into native `datetime64` elements, and established the timestamps as the primary dataframe index.
- **Resampling Strategy:** Hourly logging variations and missing updates were resolved by resampling the stream into daily aggregate intervals ($Frequency = 'D'$) to create a clean series spanning 120 distinct day cycles.
- **Additive Modeling:** Because seasonal deviations remained relatively steady as the overall volume grew, we used an **Additive Decomposition Model** ($Y_t = Trend + Seasonal + Residual$) with a 7-day cyclical window:
  - **Trend Component:** Reveals steady linear macro growth climbing from a baseline of ~36,000 views to over ~41,000 daily views.
  - **Seasonal Component:** Isolates repeating 7-day user behavior profiles.
  - **Residual Component:** Captures random, unpredictable algorithm fluctuations or viral events.

---

## Technical Phase: Statistical Stationarity Assessment

To evaluate if the data structure is safe for autoregressive tracking, we ran an **Augmented Dickey-Fuller (ADF) Test**:

### Hypothesis Architecture
- **$H_0$ (Null Hypothesis):** The series contains a unit root, indicating it is **non-stationary** (its mean, variance, or autocorrelation structure change over time).
- **$H_a$ (Alternative Hypothesis):** The series contains no unit root, proving it is **stationary** and fluctuates around a fixed baseline.

### Concrete Empirical Test Matrix (Verified Outputs)
- **ADF Test Statistic:** `+0.2117`
- **p-value Output:** `9.7289e-01` (0.9729)
- **Critical Threshold Value (1%):** `-3.4865`
- **Critical Threshold Value (5%):** `-2.8861`
- **Critical Threshold Value (10%):** `-2.5799`

### Statistical Conclusion
Because our calculated $p$-value ($0.9729$) sits far above our significance ceiling ($\alpha = 0.05$) and the ADF test statistic is greater than all critical value benchmarks, **we fail to reject the Null Hypothesis ($H_0$)**. The time series is non-stationary, driven by a strong upward growth trend.

**Forecasting Readiness:** To prepare this dataset for linear models like ARIMA/SARIMA, **first-order differencing ($d=1$) is mandatory** to strip away the trend component and stabilize the mean.

---

## Action Phase: Content Scheduling & Next-Step Strategy

### 1. Optimal Content Programming Architecture
By tracking the isolated weekly seasonal component, the analysis reveals clear viewer behaviors:
- 🚀 **Peak Engagement Windows:** **Friday (+4,514.80 views)** and **Saturday (+5,005.15 views)** show massive positive baseline deviations, making them the ideal times for high-impact content releases.
- 📉 **Maintenance/Low-Priority Windows:** **Monday (-3,415.52 views)** and **Tuesday (-2,773.09 views)** show substantial drops. This is the perfect window to perform system maintenance, run account optimizations, or test lower-priority concepts.

### 2. Next Steps: Transitioning to ARIMA/SARIMA Forecasting
This analysis establishes the exact structural building blocks needed to build robust predictive models:
- **ARIMA $(p, d, q)$ Integration:** The non-stationary trend confirms that the integrated parameter must be set to $d=1$.
- **SARIMA $(p, d, q) \times (P, D, Q)_7$ Integration:** Because the seasonal plot highlights a clear weekly cycle, a seasonal ARIMA model (SARIMA) with an explicit length parameter of $s=7$ should be deployed. This will map both immediate daily momentum and historical weekly habits to accurately forecast upcoming views.