---
layout: post
title: What is Time series data
description: Data analysis
tag: Data
---

## Understanding the dataset is really important before we start analyzing

### 1) Read Data
When processing time series data, I first use `index_col` and `parse_date` while reading the data. I convert the date column into `datetime` format and set it as the index, which facilitates time-based analysis.

![1](/assets/Pasted%20image%2020250224115140.png)

### 2) Cleaning Data
1. If there are missing values, we can use methods like `fillna()` to fill them. The `fill` parameter allows us to use the latest observed value for missing entries.
2. To identify missing values:
   - `time_series_data.isnull().any()`: Identifies columns with missing values.
   - `time_series_data.isnull().sum()`: Counts missing values per column.

### 3) Visualization
Generating a graph for each column:

![2](/assets/Pasted%20image%2020250224115154.png)

### 4) Shifts (Method in Time Series Data)
Shifting allows comparison of current data with its past values using `datetimeindex`.

![3](/assets/Pasted%20image%2020250224115600.png)

### 5) Comparing Two Different Time Series Data
When comparing two time series attributes:
- Differences in scale can cause issues in visualization, correlation, machine learning, and distance metrics.
- Solutions include:
  1. **Min-Max Scaling**:
     $$ X' = \frac{X - X_{\min}}{X_{\max} - X_{\min}} $$
  2. **Z-score Normalization**:
     $$ X' = \frac{X - \mu}{\sigma} $$
  3. **First Point Z-score**:
     $$ X' = \frac{X}{X_0} $$

We generally use the last method for time series analysis.

### 6) Rolling Windows
A rolling window technique calculates the mean, standard deviation, or trend over a fixed period (e.g., 90 days), smoothing fluctuations.

![4](/assets/Pasted%20image%2020250224122300.png)

### 7) Expanding Windows
Unlike rolling windows, expanding windows include all past data points as the window grows over time.

![5](/assets/Pasted%20image%2020250224122821.png)

## ARMA & ARIMA Models

### 8) ARMA & ARIMA
- **Financial data is typically time series data**, evolving as time progresses.
- A random walk model: $$ P_t = P_{t-1} + \sigma_t $$
- Financial data often has a mean value, leading to:
  $$ P_t = P_{t-1} + u + \sigma_t $$

#### How to Represent Financial Data Using Equations
If a time series can be represented as:
  $$ P_t = \beta P_{t-1} + u + \sigma_t $$
  - If \( \beta = 1 \), the series is **not stable** (variance increases over time).
  - **Stability check**: If the series is unstable, we apply **differencing**.

#### Stability in Time Series Data
- **Definition**: A stable time series maintains its properties over time.
- **Tests for stability**:
  1. **Visualization**:
     - Stable time series should have **constant mean and variance**.
     - Trends or volatility indicate instability.
  2. **ACF (Autocorrelation Function)**:
     - Measures correlation with lagged values.
     - If ACF **cuts off** at a certain lag, the data **may be stationary**.
  3. **PACF (Partial Autocorrelation Function)**:
     - Helps in identifying AR processes.
  4. **Augmented Dickey-Fuller (ADF) Test**:
     - \( H_0 \): Time series is unstable.
     - \( H_a \): Time series is stable.
     - If p-value > 0.05, we **cannot reject** \( H_0 \) (i.e., the series is unstable).

If unstable, apply **ARIMA**:
- Differencing
- Trend, seasonality, and cyclicality removal:
  $$ Z_t = T_t + S_t + X_t $$
- Log transformation

### 9) White Noise
- Before modeling, check if the residuals are **white noise**.
- **White noise properties**:
  - ACF has a peak only at lag 0.
  - Values are independent.
- **Tests**:
  - **Box-Pierce Test** (or Ljung-Box Test for better accuracy).

### 10) ARMA Model (Requires Stable Data)
- **ARMA consists of two parts:**
  - **AR (AutoRegressive model):**
    $$ R_t = u + \beta R_{t-1} + \epsilon_t $$
  - **MA (Moving Average model):**
    $$ R_t = u + \theta \epsilon_{t-1} + \epsilon_t $$
  - **Combining AR and MA**:
    $$ R_t = u + \beta R_{t-1} + \theta \epsilon_{t-1} + \epsilon_t $$

### 11) ARIMA Model (Does Not Require Stability)
- **ARIMA (p, d, q) extends ARMA** by introducing differencing:
  - \( p \): Number of autoregressive terms (AR)
  - \( d \): Differencing order (to make data stationary)
  - \( q \): Number of moving average terms (MA)

### 12) Model Selection
- Choose \( p, q \) by examining **ACF** and **PACF**:
  - If **PACF cuts off** after lag \( p \), choose \( p \).
  - If **ACF cuts off** after lag \( q \), choose \( q \).

### 13) How to Train and Validate Models
- **Data Splitting**:
  - Train the model on training data.
  - Test on validation data (if unavailable, use **cross-validation**).
- **Cross-validation methods**:
  1. **K-Fold Validation**: Split data into \( K \) parts, rotating test and training sets.
  2. **Bootstrap**: Sample with replacement to create multiple training sets.
- **Model Selection Based on Error Metrics**:
  - **AIC (Akaike Information Criterion)** favors complex models.
  - **BIC (Bayesian Information Criterion)** favors simpler models.
  - Compare AIC and BIC curves to find an optimal model.

### 14) Traditional vs. Machine Learning Methods
- **ARMA & ARIMA** require assumptions:
  - Stationarity (or differencing to achieve it).
  - Future values depend linearly on past values.
- **Machine Learning (XGBoost, Random Forest, LSTM)**:
  - No stationarity assumptions.
  - Requires feature engineering (e.g., lag features, rolling windows).
  - Harder to interpret than ARIMA but can model complex structures.
