---
icon: material/numeric-6
---
# Introduction to Ensemble Models

While tuning a single model can yield significant gains, one of the most powerful techniques in machine learning is to combine multiple models into a single, more powerful **ensemble**. The core idea is that a "team" of models can often achieve better performance, stability, and robustness than any single "star player" model.

Two of the most common and effective ensemble strategies are **Bagging** and **Boosting**.

---
## **Bagging: The Power of Diverse Opinions 🌳🌳🌳**

**Bagging**, which stands for **B**ootstrap **Agg**regating, works like asking a large group of independent experts for their opinion and averaging their responses. The goal is to reduce **variance** and prevent a single model from overfitting to quirks in the data.

The most popular bagging algorithm is the **Random Forest**. Here’s how it works:

1.  It creates hundreds of different Decision Trees.
2.  Each tree is trained on a random sample (a "bootstrap" sample) of the original training data.
3.  Each tree also only considers a random subset of features at each split point.
4.  To make a final prediction, the Random Forest averages the predictions from all the individual trees.

This process results in a model that is typically much more accurate and stable than a single, highly-tuned Decision Tree.

---
## **Boosting: The Power of Iterative Improvement 🎯**

**Boosting** builds a model sequentially, where each new model in the sequence focuses on correcting the errors made by the previous one. Think of it as building a team of specialists, where each new member is trained specifically to fix the mistakes of the team so far.

Popular boosting algorithms include **Gradient Boosting** and **XGBoost**. The general process is:

1.  A simple initial model is trained on the data.
2.  The algorithm identifies the errors (residuals) made by this first model.
3.  A second model is trained specifically to predict those errors.
4.  This process is repeated, with each subsequent model focusing on the remaining errors, until the model's predictions are highly accurate.

Boosting often leads to state-of-the-art performance but can be more sensitive to noisy data and requires more careful tuning than bagging models. For our demonstration, we will focus on the highly effective and widely used Random Forest.

???+ note

    The popular `xgboost` library provides a wrapper that fully adopts `scikit-learn`'s Estimator API. This means you can plug an `XGBRegressor` directly into your existing `Pipeline` and `GridSearchCV` framework just like any other `scikit-learn` model, making it an excellent choice for a demonstration.

-----

## Tuning an Ensemble Model

Let's see the power of ensembles in action. We will replace the single `DecisionTreeRegressor` in our workflow with a powerful boosting model, `XGBRegressor`, and run our hyperparameter search again to find the best configuration for this new model.

#### **Setup: Importing XGBoost**

First, you may need to install the `xgboost` library. You can do this in a notebook cell by running `!pip install xgboost`. Then, we import the model.

```python
from xgboost import XGBRegressor
```

#### **Step 1: Create a New Pipeline with XGBoost**

We define a new pipeline, this time incorporating the `XGBRegressor`. Notice how seamlessly the new model fits into the existing structure.

```python
# Create the new pipeline with the XGBoost Regressor
xgb_pipe = make_pipeline(
    StandardScaler(),
    XGBRegressor(random_state=42)
)
```

#### **Step 2: Define a New Parameter Grid**

Next, we create a new parameter grid with hyperparameters specific to `XGBRegressor`. Note the naming convention `xgbregressor__` to target the correct step in the pipeline.

```python
# Define the grid of hyperparameters for XGBoost
xgb_param_grid = {
    'xgbregressor__n_estimators': [100, 200],
    'xgbregressor__max_depth': [3, 5, 7],
    'xgbregressor__learning_rate': [0.1, 0.05]
}
```

#### **Step 3: Run the Grid Search**

We set up and run a new `GridSearchCV` with our XGBoost pipeline and its corresponding parameter grid.

```python
# Set up and run the new grid search
xgb_grid_search = GridSearchCV(
    estimator=xgb_pipe,
    param_grid=xgb_param_grid,
    cv=5,
    scoring='neg_mean_absolute_error',
    verbose=1
)

# Fit the grid search on the training data
xgb_grid_search.fit(X_train, y_train)
```

#### **Step 4: Evaluate the Tuned Ensemble Model**

Finally, we inspect the results and evaluate our best-performing XGBoost model on the held-out test set. You can compare this final Mean Absolute Error to the one achieved by the single Decision Tree to quantify the performance gain from using a more powerful ensemble model.

```python
# Print the best hyperparameters
print("Best XGBoost Hyperparameters:")
print(xgb_grid_search.best_params_)

# Print the best cross-validated score
best_mae_xgb = -xgb_grid_search.best_score_
print(f"\nBest Cross-Validated MAE (XGBoost): ${best_mae_xgb:,.2f}")

# Evaluate the final model on the test set
final_predictions_xgb = xgb_grid_search.predict(X_test)
final_mae_xgb = mean_absolute_error(y_test, final_predictions_xgb)

print(f"\nFinal Model MAE on Test Set (XGBoost): ${final_mae_xgb:,.2f}")
```