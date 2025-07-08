---
icon: material/numeric-4
---

# Feature Engineering: Creating Better Inputs for Better Models

## Introduction to Feature Engineering

So far, we have largely used the features provided in our datasets as-is. However, one of the most impactful activities in the entire machine learning workflow is **feature engineering**—the process of using domain knowledge and statistical techniques to create new features from existing ones. The quality of your features directly determines the maximum performance your model can achieve. Better features lead to better models.

There are three primary categories of feature engineering:

1.  **Feature Transformation**: This involves modifying existing features to make them more suitable for a model. This includes scaling numerical features or encoding categorical ones. You have already been doing this with tools like `StandardScaler` and `OneHotEncoder` inside your `Pipeline`. Other examples include creating polynomial features or applying mathematical transformations like logarithms.

2.  **Feature Selection**: This is the process of automatically selecting a subset of the most relevant features from your original dataset and discarding the rest. The goal is to reduce noise, improve model efficiency, and often, enhance predictive performance by focusing only on what's important.

3.  **Feature Extraction**: This involves creating entirely new features by combining or transforming existing ones. The goal is to capture the most important information in a more compact and powerful representation. You have already seen this with PCA, where we extracted new "principal component" features from the original pixel data.

A key takeaway for any data scientist is that **supervised and unsupervised techniques are often used together to achieve analytical goals**. We use unsupervised methods like PCA for feature extraction and supervised methods like feature selection to prepare the best possible inputs for our final predictive models.


## **Feature Selection in Practice**

Let's demonstrate how feature selection works. We will take the clean Iris dataset, which has 4 highly informative features, and intentionally add 20 "noisy," irrelevant features. We will then use a feature selection technique to see if it can correctly identify and select the original 4 features, and we'll measure whether this selection improves our model's performance.

### **Code Walkthrough: Selecting the Best Features**

**1. Setup: Create a Noisy Dataset**
First, we load the Iris data and then create 20 columns of random noise. We use `np.hstack` to stack our 4 original features and the 20 new noisy features side-by-side, creating a dataset with 24 features in total.

```python
# The iris dataset
X, y = load_iris(return_X_y=True)
print(f"X shape before introducing noisy features: {X.shape}")

# Some noisy data not correlated
E = np.random.RandomState(42).uniform(0, 0.1, size=(X.shape[0], 20))

# Add the noisy data to the informative features
X = np.hstack((X, E))
print(f"X shape after introducing noisy features: {X.shape}")

# Split dataset to select features and evaluate the classifier
X_train, X_test, y_train, y_test = train_test_split(
    X, y, stratify=y, random_state=0
)
```

**2. Scoring Features with `SelectKBest`**
We will use `SelectKBest`, a `scikit-learn` utility that scores features using a statistical test (in this case, `f_classif`, which is an ANOVA F-test) and selects the 'k' features with the highest scores. We'll set `k=4`, hypothesizing that it can find our four original features.

```python
selector = SelectKBest(f_classif, k=4)
selector.fit(X_train, y_train)
scores = -np.log10(selector.pvalues_)
scores /= scores.max()

# Plot the scores
X_indices = np.arange(X.shape[-1])
plt.figure(1)
plt.clf()
plt.bar(X_indices - 0.05, scores, width=0.2)
plt.title("Feature univariate score")
plt.xlabel("Feature number")
plt.ylabel(r"Univariate score ($-Log(p_{value})$)")
plt.show()
```

The bar chart clearly shows that the first four features have significantly higher scores than the 20 noisy features we added, confirming that our selection method is working as expected.

**3. Comparing Model Performance**
Now for the critical test: does removing the noisy features actually improve our model's performance? We will train two models: one on the full 24-feature dataset and another on a pipeline that includes our `SelectKBest` step.

```python
# Model 1: Using all 24 features
clf = make_pipeline(MinMaxScaler(), LinearSVC())
clf.fit(X_train, y_train)
print(
    "Classification accuracy without selecting features: {:.3f}".format(
        clf.score(X_test, y_test)
    )
)

# Model 2: Pipeline with feature selection
clf_selected = make_pipeline(SelectKBest(f_classif, k=4), MinMaxScaler(), LinearSVC())
clf_selected.fit(X_train, y_train)
print(
    "Classification accuracy after univariate feature selection: {:.3f}".format(
        clf_selected.score(X_test, y_test)
    )
)
```

