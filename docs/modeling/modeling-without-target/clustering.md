---
icon: material/numeric-2
---

#  An Introduction to Clustering

Clustering is a fundamental unsupervised learning technique used to discover natural groupings within data. The objective is to partition data points into distinct subgroups, or **clusters**, such that points within the same cluster are highly similar, while points in different clusters are dissimilar.

A classic business application is market analysis. Analysts traditionally group stocks using pre-defined categories like GICS sectors ("Technology," "Healthcare," "Financials"). However, these labels may not accurately reflect how stocks actually behave based on their price movements.

A more sophisticated approach, demonstrated in a well-known `scikit-learn` example, is to use a clustering algorithm like **Affinity Propagation** to analyze the correlation of stock price fluctuations. This method learns the structure directly from the data, revealing data-driven clusters of companies whose stocks tend to move together. These discovered groupings can be more meaningful for portfolio diversification and risk management than traditional industry classifications.

## A Case Study Dataset: Handwritten Digits

Throughout this lesson, we will use the **UCI ML handwritten digits dataset** as a running example. This dataset, available in `scikit-learn` via `load_digits()`, contains thousands of 8x8 pixel images of handwritten digits (0-9). Each image is represented as a row of 64 features (one for each pixel's intensity).

This dataset is an excellent learning tool because it's complex enough to be interesting but small enough for rapid experimentation. Crucially, while we will treat it as unlabeled data for our clustering tasks, it comes with **ground truth labels** (we know which image corresponds to which digit). This allows us to objectively evaluate how well our unsupervised algorithms perform at rediscovering the inherent structure of the data.

## K-Means Clustering: A Foundational Algorithm

One of the most popular and intuitive clustering algorithms is **K-Means**. Its goal is to partition the data into a pre-specified number of clusters ('K') by finding cluster centers, or **centroids**, that minimize the distance from each data point to its assigned centroid.

### Key Hyperparameters

When instantiating a `KMeans` model, you will encounter several key hyperparameters:

* **`n_clusters`**: This is the most important hyperparameter. It defines the number of clusters the algorithm must find. The choice of `n_clusters` is a critical part of the modeling process.
* **`init`**: This parameter specifies the method for initializing the centroids. The default, **`'k-means++'`**, is a "smart" initialization method that places initial centroids far apart, leading to more reliable and consistent results than the `'random'` option.
* **`n_init`**: Because the algorithm's outcome can depend on the starting position of the centroids, `n_init` controls how many times the algorithm will be run with different random initializations. The default value of `10` is standard practice, and `scikit-learn` will automatically return the best result.

### Assumptions and Limitations

K-Means is powerful but relies on several key assumptions:

* **Pre-specification of 'k'**: You must decide the number of clusters beforehand.
* **Isotropic Distribution**: K-Means works best when clusters are **isotropic** (spherical) and have similar variance. It assumes all directions are equally important.
* **Cluster Size**: The algorithm implicitly assumes that clusters are of a similar size and density. It can struggle with identifying elongated clusters or groups of varying sizes.

### Evaluating Clustering Performance

Since we have no "ground truth" target, we cannot use metrics like accuracy. Instead, we use metrics that evaluate the quality of the clusters themselves. These fall into two categories.

#### Metrics Requiring Ground Truth Labels

When true labels are available (as in our `digits` dataset), we can use them to score our model's performance.

* **Homogeneity**: Measures if each cluster contains only members of a single class.
* **Completeness**: Measures if all members of a given class are assigned to the same cluster.
* **V-Measure**: The harmonic mean of homogeneity and completeness.
* **Adjusted Rand Index (ARI) & Adjusted Mutual Information (AMI)**: These measure the similarity between the true and predicted labels, correcting for chance. Scores close to 1.0 are good.

#### Metrics Not Requiring Ground Truth Labels

In most real-world scenarios, you won't have true labels. The **Silhouette Coefficient** is a powerful metric for these situations.

* **Silhouette Coefficient**: This metric measures how similar a data point is to its own cluster compared to how similar it is to other, nearby clusters. The score ranges from -1 to 1, where higher values indicate that the object is well-matched to its own cluster and poorly matched to neighboring clusters. It can be used to evaluate the quality of the clustering itself and to help determine the optimal number of clusters.


### **Understanding the Digits Dataset**

Before we model, it's crucial to understand our data. The `load_digits` dataset contains images of handwritten digits (0 through 9).

  * **What is a row?** Each **row** in the `data` array represents a single, complete image of a handwritten digit.

  * **What is a column?** Each image is a tiny 8x8 grid of pixels. To turn this grid into a data row, it's "unrolled" into a line of 64 pixel values. So, each of the 64 **columns** represents the brightness of a single pixel in the 8x8 image.

  * **What does clustering achieve here?** Our goal is to see if a machine learning model, without any prior knowledge of the actual digit labels, can **group the images based on their visual similarity**. Essentially, we're asking the model to discover the concept of "a zero," "a one," and so on, just by looking at the pixel patterns.

The first part of the code loads this data and confirms its structure: there are 10 unique digits, 1797 sample images, and 64 features (pixels) for each image. The `plt.matshow` command then gives us a visual confirmation by reshaping one of the rows back into its original 8x8 image format.

```python
## 1. Load and Inspect the Data
data, labels = load_digits(return_X_y=True)
(n_samples, n_features), n_digits = data.shape, np.unique(labels).size
print(f"# digits: {n_digits}; # samples: {n_samples}; # features {n_features}")

## 2. Visualize a Single Sample
digits = load_digits()
plt.matshow(digits.images[0], cmap="gray")
plt.show()
```

-----

### **Fitting and Evaluating the Clustering Model**

The next block of code executes our workflow: building a pipeline, fitting the model, and evaluating the results.

#### **Fitting the Model**

We use `make_pipeline` to chain our steps. This ensures a robust and reproducible process.

1.  **`StandardScaler()`**: This is a preprocessing step. It standardizes the pixel values in each feature (column) to have a mean of 0 and a standard deviation of 1. This helps the K-Means algorithm treat all pixels equally, regardless of their raw brightness values.
2.  **`KMeans(...)`**: This is our clustering algorithm. We configure it with key hyperparameters:
      * `init="k-means++"`: Uses a smart method to initialize the cluster centers, leading to more reliable results.
      * `n_clusters=n_digits`: We tell the model to find 10 clusters, since we know there are 10 unique digits.
      * `n_init=4`: The algorithm will run 4 times with different random starting points and will automatically keep the best result.
      * `random_state=42`: This ensures our results are reproducible.

The `.fit(data)` command executes the entire pipeline on our image data.

```python
## 3. Fit the Model
estimator = make_pipeline(
    StandardScaler(),
    KMeans(
        init="k-means++",
        n_clusters=n_digits,
        n_init=4,
        random_state=42),
).fit(data)

```

-----

### **Interpreting the Evaluation Metrics**

Because we have the true labels for this dataset, we can use a rich set of metrics to evaluate how well our unsupervised algorithm rediscovered the underlying digit categories.

  * **Homogeneity**: Measures if each cluster contains only images of a single digit. A score of 1.0 would mean every cluster is perfectly "pure."
  * **Completeness**: Measures if all images of a given digit are assigned to the same cluster. A score of 1.0 means no digit's images are split across multiple clusters.
  * **V-Measure**: A balanced average of Homogeneity and Completeness.
  * **Adjusted Rand Index (ARI)** and **Adjusted Mutual Info (AMI)**: These measure the similarity between the true labels and the cluster assignments, with scores close to 1.0 indicating a strong agreement. They are "adjusted" to ensure that random assignments get a score near 0.
  * **Silhouette Coefficient**: This is the only metric here that **does not use the true labels**. It measures how dense and well-separated the clusters are based only on the data's geometry. A score close to 1 indicates that the clusters are well-defined.

The final block of code calculates these scores and presents them in a clean table, giving us a comprehensive view of our model's performance.

```python
## 4. Score the Model
metrics={
        "Homogeneity": homogeneity_score(labels, estimator[-1].labels_),
        "Completeness": completeness_score(labels, estimator[-1].labels_),
        "V Measure": v_measure_score(labels, estimator[-1].labels_),
        "Adj. Rand": adjusted_rand_score(labels, estimator[-1].labels_),
        "Adj. Mutual Info": adjusted_mutual_info_score(labels, estimator[-1].labels_),
        "Silhouette": silhouette_score(
            data,
            estimator[-1].labels_,
            metric="euclidean",
            sample_size=300,
        )
    }

pd.DataFrame(metrics.values(), columns=["Score"], index=metrics.keys())

```

### Exploring Other Clustering Algorithms

KMeans is a powerful and popular algorithm, but it's just one of many tools available for clustering. As you continue your learning, we encourage you to explore other algorithms, each with different strengths and assumptions.

Some notable algorithms available in `scikit-learn` include:

* **Agglomerative Clustering**: A "bottom-up" hierarchical approach that starts with each data point as its own cluster and progressively merges the closest pairs of clusters.
* **DBSCAN**: A density-based algorithm that is excellent at finding arbitrarily shaped clusters and identifying outliers. Unlike KMeans, it doesn't require you to pre-specify the number of clusters.
* **BIRCH**: An efficient algorithm designed specifically for very large datasets.

#### A Checklist for Trying New Algorithms

When you experiment with a new algorithm, always follow a systematic process:

1.  **Understand the Hyperparameters**: Read the `scikit-learn` documentation to understand the algorithm's key hyperparameters. For `DBSCAN`, this would be `eps` and `min_samples`, not `n_clusters`.
2.  **Apply Appropriate Preprocessing**: Does the new algorithm have specific data requirements? For example, distance-based algorithms like DBSCAN are sensitive to feature scales, so using `StandardScaler` is crucial.
3.  **Use Relevant Evaluation Metrics**: Apply a range of metrics to understand performance. Use the **Silhouette Coefficient** to assess the quality of the clusters and, if you have ground truth labels, use metrics like the **Adjusted Rand Index (ARI)** to measure accuracy.