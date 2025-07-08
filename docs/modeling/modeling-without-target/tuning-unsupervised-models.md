---
icon: material/numeric-5
---

# Tuning Unsupervised Models


#### **Analysis of the Results**

By examining the output table, you can now build a narrative for each cluster:

  * **Cluster 0**: This group has a very **small** petal length and petal width.
  * **Cluster 1**: This group has the **largest** petal length, petal width, and sepal length.
  * **Cluster 2**: This group falls in the **middle** for most measurements.

If we were to compare these profiles to our knowledge of the actual iris species, we would find that our unsupervised clustering algorithm has successfully rediscovered the `setosa`, `virginica`, and `versicolor` species based purely on the structure of the data. This demonstrates how an analyst can use clustering to identify meaningful segments in a dataset, even without having prior labels.
Yes, **`n_clusters`** and **`n_init`** are both hyperparameters for the KMeans clustering model.

The equivalent of "tuning" for an unsupervised model like KMeans involves using metrics that measure the quality of the clusters themselves, since there is no "ground truth" like accuracy to measure against. For KMeans, the most common tuning method is the **Elbow Method**, which is used to find the optimal value for the `n_clusters` hyperparameter.

-----

### The Elbow Method for Tuning `n_clusters`

The Elbow Method focuses on a metric called **inertia**, which measures the within-cluster sum-of-squares. In simpler terms, inertia tells you how internally coherent the clusters are; a lower inertia means the data points are closer to their own cluster's center.

The process is as follows:

1.  Run the K-Means algorithm for a range of different values for `k` (e.g., from 1 to 10).
2.  For each value of `k`, record the model's inertia.
3.  Plot the number of clusters (`k`) against the inertia.

The resulting plot typically shows the inertia decreasing as `k` increases. You look for the "elbow" in the curve—the point where the rate of decrease sharply slows down. This point suggests the last value of `k` that provides a significant improvement, representing the best trade-off between the number of clusters and the compactness of those clusters.

```python
# --- Hands-On Example of the Elbow Method ---
import polars as pl
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
from sklearn.datasets import make_blobs

# Using the same synthetic data from before
X, _ = make_blobs(n_samples=300, centers=4, cluster_std=0.70, random_state=42)

# Calculate inertia for a range of k values
inertia_values = []
k_range = range(1, 11)

for k in k_range:
    kmeans = KMeans(n_clusters=k, random_state=42, n_init=10)
    kmeans.fit(X)
    inertia_values.append(kmeans.inertia_)

# Plot the elbow curve
plt.figure(figsize=(8, 5))
plt.plot(k_range, inertia_values, marker='o')
plt.title('Elbow Method For Optimal k')
plt.xlabel('Number of clusters (k)')
plt.ylabel('Inertia')
plt.xticks(k_range)
plt.grid(True)
plt.show()

```

As you can see in the plot, the "elbow" is clearly visible at **k=4**, which correctly identifies the number of clusters we originally generated in our synthetic data.

-----

### The `n_init` Hyperparameter

The **`n_init`** hyperparameter addresses the fact that the K-Means algorithm's outcome can depend on the initial random placement of the centroids. It specifies the number of times the algorithm will be run with different random centroid initializations. `scikit-learn` then automatically chooses the best result (the one with the lowest inertia) from all the runs. The default value of `10` is often sufficient to ensure a stable and reliable result.