The output shows a clear improvement in classification accuracy after performing feature selection. By removing the irrelevant noise, we enabled the `LinearSVC` model to learn more effectively from the signals that truly matter.

The final block of code visualizes a comparison of the feature scores, confirming that the selection process correctly identified the most important features for the model.

```python
# ... (visualization code from the prompt) ...
plt.title("Comparing feature selection")
plt.xlabel("Feature number")
plt.yticks(())
plt.axis("tight")
plt.legend(loc="upper right")
plt.show()
```

This example demonstrates that adding more features is not always better. Intelligent feature selection is a crucial step for building efficient and high-performing models.


## **Feature Extraction with PCA**

We've seen how feature selection *chooses* the best existing features. **Feature extraction**, by contrast, creates entirely new features from the old ones. The goal is to capture the most important information from the original feature set in a smaller, more powerful set of new features.

Principal Component Analysis (PCA) is a primary tool for this. By using the principal components as our new features, we can often reduce the complexity of our model, reduce noise, and even improve predictive performance.

### **Code Walkthrough: Finding the Optimal Number of Components**

This example demonstrates a complete workflow where we use `GridSearchCV` to find the optimal number of principal components to keep for a classification task on the handwritten digits dataset.

**1. Setting Up the Pipeline**
First, we define a three-step `Pipeline`. This will be the blueprint for our experiment.

```python
# Define a pipeline to search for the best combination of PCA truncation
# and classifier regularization.
pca = PCA()
# Define a Standard Scaler to normalize inputs
scaler = StandardScaler()
# set the tolerance to a large value to make the example faster
logistic = LogisticRegression(max_iter=10000, tol=0.1)

pipe = Pipeline(steps=[("scaler", scaler), ("pca", pca), ("logistic", logistic)])
X_digits, y_digits = datasets.load_digits(return_X_y=True)
```

  * **`scaler`**: Our standard preprocessing step to scale the pixel data.
  * **`pca`**: The feature extraction step. We create a `PCA` object without specifying the number of components yet; this will be tuned by our grid search.
  * **`logistic`**: Our final supervised model, a `LogisticRegression` classifier.

**2. Defining the Search Space**
Next, we create a `param_grid` to tell `GridSearchCV` what to tune. This is where the synergy between the components happens.

```python
# Parameters of pipelines can be set using '__' separated parameter names:
param_grid = {
    "pca__n_components": [5, 15, 30, 45, 60],
    "logistic__C": np.logspace(-4, 4, 4),
}
```

  * **`"pca__n_components"`**: This is the key. We are telling the grid search to treat the number of principal components as a hyperparameter. It will build models using the top 5, 15, 30, 45, and 60 components.
  * **`"logistic__C"`**: We are also simultaneously tuning the regularization strength `C` of our final classifier.

**3. Running the Search and Analyzing Results**
Finally, we create a `GridSearchCV` object with our pipeline and parameter grid and fit it to the data.

```python
search = GridSearchCV(pipe, param_grid, n_jobs=2)
search.fit(X_digits, y_digits)
print("Best parameter (CV score=%0.3f):" % search.best_score_)
print(search.best_params_)
```

The grid search will now automatically test every combination. For example, it will train a model using 5 principal components and a `C` value of 0.0001, then a model with 5 components and a `C` of 0.01, and so on, for all possible pairs.

The final output from `search.best_params_` will tell us the winning combination—the optimal number of extracted features and the best classifier setting for this specific problem, demonstrating a powerful and automated approach to feature engineering.

## **Exploring Other Feature Extraction Methods**

While PCA is a powerful and common technique, `scikit-learn` offers other methods for feature extraction that may be better suited for different types of problems.

As you continue to learn, I encourage you to explore **`FeatureAgglomeration`**. This technique works by applying hierarchical clustering to the features themselves, progressively grouping together features that are most similar.

* **Typical Use Case**: `FeatureAgglomeration` is particularly useful when you have many features that are redundant or measure similar underlying concepts. By grouping them, you can create a simplified set of "meta-features" that are easier for a model to interpret, which can be especially effective in domains like bioinformatics or text analysis where you might have thousands of highly correlated features.