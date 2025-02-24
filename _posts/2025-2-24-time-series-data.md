1) Read data
    1) When I process time series data, I first use `index_col` and `parse_date` in the process of reading the data. I convert the date column data into datetime format and set the date column as my index. This is conducive to the subsequent time-based analysis of the time series data.

    ![[Pasted image 20250224115140.png]]

2) Cleaning data
    1) If there are any missing values, then we need to use a method like `fillna()` to fill them out. We may need to use the parameter `fill`, which is a parameter to let the computer know we want to use the latest observed value to fill the missing value.
    2) In order to find out where the missing values are:
        1) `time_series_data.isnull().any()`
            - It will tell you which column has the missing value.
        2) `time_series_data.isnull().sum()`
            - It will give you how many missing values exist in each column.

3) Visualization
    1) It will give you the graph of each column.

    ![[Pasted image 20250224115154.png]]

4) Shifts (method in time series data)
    1) One thing that is really important in time series data is shifting. We can use different `datetimeindex` to shift the index to get a new period of that time series data. Therefore, we can compare the current data to its own past.

    ![[Pasted image 20250224115600.png]]

5) Comparing two different time series data (method in time series data)
    1) Whether we are comparing two time series attributes in one dataset or two dependent variables that are both time series data, we need to consider the problem of dimension. If the first dataset's range is from 100 to 500, and another one is from 10 to 50, different dimensions will cause several problems:
        - Visualization
        - Correlation
        - Machine Learning
        - Distance Metrics

    2) To solve these problems, normally we have three ways:
        1) **Min-Max Scaling**:
            \[
            X' = \frac{X - X_{\min}}{X_{\max} - X_{\min}}
            \]
        2) **Z-score**:
            \[
            X' = \frac{X - \mu}{\sigma}
            \]
        3) **Based on first point Z-score**:
            \[
            X' = \frac{X}{X_0}
            \]

    3) We tend to use the last method to analyze time series data.

6) Rolling Windows (method in time series data)
    1) Windows is a technique when dealing with time series data. It allows us to calculate the mean, standard deviation, and trend within a certain period. In this case, the window size is fixed at 90 days.

    ![[Pasted image 20250224122300.png]]

    2) Using rolling windows can help us smooth time series data, extract trends, and reduce the impact of short-term fluctuations.

7) Expanding Windows (method in time series data)
    1) The difference between expanding windows and rolling windows is that the former contains all the data. Rolling windows have a fixed size, whereas expanding windows can infinitely expand until they contain all data points.

    ![[Pasted image 20250224122821.png]]


