---
layout: post
title: What is Time series data
description: Data analysis
tag: Data
---

## Understanding the dataset is really important before we start analyzing

### Read Data
When I process time series data, I first use `index_col` and `parse_date` in the process of reading the data. I convert the date column data into datetime format and set the date column as my index. This is conducive to the subsequent time-based analysis of the time series data.

![1](/assets/Pasted image 20250224115140.png)

### Cleaning Data
1. If there are any missing values, then we need to use methods like `fillna()` to fill them out. We may need to use the `fill` parameter to let the computer know we want to use the latest observed value to fill the missing value.
2. To find out where the missing values are:
   - `time_series_data.isnull().any()`: It will tell you which column has missing values.
   - `time_series_data.isnull().sum()`: It will show the number of missing values in each column.

### Visualization
It will give you the graph of each column.

![2](/assets/Pasted image 20250224115154.png)

### Shifts (Method in Time Series Data)
One thing that is really important in time series data is shifting. We can use different `datetimeindex` to shift the index to get a new period of that time series data. Therefore, we can compare the current data to its past.

![3](/assets/Pasted image 20250224115600.png)

### Comparing Two Different Time Series Data
Whether we are comparing two time series attributes in one dataset or two dependent variables that are both time series data, we need to consider the problem of dimension. If the first data range is from 100 to 500 and the second one is from 10 to 50, different dimensions will cause several problems:
1. Visualization
2. Correlation
3. Machine Learning
4. Distance Metrics

To solve these problems, we normally have three methods:
- **Min-Max Scaling**:
  \[
  X' = \frac{X - X_{\min}}{X_{\max} - X_{\min}}
  \]
- **Z-score Normalization**:
  \[
  X' = \frac{X - \mu}{\sigma}
  \]
- **First Point Normalization**:
  \[
  X' = \frac{X}{X_0}
  \]

We tend to use the last method for time series data.

### Rolling Windows (Method in Time Series Data)
Windows is a technique used to process time series data. It allows us to calculate the mean, standard value, and trend within a certain period. In this case, the window size is fixed at 90 days.

![4](/assets/Pasted image 20250224122300.png)

Using rolling windows can help smooth time series data, extract trends, and reduce the impact of short-term fluctuations.

### Expanding Windows (Method in Time Series Data)
The difference between expanding windows and rolling windows is that rolling windows have a fixed size, whereas expanding windows grow indefinitely until they contain all data points.

![5](/assets/Pasted image 20250224122821.png)

### ARMA & ARIMA (Model)
#### Financial Time Series and Random Walk
Most financial data are time series data since they evolve over time. The random walk equation is:
\[
P_t = P_{t-1} + \sigma_t
\]
Random walks cannot be used for prediction since their noise is entirely random. Most financial data have a mean value, leading to the equation:
\[
P_t = P_{t-1} + u + \sigma_t
\]

#### Testing for Stationarity
Before analyzing data, we need to check whether the time series is stable. If it is not, we use **Differencing**.

A stable time series satisfies:
1. Mean does not change over time.
2. Variance does not change over time.
3. The absence of trending or volatile behavior.

Tests for stationarity:
- **Autocorrelation Function (ACF):** Measures correlation with lagged values.
- **Partial Autocorrelation Function (PACF):** Helps in identifying AR processes.
- **Augmented Dickey-Fuller (ADF) Test:**
  - Null Hypothesis (H₀): Time series is unstable.
  - Alternative Hypothesis (H₁): Time series is stable.
  - If p-value > 0.05, we cannot reject H₀ (unstable series).

#### ARMA Model (Requires Stationary Data)
- **AR (AutoRegressive Model):**
  \[
  R_t = u + \beta R_{t-1} + \epsilon_t
  \]
- **MA (Moving Average Model):**
  \[
  R_t = u + \theta \epsilon_{t-1} + \epsilon_t
  \]
- **ARMA (Combining AR and MA):**
  \[
  R_t = u + \beta R_{t-1} + \theta \epsilon_{t-1} + \epsilon_t
  \]

#### ARIMA Model (Handles Non-Stationary Data)
- Introduces **Differencing (d)** to transform unstable data into stable data.
- Parameters:
  - **p**: Number of autoregressive terms.
  - **d**: Differencing order.
  - **q**: Number of moving average terms.

### Model Selection
1. Use **ACF and PACF** to determine parameters.
   - If PACF cuts off after p, choose p.
   - If ACF cuts off after q, choose q.
2. **Cross-Validation** for model performance.
3. **Error Metrics:**
   - AIC (Akaike Information Criterion) favors complex models.
   - BIC (Bayesian Information Criterion) favors simple models.
   - Compare AIC and BIC to find an optimal balance.

### Machine Learning for Time Series
- ARIMA relies on statistical assumptions.
- Machine Learning methods (XGBoost, Random Forest, LSTM):
  - No stationarity requirement.
  - Requires feature engineering.
  - Harder to interpret than ARIMA but captures complex patterns.
