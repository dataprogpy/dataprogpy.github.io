---
icon: material/numeric-4
---

### **Model Implementation and Evaluation**

Now we apply these concepts using the `statsmodels` and `arch` Python libraries.

#### **Code Demo: Fitting and Forecasting with ARIMA**

We'll use the `statsmodels` library to fit an ARIMA model. The key is to provide the `order=(p,d,q)` that you determined during your analysis in Module 2.

```python
import polars as pl
import statsmodels.api as sm
import matplotlib.pyplot as plt

# Assume 'differenced_series' is our stationary data from Module 2
# From our ACF/PACF analysis, let's say we chose an order of (1,0,1)
# Note: Since the data is already differenced, 'd' is 0 here.

# Fit the ARIMA model
model = sm.tsa.ARIMA(differenced_series, order=(1, 0, 1))
results = model.fit()

# Print the model summary
print(results.summary())

# Generate forecasts
forecast_steps = 24
forecast = results.forecast(steps=forecast_steps)

# Plot the original series and the forecast
plt.figure(figsize=(12, 6))
plt.plot(differenced_series, label='Observed')
plt.plot(forecast, label='Forecast', color='red')
plt.title('ARIMA Forecast')
plt.legend()
plt.show()

```

The `results.summary()` output provides a wealth of information, including the model coefficients and their statistical significance, which helps you validate your model's structure.

-----

#### **Model Evaluation**

To evaluate a forecasting model, we need to compare its predictions to actual, held-out data. Because time series data has an order, standard k-fold cross-validation is not appropriate. Instead, we use a method called **walk-forward validation** or a simple **train-test split** based on time.

1.  **Split the data**: Reserve the last portion of your data as the test set (e.g., the last 12 months).
2.  **Train the model**: Fit your model only on the training data.
3.  **Forecast**: Generate forecasts for the same period as the test set.
4.  **Compare**: Measure the error between your forecasts and the actual values in the test set.

Common evaluation metrics include:

  * **Root Mean Squared Error (RMSE)**: The square root of the average of squared errors. It penalizes large errors more heavily.
  * **Mean Absolute Error (MAE)**: The average of the absolute errors. It's easier to interpret as it's in the same units as the original data.

  You are correct. Let's complete the content for Module 3. Here are the remaining sections that bridge the gap between manual statistical modeling and more modern, automated approaches.


You are correct to point that out.

Upon review, while the concept has been mentioned and used implicitly in a code demo, we have not yet created a dedicated section that explains **backtesting** or **walk-forward validation** in detail with its own focused code example. The existing content assumes the student might infer the process, which is not ideal for an introductory course.

A dedicated section is needed to properly explain this crucial evaluation methodology.

-----

### **Model Evaluation: Backtesting Your Forecast**

After building a model, you must evaluate its performance on data it has never seen. In time series forecasting, this evaluation process is called **backtesting**. The goal is to simulate how your model would have performed in the past.

This is fundamentally different from the standard k-fold cross-validation used in other machine learning tasks. You **cannot** shuffle your data and choose random splits because that would destroy the temporal order, allowing the model to "see into the future." Instead, backtesting always involves a chronological split.

The simplest and most common backtesting method is a **train-test split based on time**:

1.  **Training Set**: You use the older portion of your data to train your model.
2.  **Test Set (or Hold-Out Set)**: You reserve the most recent portion of your data to test the model.
3.  **Forecast**: You train the model on the training set and then generate forecasts for the time period covered by the test set.
4.  **Evaluate**: You compare your forecasts to the actual values in the test set to calculate error metrics.

-----

#### **Code Demo: A Simple Backtesting Workflow**

Let's demonstrate a clear backtesting workflow using our Air Passengers data and an ARIMA model.

```python
import polars as pl
import statsmodels.api as sm
from sklearn.metrics import mean_squared_error
import numpy as np
import matplotlib.pyplot as plt

# --- 1. Load and Prepare Data ---
file_path = 'path/to/your/AirPassengers.csv'
df = pl.read_csv(file_path).with_columns(
    pl.col("Month").str.to_datetime(format="%Y-%m")
)
series = df.to_pandas().set_index('Month')['Passengers']

# --- 2. Split the Data into Training and Test Sets ---
# The test set will be the last 24 months. The rest is for training.
train_data = series[:-24]
test_data = series[-24:]

print(f"Training data points: {len(train_data)}")
print(f"Test data points: {len(test_data)}")

# --- 3. Train the Model on the Training Set ---
# We'll use a SARIMA model since this data is seasonal
# (p,d,q)(P,D,Q)m parameters are chosen for demonstration
model = sm.tsa.SARIMAX(train_data,
                        order=(2, 1, 1),
                        seasonal_order=(1, 1, 1, 12))
results = model.fit()

# --- 4. Generate Forecasts for the Test Period ---
# The 'start' and 'end' parameters correspond to the index of the test data
forecast_start = test_data.index[0]
forecast_end = test_data.index[-1]
forecast = results.predict(start=forecast_start, end=forecast_end, dynamic=True)

# --- 5. Evaluate the Forecast ---
rmse = np.sqrt(mean_squared_error(test_data, forecast))
print(f"Backtesting RMSE: {rmse:.2f}")

# --- 6. Visualize the Results ---
plt.figure(figsize=(12, 6))
plt.plot(train_data, label='Training Data')
plt.plot(test_data, label='Actual Test Data', color='orange')
plt.plot(forecast, label='Forecasted Data', color='red', linestyle='--')
plt.title('SARIMA Model Backtest')
plt.legend()
plt.show()

```

