---
layout: post
title: What is time series data
description: What is time series data
tag: Data
---

# Time series data

 Understanding the dataset is really important before we start analyzing

1. Read data

   1. When I process time series data, I first use `index_col`and `parse_date` in the process of reading the data. I convert the date column data into datetime format and set the date column as my index. This is conducive to the subsequent time-based analysis of the time series data.

2. Cleaning data

   1. f there are any missing value, then we need to use method like `fillna()` to fill them out. We may need to use parameter `fill`, which is a parameter to let computer knows we want to use the latest observed value to fill the missing value.
   2. In order to find out where is the missing value
      1. `time_series_data.isnull().any()` ->  It will tell you which column has the missing value
      2. `time_series_data.isnull().any()` ->  It will tell you which column has the missing value

3. Visualization

   1. It will give you the graph of each column

4. Shifts (method in time series data)

   1. One thing that is really important in time series data is shifting. We can use different `datetimeindex` to shift the index to get a new period of that time series data. Therefore, we can compare right now data to its own past.

5. Comparing two different time series data (method in time series data)

   1. Whether we are comparing two time series attributes in one data or two dependent variable which are both time series data, we need to consider the problem of dimension. If the first data's range is from 100 to 500, another one is from 10 to 50, different dimension will cause several problem

      1. Visualization
      2. Correlation
      3. Machine Learning
      4. Distance Metrics

   2. To solve those problem, normally we have three ways

      1. Min-Max Scaling

         1. $$
            X' = \frac{X - X_{\min}}{X_{\max} - X_{\min}}
            $$

      2. Z-score

         1. $$
            X' = \frac{X - \mu}{\sigma}
            $$

      3. Based on first point Z-score

         1. $$
            X' = \frac{X}{X_0}
            $$

      4. We tend to use the last way to analyze time series data

6. Rolling Windows (method in time series data)

   1. Windows is a technique when we deal with time series data. It can allow us to calculate the mean, standard value, trend in certain period. In this case, window is not going to change. The window size is always 90 days
   2. Using rolling windows can help us smooth time series data, extract trends and reduce the impact of short-term fluctuations.

7. Expanding Windows  (method in time series data)

   1.  The difference of expanding windows and Rolling windows is the prior contains all the data.  Rolling windows get a fixed size, however expanding windows can infinitely expand until it contains all data points

8. ARMA & ARIMA (model)

   1. Most of the financial data are time series data since they are generated as time flow. We can use an equation $P_t = P_{t-1} + sigma_t$. Random walk is not able to do prediction because the noise is totally random. Most of the financial data has a mean value, then we add u to the equation $P_t = P_{t-1} + u + sigma_t$
   2. How we use equation represent financial data
      1. Since we already know that an time series can be represented as $P_t = beta*P_{t-1} + u + sigma_t$. If beta=1, then we say this time series is not stable. The standard deviation will increase as time flow. Before we analyze the data, we first need to test whether this time series data is stable. If not, we need to use Differencing.
   3. What is a stable data
      1. Defn: The most ideal property is to assume that the basic characteristics of random variables remain unchanged over a long period of time in the future, the same as in the past. Generally speaking, strong stationarity is too ideal, so we try to transform the data into weak stationarity before analyzing it.
         1. How we test it
            1. Visualization
               1. The characteristic of stable time series data is
                  1. mean won't change over the time
                  2. variance won't change over the time
                  3. f the graph shows trending or volatile changes, it may indicate non-stationary data.
            2. ACF
               1. The correlation between the measurable time series data with its own lag. Basically, it is Pearson correlation coefficient .
               2. if the ACF is cut off at a certain lag point, it means that the data may be stationary.
               3. ACF drops rapidly to 0
            3. PACF
               1.  If the PACF is cut off at a certain lag point, it means that the data may be stationary.
            4. Augmented Dickey-Fuller(ADF)
               1. $H_0$: time series is unstable / $H_a$: time series is stable
               2. If p value > 0.05, then we cannot reject $H_0$. Vice versa
      2. What if it is unstable:
         1. we use ARIMA/ we do the differencing (and then continue doing PACF, ACF)
         2. Using Trends, seasonality and cyclicality
            1. time series data -> $Z_t=T_t+S_t+X_t$
            2. By reducing this three trend, we can get a stable series
         3. transforming (log)
   4. White noise
      1. Before we put our stable data into model, we need to test whether that data is white noise or not. If it is, then we cannot put it into models. If we look at the ACF, white noise's ACF get value only at time 0, other value is really small cause each value is independent to each other. When decomposing a time series, it is reasonable that the residual should be white noise. If it is not white noise, you can consider continuing to model the residual.
      2. How to test it
         1. The Box-Pierce test (upgraded version: Ljung-Box test) is used to determine whether the time series is white noise.
   5.  ARMA (input needs to be stable data)
      1. First introducing ARMA. ARMA requires input time series data is weak stable. ARMA contains two parts. The first one is AR, another one is MA. ARMA specific that output variable depends linearly on its own previous value and random term.
      2. For **AR**, the equation is $R_t=u + beta*R_{t−1} +ϵ_t$. Equation tells us the value depends on the previous value and the random term. If beta close to 1, it close to random walk. If beta close to 0, it is white noise, the value not depend on previous value. IF beta goes to negative, then eventually it will get close to u.
      3. For **MA**, the equation is $R_t=u + theta*ϵ_{t−1}+ϵ_t$. Equation tells us the value depends on the previous random term and the random term.
      4. Combining AR and MR, we get **ARMA**. We use these two polynomial to describe weak stable random process. The equation is $R_t=u + beta*R_(t−1) + theta*ϵ_{t−1} +ϵ_t$\
   6. ARIMA (input does not need to be stable data)
      1. ARIMA is kind of upgraded version of ARMA. It does not require input data to be stable because it will help us transform an unstable data to stable data.

