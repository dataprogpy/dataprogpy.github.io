Of course. Here is the content for the concluding case study.

-----

### **Case Study: Forecasting Retail Sales**

This capstone case study will walk you through a complete, end-to-end forecasting project. We will apply all the concepts you've learned—from initial exploration to building and comparing sophisticated models—to forecast monthly retail sales.

#### **Project Goal**

The objective is to build and evaluate two different types of forecasting models—a statistical SARIMA model and a machine learning model—to predict the next 12 months of sales for a retail company. We will then compare their performance to determine which model is more suitable for this task.

#### **The Data**

We will use a dataset representing monthly sales for a U.S. retail store. The data spans several years and will allow us to analyze both trend and seasonal patterns.

-----

### **1. Data Exploration (EDA)**

First, we load the data and perform an initial visual analysis to understand its structure.

```python
import polars as pl
import matplotlib.pyplot as plt
import statsmodels.api as sm

# Load the dataset
# This assumes you have a CSV with 'DATE' and 'SALES' columns
file_path = 'path/to/your/retail_sales.csv'
df = pl.read_csv(file_path).with_columns(
    pl.col("DATE").str.to_datetime(format="%Y-%m-%d")
)

# Plot the raw data
df.plot(x='DATE', y='SALES', title='Monthly Retail Sales')
plt.show()

# Decompose the series to see the components
series_pd = df.to_pandas().set_index('DATE')['SALES']
decomposition = sm.tsa.seasonal_decompose(series_pd, model='multiplicative', period=12)
fig = decomposition.plot()
fig.set_size_inches(12, 8)
plt.show()
```

**Observations:**

  * The raw data shows a clear **upward trend**, indicating sales are growing over time.
  * There is a strong, repeating **seasonal pattern**, with peaks occurring at the end of each year, likely due to holiday sales.
  * The multiplicative decomposition confirms these findings and shows that the magnitude of the seasonal swing grows with the trend.

-----

### **2. Data Preparation and Backtesting Setup**

Before modeling, we need to set up our backtesting framework. We will reserve the last 12 months of data as our hold-out test set to evaluate the final models.

```python
# Split the data into training and test sets
train_df = df.filter(pl.col("DATE") < '2022-01-01')
test_df = df.filter(pl.col("DATE") >= '2022-01-01')

print(f"Training data runs from {train_df['DATE'].min()} to {train_df['DATE'].max()}")
print(f"Test data runs from {test_df['DATE'].min()} to {test_df['DATE'].max()}")
```

-----

### **3. Modeling - Part 1: The SARIMA Approach**

For our first model, we'll use a statistical approach. We will use `auto_arima` to find the best SARIMA model for our training data.

```python
import pmdarima as pm

# Use the training data to find the best model
train_series = train_df['SALES'].to_numpy()

sarima_model = pm.auto_arima(train_series,
                             seasonal=True, m=12,
                             trace=True,
                             error_action='ignore',
                             suppress_warnings=True,
                             stepwise=True)

print(sarima_model.summary())

# Generate forecasts for the test period
sarima_forecast = sarima_model.predict(n_periods=len(test_df))
```

`auto_arima` will perform a search and land on the optimal `(p,d,q)(P,D,Q)m` parameters, giving us a robust statistical model fitted to our training data.

-----

### **4. Modeling - Part 2: The Machine Learning Approach**

Next, we'll build a machine learning model. This requires us to first engineer relevant features from our time series data.

```python
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_squared_error
import numpy as np

# Feature Engineering Function
def create_features(data):
    return data.with_columns([
        pl.col("DATE").dt.month().alias("month"),
        pl.col("DATE").dt.year().alias("year"),
        # Create lag features for the last 12 months
        *[pl.col("SALES").shift(i).alias(f"lag_{i}") for i in range(1, 13)]
    ])

# Create features for the full dataset and then split
df_featured = create_features(df)
train_featured = df_featured.filter(pl.col("DATE") < '2022-01-01').drop_nulls()
test_featured = df_featured.filter(pl.col("DATE") >= '2022-01-01')

# Define features and target
features = [col for col in train_featured.columns if col not in ['DATE', 'SALES']]
target = "SALES"

X_train, y_train = train_featured[features], train_featured[target]
X_test, y_test = test_featured[features], test_featured[target]

# Train the RandomForest model
ml_model = RandomForestRegressor(n_estimators=100, random_state=42)
ml_model.fit(X_train, y_train)

# Generate predictions
ml_forecast = ml_model.predict(X_test)
```

-----

### **5. Evaluation and Conclusion**

Now we compare the performance of both models on the test set using the Root Mean Squared Error (RMSE).

```python
# Calculate RMSE for both models
sarima_rmse = np.sqrt(mean_squared_error(test_df['SALES'], sarima_forecast))
ml_rmse = np.sqrt(mean_squared_error(test_df['SALES'], ml_forecast))

print(f"SARIMA Model RMSE: {sarima_rmse:.2f}")
print(f"Machine Learning Model RMSE: {ml_rmse:.2f}")

# Visualize the comparison
plt.figure(figsize=(14, 7))
plt.plot(train_df['DATE'], train_df['SALES'], label='Training Data')
plt.plot(test_df['DATE'], test_df['SALES'], label='Actual Sales', color='blue')
plt.plot(test_df['DATE'], sarima_forecast, label=f'SARIMA Forecast', color='red', linestyle='--')
plt.plot(test_df['DATE'], ml_forecast, label=f'ML Forecast', color='green', linestyle=':')
plt.title('Model Comparison: SARIMA vs. Machine Learning')
plt.legend()
plt.show()

```

#### **Conclusion**

In this case study, we followed a structured workflow to tackle a real-world forecasting problem. We saw that both the statistical SARIMA model and the feature-based machine learning model were able to capture the trend and seasonality of the data.

By comparing their RMSE on a held-out test set, we can make an informed decision about which model performs better for this specific task. Often, machine learning models can have an edge when complex, non-linear patterns exist or when many external variables are available. However, SARIMA models provide excellent performance for series with clear, regular patterns and are often easier to interpret. This analysis provides a solid, data-driven foundation for a business to plan for future sales.