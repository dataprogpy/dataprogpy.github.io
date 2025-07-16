---
icon: material/numeric-3
---

Great. Let's build out the content for Module 3.

This module transitions from analysis to prediction. We'll start with a conceptual overview of the models, as planned, before moving to implementation.

-----

### **Module 3: Time Series Forecasting Models**

Having analyzed our data's structure, we can now move to the primary goal of most time series projects: **forecasting**. This module introduces several powerful statistical models, explaining what they do conceptually before showing how to implement them in Python.

-----

### **A Conceptual Guide to Forecasting Models**

Understanding which model to use for a specific job is the most critical skill in forecasting. Each model has a different purpose and set of assumptions.

#### **ARIMA: The Foundational Forecaster**

The **ARIMA** model is the workhorse for forecasting stationary (or stationarized) time series data. The name itself describes how it works:

  * **AR (AutoRegressive)** 📈: This part assumes that the future value is a linear combination of past **values**. The "p" parameter in the model, which tells us how many past values to include, is typically identified by looking for significant spikes in the **PACF plot**.

  * **I (Integrated)** 🔨: This refers to the **differencing** step. The "d" parameter simply states how many times we had to difference the data to make it stationary. If the data was already stationary, d=0.

  * **MA (Moving Average)** 📉: This part assumes that the future value is a function of past **forecast errors**. The "q" parameter, which tells us how many past errors to consider, is identified by looking at the **ACF plot**.

An ARIMA model is specified by its order `(p, d, q)`. The analysis you performed in Module 2 is what allows you to choose these parameters intelligently.

-----

#### **SARIMA: The Seasonal Specialist**

If your data exhibits clear seasonality, the **SARIMA** model is the appropriate tool. It is an extension of ARIMA that adds a second set of parameters to account for the seasonal patterns.

Think of it as two ARIMA models working in tandem: one for the non-seasonal part and one for the seasonal part. It's specified as `(p,d,q)(P,D,Q)m`, where:

  * `(p,d,q)` are the non-seasonal parameters we've already discussed.
  * `(P,D,Q)` are the seasonal counterparts. They work just like the non-seasonal parameters but operate on data at the seasonal lag.
  * `m` is the number of periods in each season (e.g., m=12 for monthly data with a yearly season).

-----

#### **GARCH: The Volatility Forecaster**

ARIMA and SARIMA are used to model and predict the **value** (the mean) of a series. **GARCH** models are used for a different purpose: modeling and predicting the **variance** or **volatility** of a series.

This is particularly useful in finance, where the volatility of an asset's return is a key measure of risk. GARCH is designed to capture **volatility clustering**, a common phenomenon where periods of high volatility are followed by more high volatility, and periods of calm are followed by more calm. While ARIMA might predict the stock price, GARCH would predict how *unstable* or *risky* that price might be.

-----