8) ARMA & ARIMA (model)
    1) Most financial data are time series data since they are generated as time flows. We can use an equation:
       \[
       P_t = P_{t-1} + \sigma_t
       \]
       A random walk is not able to make predictions because the noise is totally random. Most financial data have a mean value, so we add \( u \) to the equation:
       \[
       P_t = P_{t-1} + u + \sigma_t
       \]

    2) How we use equations to represent financial data:
        1) Since we already know that a time series can be represented as:
           \[
           P_t = \beta P_{t-1} + u + \sigma_t
           \]
           If \( \beta = 1 \), then we say this time series is **not stable**. The standard deviation will increase over time. Before analyzing the data, we first need to test whether the time series is stable. If not, we need to apply differencing.

    3) What is stable data?
        1) **Definition**: The most ideal property is to assume that the basic characteristics of random variables remain unchanged over a long period of time in the future, the same as in the past. Since strong stationarity is too ideal, we try to transform the data into **weak stationarity** before analysis.
        2) **How we test stationarity:**
            1) **Visualization:**
                - Mean remains constant over time.
                - Variance remains constant over time.
                - If the graph shows trends or volatile changes, it may indicate non-stationary data.
            2) **Autocorrelation Function (ACF):**
                - The correlation between the time series data and its own lag. This is essentially the Pearson correlation coefficient.
                - If the ACF cuts off at a certain lag point, the data may be stationary.
                - ACF should drop rapidly to 0.
            3) **Partial Autocorrelation Function (PACF):**
                - If the PACF cuts off at a certain lag point, the data may be stationary.
            4) **Augmented Dickey-Fuller (ADF) Test:**
                - Null Hypothesis (\( H_0 \)): Time series is **unstable**.
                - Alternative Hypothesis (\( H_a \)): Time series is **stable**.
                - If p-value \( > 0.05 \), we fail to reject \( H_0 \), meaning the data is **not stationary**.

        3) **What if the data is unstable?**
            - We use **ARIMA** or apply **differencing**, followed by ACF and PACF tests.
            - Use **Trends, Seasonality, and Cyclicality**:
                \[
                Z_t = T_t + S_t + X_t
                \]
                - By reducing these three trends, we can obtain a stable series.
            - **Transformation (e.g., log transformation)**.

    4) **White Noise:**
        - Before we input stable data into a model, we need to test whether it is **white noise**.
        - White noise has an ACF value only at time 0, and other values are very small because each observation is independent.
        - When decomposing a time series, the residuals should be white noise. If not, we may need to continue modeling the residuals.
        - **How to test for white noise:**
            - Use the **Box-Pierce test** (or its improved version, the **Ljung-Box test**) to determine if the time series is white noise.

    5) **ARMA (Autoregressive Moving Average Model):**
        - ARMA requires **input data to be stationary**.
        - **AR (Autoregressive) Model:**
            \[
            R_t = u + \beta R_{t-1} + \epsilon_t
            \]
            - If \( \beta \) is close to 1, it behaves like a **random walk**.
            - If \( \beta \) is close to 0, it is **white noise**.
            - If \( \beta \) is negative, it will eventually stabilize around \( u \).

        - **MA (Moving Average) Model:**
            \[
            R_t = u + \theta \epsilon_{t-1} + \epsilon_t
            \]
            - The value depends on the previous random term and the current random term.

        - **ARMA Model (Combining AR & MA):**
            \[
            R_t = u + \beta R_{t-1} + \theta \epsilon_{t-1} + \epsilon_t
            \]
            - This model describes a weakly stationary random process.

    6) **ARIMA (Autoregressive Integrated Moving Average Model):**
        - ARIMA is an **upgraded version of ARMA**.
        - It **does not require input data to be stationary** because it includes differencing to transform unstable data into stable data.

---

9) **How We Choose Model Parameters**
    1) **ARMA(p, q) Model:**
        - \( p \): The number of autoregressive (AR) terms.
        - \( q \): The number of moving average (MA) terms.

    2) **ARIMA(p, d, q) Model:**
        - \( p \): The number of autoregressive (AR) terms.
        - \( q \): The number of moving average (MA) terms.
        - \( d \): The number of **differencing steps** needed to make the data stationary.

    3) **How to choose \( p, d, q \) using ACF and PACF:**
        - **AR Model (\( p \)):** If PACF cuts off after \( p \), then we choose \( p \).
        - **MA Model (\( q \)):** If ACF cuts off after \( q \), then we choose \( q \).
        - **Differencing (\( d \)):** If the time series is non-stationary, apply differencing until it becomes stationary.

---

10) **How We Choose a Model**
    1) **Split the dataset:**
        - Train the model on the **training set** and validate on the **validation set**.
        - Since real-life problems usually lack validation sets, we use **cross-validation**.

    2) **Cross-Validation Techniques:**
        - **K-Fold Validation:**
            - Split the data into \( K \) parts.
            - Use \( K-1 \) parts for training and 1 part for testing, repeating this process \( K \) times.
            - Compute the average error across all folds.
        - **Bootstrap Sampling:**
            - Randomly sample \( N \) observations with replacement to create a training set.
            - The remaining observations form the test set.

    3) **Choosing Between Models Using Error Metrics:**
        - **AIC (Akaike Information Criterion):** Prefers more complex models.
        - **BIC (Bayesian Information Criterion):** Prefers simpler models.
        - Plot AIC and BIC and identify the optimal balance between complexity and simplicity.

---

11) **Limitations of ARMA & ARIMA Models**
    - ARMA and ARIMA are **statistical models** that assume:
        - The time series is **stationary** (or can be made stationary by differencing).
        - Future values are **linear combinations** of past values.
        - Modeling only relies on **past target variables**.

12) **Machine Learning vs. ARIMA**
    - Machine learning models (e.g., **XGBoost, Random Forest, LSTM**) do not require stationarity assumptions.
    - Feature engineering is required (e.g., **lag features, sliding windows**).
    - Machine learning models are harder to interpret compared to ARIMA.
