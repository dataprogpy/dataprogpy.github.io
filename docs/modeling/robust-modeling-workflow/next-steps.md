---
icon: material/numeric-6
---


***
### **9.0 Homework and Active Learning**

To solidify your understanding of building and tuning robust modeling pipelines, we recommend you try the following tasks. These exercises are designed to give you hands-on practice with the iterative and exploratory nature of data science.

1.  **Incorporate Categorical Features**: Our model only used numerical features.
    * **Task**: Add the `zipcode` feature to your feature set `X`. Since `zipcode` is a categorical variable, you will need to add a preprocessing step for it in your pipeline. Use the `OneHotEncoder` from `scikit-learn` to handle this. You will need to use `ColumnTransformer` to apply different preprocessing steps (scaling for numerical, one-hot encoding for categorical) to different columns.
    * **Analysis**: Does adding this location data improve your model's final performance?

2.  **Experiment with a Different Model**: We demonstrated how to swap a `DecisionTreeRegressor` for an `XGBRegressor`.
    * **Task**: Build and tune a new pipeline using a `KNeighborsRegressor`. Research its key hyperparameters (like `n_neighbors`) and create a new parameter grid for `GridSearchCV`.
    * **Analysis**: How does the performance and training time of the K-Nearest Neighbors model compare to the Decision Tree and XGBoost models?

3.  **Optimize for a Different Metric**: We used Mean Absolute Error as our scoring metric.
    * **Task**: Re-run one of your `GridSearchCV` experiments, but this time set `scoring='neg_root_mean_squared_error'`.
    * **Analysis**: Does optimizing for RMSE result in a different set of best hyperparameters? Why might a business choose to optimize for RMSE over MAE? (Hint: How does RMSE penalize large errors?)

***
### **10.0 Lesson Summary**

In this lesson, you have significantly upgraded your modeling capabilities, moving from a manual, analytical workflow to a robust, automated process ready for real-world application.

You learned that a **`Pipeline`** is the professional standard for encapsulating preprocessing and modeling steps, ensuring consistency and preventing critical errors like data leakage. We replaced the unreliable, single train-test split with **k-fold cross-validation** to build confidence in our model's performance. You saw how to systematically optimize **hyperparameters** using **`GridSearchCV`**, and witnessed the power of **ensemble models** like XGBoost to improve predictive accuracy.

Most importantly, you now have a reusable framework for building, evaluating, and tuning models in a systematic and reproducible way—a core competency for any data science practitioner.