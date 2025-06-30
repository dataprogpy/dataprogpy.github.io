---
icon: material/numeric-4
---

-----

### **6.0 Hands-On Lab: Automated Hyperparameter Tuning**

This lab demonstrates the complete, robust workflow. We will use `GridSearchCV` to automatically search for the best hyperparameters for our pipeline, using cross-validation on our training data. We will then perform a final evaluation on the held-out test set.

#### **Setup: Imports and Data Splits**

First, we'll import `GridSearchCV` and reuse the `X_train`, `X_test`, `y_train`, and `y_test` sets from our previous lab.

```python
from sklearn.model_selection import GridSearchCV

# We assume X_train, X_test, y_train, y_test are already created
# from the previous lab's train_test_split.
```

#### **Step 1: Define the Parameter Grid**

We need to tell `GridSearchCV` which hyperparameters to test. We define this in a dictionary where the keys are the names of the parameters and the values are lists of settings to try.

To specify a hyperparameter for a step in a pipeline, we use the step's name (in lowercase), followed by two underscores (`__`), and then the hyperparameter name. The default name for the `DecisionTreeRegressor` step in our `make_pipeline` object is `decisiontreeregressor`.

```python
# Define the grid of hyperparameters to search
param_grid = {
    'decisiontreeregressor__max_depth': [3, 5, 7, 10, None],
    'decisiontreeregressor__min_samples_leaf': [1, 2, 4, 6],
    'decisiontreeregressor__max_features': [None, 'sqrt', 'log2']
}
```

#### **Step 2: Set Up and Run GridSearchCV**

Now, we instantiate `GridSearchCV`. We provide our pipeline (`pipe`), the `param_grid`, the number of cross-validation folds (`cv=5`), and the scoring metric. Since `GridSearchCV` tries to *maximize* a score, and we want to *minimize* error, we use `'neg_mean_absolute_error'`.

**Note**: This step can take a few moments to run, as it is training `60` models (`4 * 3 * 5`) five times each (`300` total fits).

```python
# Set up GridSearchCV
grid_search = GridSearchCV(
    estimator=pipe,
    param_grid=param_grid,
    cv=5,
    scoring='neg_mean_absolute_error',
    verbose=1 # This will print progress updates
)

# Fit the grid search on the training data
grid_search.fit(X_train, y_train)
```

#### **Step 3: Inspect the Results**

`GridSearchCV` stores the best combination of parameters it found in the `best_params_` attribute. The `best_estimator_` attribute holds the pipeline that was refit on the entire training set using these optimal parameters.

```python
# Print the best hyperparameters found
print("Best Hyperparameters:")
print(grid_search.best_params_)

# The best score is negative, so we multiply by -1 to get the MAE
best_mae = -grid_search.best_score_
print(f"\nBest Cross-Validated MAE: ${best_mae:,.2f}")
```

#### **Step 4: Final Evaluation on the Test Set**

Finally, we use the `best_estimator_` found by `GridSearchCV` to make predictions on our held-out test set. This provides our final, unbiased assessment of the tuned model's performance.

```python
# Use the best estimator to make predictions on the test set
final_predictions = grid_search.predict(X_test)

# Calculate the final MAE on the test set
final_mae = mean_absolute_error(y_test, final_predictions)

print(f"\nFinal Model MAE on Held-Out Test Set: ${final_mae:,.2f}")
```

You can now compare this `final_mae` to the baseline MAE from our first lab to quantify the improvement gained from hyperparameter tuning.
