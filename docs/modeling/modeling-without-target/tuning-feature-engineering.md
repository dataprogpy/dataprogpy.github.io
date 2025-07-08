---
icon: material/numeric-6
---

# Fine-tuning Feature Engineering


## **The Goal: Finding the Best Feature Engineering Strategy Automatically**

So far, we have explored feature selection and feature extraction (PCA) as separate techniques. But in a real-world project, a critical question arises: **"For my specific problem, which feature engineering strategy is best?"**

Is it better to *select* the best original features, or is it better to *extract* entirely new features? And what is the optimal number of features to use?

The code below answers this question systematically. It builds a single, powerful `GridSearchCV` experiment that creates a "competition" between three different feature engineering methods (`PCA`, `NMF`, and `SelectKBest`) to find the single best combination of data reduction technique and model hyperparameters. This complexity is the key to automating what would otherwise be a very tedious, manual process of building and comparing separate models.

???+ note "Non-Negative Matrix Factorization (NMF): An Alternative to PCA"

    While PCA is a powerful and general-purpose tool for dimensionality reduction, it is not the only technique available. In our upcoming example, you will see another method called **Non-Negative Matrix Factorization (NMF)**.

    NMF is a dimensionality reduction algorithm that, like PCA, aims to find a new, more compact representation of the data. However, it operates under a significant constraint: it does not allow for any negative values in the resulting components. This makes it particularly useful for datasets where the features can only be added together meaningfully, such as pixel intensities in an image or word counts in a text document. Think of NMF as trying to explain the data as a sum of its parts.

    ### PCA vs. NMF: A Brief Comparison

    | **Feature** | **Principal Component Analysis (PCA)** | **Non-Negative Matrix Factorization (NMF)** |
    | :--- | :--- | :--- |
    | **Core Goal** | Finds directions of maximum **variance** in the data. | Decomposes the data into a sum of non-negative **parts**. |
    | **Component Values** | Components can have both positive and negative values, which can sometimes be hard to interpret. | Components are strictly **non-negative** (zero or positive). |
    | **Best For** | General-purpose dimensionality reduction, especially for data centered around a mean. | Datasets where features are counts or represent parts of a whole (e.g., text analysis, image processing, audio signal processing). |
    | **Interpretation** | The first component is the most important, followed by the second, and so on. | All components can be equally important, representing different additive parts of the original data. |

-----

### **Code Walkthrough: A Competition Pipeline**

Let's dissect the code piece by piece.

#### **1. The Pipeline Blueprint**

First, we define a `Pipeline` that acts as a template for our experiment. Notice the crucial second step.

```python
pipe = Pipeline(
    [
        ("scaling", MinMaxScaler()),
        # the reduce_dim stage is populated by the param_grid
        ("reduce_dim", "passthrough"),
        ("classify", LinearSVC(dual=False, max_iter=10000)),
    ]
)
```

  * **`("scaling", MinMaxScaler())`**: Our standard first step to scale the data.
  * **`("reduce_dim", "passthrough")`**: This is the key. We are creating a placeholder step named `reduce_dim`. The `"passthrough"` value tells the pipeline to do nothing at this stage *by default*. This placeholder will be dynamically replaced by our feature engineering objects (`PCA`, `NMF`, `SelectKBest`) during the grid search.
  * **`("classify", LinearSVC(...))`**: Our final classification model.

#### **2. The Parameter Grid: Defining the Competition**

This is the most complex part. Instead of a single dictionary, `param_grid` is a **list of dictionaries**. `GridSearchCV` will run a completely separate search for each dictionary in the list.

**Competition Bracket 1: Feature Extraction (`PCA` vs. `NMF`)**
The first dictionary defines the competition between our two feature extraction methods.

```python
param_grid = [
    {
        "reduce_dim": [PCA(iterated_power=7), NMF(max_iter=1_000)],
        "reduce_dim__n_components": N_FEATURES_OPTIONS,
        "classify__C": C_OPTIONS,
    },
# ...
]
```

  * **`"reduce_dim": [PCA(...), NMF(...)]`**: This tells `GridSearchCV`: "For the `reduce_dim` step in the pipeline, first try using `PCA`, and then try using `NMF`."
  * **`"reduce_dim__n_components": [2, 4, 8]`**: This uses the double-underscore (`__`) syntax to set a hyperparameter on the object *inside* the `reduce_dim` step. It says: "For whichever method you are trying (`PCA` or `NMF`), test it with 2, 4, and 8 components."
  * **`"classify__C": [1, 10, 100, 1000]`**: This sets the `C` hyperparameter for our final `LinearSVC` classifier.

**Competition Bracket 2: Feature Selection (`SelectKBest`)**
The second dictionary defines the experiment for our feature selection method.

```python
# ...
    {
        "reduce_dim": [SelectKBest(mutual_info_classif)],
        "reduce_dim__k": N_FEATURES_OPTIONS,
        "classify__C": C_OPTIONS,
    },
]
```

  * **`"reduce_dim": [SelectKBest(...)]`**: This tells `GridSearchCV`: "Now, for the `reduce_dim` step, try using `SelectKBest`."
  * **`"reduce_dim__k": [2, 4, 8]`**: This targets the hyperparameter for `SelectKBest`, telling it to try selecting the top 2, 4, and 8 features.
  * **`"classify__C": [1, 10, 100, 1000]`**: Again, this tunes the final classifier.

#### **3. Running the Search**

Finally, we put it all together. `GridSearchCV` will now exhaustively test every possible combination defined in the `param_grid`. It will try PCA with 2, 4, and 8 components; NMF with 2, 4, and 8 components; and SelectKBest with 2, 4, and 8 features, each paired with all four `C` values for the classifier.

```python
grid = GridSearchCV(pipe, n_jobs=1, param_grid=param_grid)
grid.fit(X, y)
```

After the search is complete, you can inspect `grid.best_estimator_` and `grid.best_params_` to see which combination—which feature engineering strategy, which number of features, and which classifier setting—emerged as the ultimate winner of this automated competition. This is the power and purpose of this complex-seeming setup: it provides a robust, automated framework for making optimal feature engineering decisions.