This workflow provides a reliable method for estimating how your model will perform on new, unseen data. A more advanced technique, called **walk-forward validation**, involves creating multiple overlapping train-test splits to get a more robust measure of model performance, but the fundamental principle remains the same.
-----

### **Automating Model Selection**

In the previous section, we discussed how to select the `(p,d,q)` order for an ARIMA model by manually inspecting ACF and PACF plots. This is an excellent way to build intuition, but in practice, it can be subjective and time-consuming. A more efficient and reproducible approach is to automate this selection process.

The goal of automated model selection is to programmatically search through many different combinations of model parameters and find the one that optimizes a specific performance metric. The most common metric used for this is the **Akaike Information Criterion (AIC)**. The AIC evaluates how well the model fits the data while penalizing it for having too many parameters. A lower AIC score indicates a better balance of fit and simplicity, which helps prevent overfitting.

#### **Code Demo: Finding the Best ARIMA Model with `auto_arima`**

The `pmdarima` library offers a powerful function called `auto_arima` that handles this process for us. It intelligently searches for the best model order, including the degree of differencing.

```python
# First, you may need to install the library:
# pip install pmdarima

import pmdarima as pm
from pmdarima import model_selection
import polars as pl

# We will use the raw 'AirPassengers' series, as auto_arima will handle differencing
file_path = 'path/to/your/AirPassengers.csv'
df = pl.read_csv(file_path).with_columns(
    pl.col("Month").str.to_datetime(format="%Y-%m")
)
series = df['Passengers'].to_numpy() # auto_arima works well with NumPy arrays

# Fit the auto_arima model
# 'm=12' tells the model to look for yearly seasonality
auto_model = pm.auto_arima(series,
                           start_p=1, start_q=1,
                           test='adf',       # use adf test to find optimal 'd'
                           max_p=3, max_q=3, # maximum p and q
                           m=12,             # frequency of the series
                           d=None,           # let model determine 'd'
                           seasonal=True,    # Fit a seasonal model (SARIMA)
                           start_P=0,
                           D=1,              # let model determine 'D'
                           trace=True,       # print status on the fits
                           error_action='ignore',
                           suppress_warnings=True,
                           stepwise=True)    # use stepwise algorithm to find best model

# Print the model summary
print(auto_model.summary())
```

The output of `auto_arima` will show the different models it tested and their corresponding AIC scores. It will automatically return the best-performing model, which you can then use for forecasting, saving you the manual effort of trial and error.

-----

### **Using Machine Learning for Forecasting**

An alternative and increasingly popular approach is to treat forecasting as a standard **supervised machine learning** problem. This method is powerful because it allows you to use any regression model from libraries like `scikit-learn` and makes it easy to include additional predictive features.

The core idea is to transform the time series data into a feature matrix (`X`) and a target vector (`y`), which is the format `scikit-learn` expects.

#### **Time-Based Feature Engineering**

The primary way to create features is by extracting information from the time index itself. From a single date, you can engineer a rich set of features that can help a model capture trends and seasonality.

  * **Calendar Features**: Day of the week, month, quarter, week of the year.
  * **Lag Features**: Past values of the series itself. For example, to predict today's sales, you could include features for sales from the previous day (lag 1), the day before that (lag 2), and the same day last week (lag 7).

#### **Using Exogenous Variables**

This approach also makes it simple to add **exogenous variables**—external factors that might influence your target variable. For instance, if you are forecasting a store's daily sales, you could include features like:

  * Was there a promotion running on that day?
  * Was it a public holiday?
  * What was the local weather?

#### **Code Demo: Forecasting with a `RandomForestRegressor`**

Here's a simplified example of how you might set up a machine learning model for forecasting.

```python
import polars as pl
from sklearn.ensemble import RandomForestRegressor
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error

# --- 1. Feature Engineering ---
# Assume 'df' is our Polars DataFrame with 'Date' and 'Sales'
df = df.with_columns([
    pl.col("Date").dt.month().alias("month"),
    pl.col("Date").dt.day().alias("day_of_month"),
    pl.col("Date").dt.weekday().alias("day_of_week"),
    # Create a lag feature: sales from the previous period
    pl.col("Sales").shift(1).alias("sales_lag_1")
]).drop_nulls() # Drop rows with nulls created by the lag

# --- 2. Create Training and Test Sets ---
# It's crucial to split by time, not randomly!
train_df = df.filter(pl.col("Date") < '2024-01-01')
test_df = df.filter(pl.col("Date") >= '2024-01-01')

features = ["month", "day_of_month", "day_of_week", "sales_lag_1"]
target = "Sales"

X_train, y_train = train_df[features], train_df[target]
X_test, y_test = test_df[features], test_df[target]

# --- 3. Train the Model ---
ml_model = RandomForestRegressor(n_estimators=100, random_state=42)
ml_model.fit(X_train, y_train)

# --- 4. Evaluate the Model (Backtesting) ---
predictions = ml_model.predict(X_test)
rmse = mean_squared_error(y_test, predictions, squared=False)

print(f"RMSE of the ML model: {rmse:.2f}")
```

This machine learning approach offers great flexibility and is often very effective, especially for complex series or when multiple driving factors are involved.