9. How we choose model

   1. Split data set
      1. We first train our model in training set, and test it in validation set. We see the error in extra-sample and use that error to choose model. The problem is, in real life problem, we normally don't have a validation set. To solve it, we can get validation set from training set. In this method, we can use a famous method called cross validation.
      2. Cross validation
         1. the reason why we use cross validation is because different way to split data set may cause the error unstable.
         2. This method help us divide the data set multiple times and get different test error.
         3.  K-fold validation: Divide the data into K parts, use 1 part as the test set each time, and the remaining K-1 parts as the training set. We repeat this k times. Get a mean value of error in the end.
         4. Bootstrap: take N sample randomly from training set with replacement, get a new training set. The other is test set.
   2. Change error into other from
      1. In real life problem, sometimes we might get a small testing data, we cannot use them frequently so that it will get overfitted. Calculating the error actually require a lot of data.
      2. We then can use some statistic method like AIC, BIC.
         1. since AIC and BIC prefer different kind of model. For BIC, it prefer easy model, AIC prefer complicated model. In this case, we can draw the graph of these two method and see which point they interact with each other.

10. How we choose model parameter

    1. ARMA(p,q)
       1. P is for AR(p), which means the value is decided by previous p value, Q is for MR(q), which means the value is decided by previous q error term.
    2. ARIMA(p,d,q)
       1. P and Q are same as ARMA. It introduce d, stands for differencing to address unstable time series data.
    3. If we want to choose those parameter, we need to look at ACF and PACF.
       1. Notice that, AR means$X_t$ is directly affected by p past values , while earlier values are indirectly affected less strongly. MA means the influence of past errors on current values. If PACF suddenly cut off after P, it means that the value after P has no impact on current values. If ACF suddenly cut off after Q, we choose it as q.
       2. The reason why we use ACF to q, PACF to AR(p) is because after removing all intermediate variables, the current value is directly related to the past value and thus becomes 0 (truncated) after p

11. ARMA and ARIMA is model in statistic. It needs some assumption like The time series is stationary (or can be made stationary by differencing), Future values are linear combinations of past values, modeling only relies on past target variables.

12. Machine learning methods (such as XGBoost, Random Forest, LSTM) are not limited by time series assumptions. Need to construct features (such as lag features, sliding windows). The model is difficult to interpret and it is difficult to clarify the data structure like ARIMA.
