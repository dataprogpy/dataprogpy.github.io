
### **Lesson 2: Supervised Learning in Practice: Regression & Pipelines**

**Objectives:**
* Differentiate between regression and classification tasks.
* Build an end-to-end data processing and modeling pipeline using `scikit-learn`'s `Pipeline` object.
* Define and distinguish between model parameters and hyperparameters.
* Evaluate a regression model using metrics like Mean Absolute Error (MAE).
* Use cross-validation and `GridSearchCV` to tune model hyperparameters systematically.

**Key Concepts & Terminology:**
* Regression vs. Classification
* Parameters vs. **Hyperparameters**
* Feature Engineering (scaling, encoding)
* **`scikit-learn` Pipeline**
* **Cross-Validation (k-fold)**
* Grid Search (`GridSearchCV`)
* Regression Metrics: **Mean Absolute Error (MAE)**, Root Mean Squared Error (RMSE)

**Proposed Outline:**

1.  **Recap and Introduction to Regression**
    * Quickly review the workflow from Lesson 1.
    * Introduce Regression: Predicting a continuous numerical value (e.g., price, temperature) instead of a category.
    * **Dataset:** Introduce the King County House Prices dataset. It's realistic, has a mix of numeric and categorical features, and requires preprocessing.

2.  **The Need for Preprocessing and Pipelines**
    * Show the raw data. Point out issues: features on different scales (e.g., `sqft_living` vs. `bedrooms`), categorical features that need to be converted to numbers.
    * Explain that applying these steps manually is tedious and error-prone.
    * **Introduce the `scikit-learn` `Pipeline`**. Frame it as a way to chain together all our preprocessing and modeling steps into a single object. This is a huge practical skill.

3.  **Hands-On Lab 1: Building a Regression Pipeline**
    * **Workflow:**
        1.  Load the King County dataset with Polars.
        2.  Select a smaller subset of numerical features and the `price` target for the first pass.
        3.  Create a simple pipeline: `pipe = make_pipeline(StandardScaler(), DecisionTreeRegressor())`. `StandardScaler` standardizes features by removing the mean and scaling to unit variance.
        4.  Split the data (`train_test_split`).
        5.  Fit the entire pipeline: `pipe.fit(X_train, y_train)`.
        6.  Make predictions and evaluate using `mean_absolute_error`. Interpret the result in plain English: "Our model's predictions are, on average, off by $X."

4.  **Improving Performance: Hyperparameter Tuning**
    * Distinguish: **Parameters** are learned from data during `.fit()` (e.g., the splits in a decision tree). **Hyperparameters** are settings you choose *before* training (e.g., `max_depth`).
    * Pose the question: "How do we find the best `max_depth` for our regressor?"

5.  **Robust Evaluation: Cross-Validation**
    * Explain the limitation of a single train-test split (it can be "lucky" or "unlucky").
    * Introduce **k-fold cross-validation** with a simple diagram. It provides a more robust estimate of the model's performance. Show how to use `cross_val_score`.

6.  **Hands-On Lab 2: Automated Hyperparameter Tuning**
    * Introduce `GridSearchCV`. Explain that it combines cross-validation with a search over a "grid" of specified hyperparameters.
    * **Workflow:**
        1.  Define a parameter grid for the `DecisionTreeRegressor` within our pipeline (e.g., `{'decisiontreeregressor__max_depth': [3, 5, 7, 10]}`). Note the `__` syntax for pipeline steps.
        2.  Set up `GridSearchCV` with the pipeline, parameter grid, and scoring metric (`neg_mean_absolute_error`).
        3.  Fit the `GridSearchCV` object.
        4.  Inspect the `best_params_` and `best_score_`.

7.  **Wrap-up & Recap:**
    * Review the enhanced workflow: It's still the same core idea, but now with robust pipelines and tuning.
    * Emphasize that modeling is an iterative process of proposing a model, evaluating it, and refining it.
