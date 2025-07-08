---
icon: material/numeric-2
---

# Clustering


### **2.0 Clustering with K-Means**

Clustering is the task of identifying subgroups in the data, such that data points in the same subgroup (or **cluster**) are very similar, while data points in different clusters are very different. One of the most widely used and intuitive clustering algorithms is **K-Means**.

#### **The K-Means Algorithm: An Intuitive View**

The goal of K-Means is to partition data into a pre-specified number of clusters, 'K'. It accomplishes this through a simple, iterative process:

1.  **Initialization**: First, the algorithm randomly places 'K' initial points into the feature space. These initial points are called **centroids**.
2.  **Assignment Step**: It then assigns each data point in your dataset to its nearest centroid. This creates 'K' initial clusters.
3.  **Update Step**: For each of the 'K' clusters, the algorithm calculates a new centroid by taking the mean (average) of all the data points assigned to that cluster.
4.  **Repeat**: Steps 2 and 3 are repeated. The algorithm keeps re-assigning points to the nearest (new) centroid and then re-calculating the centroids based on the new assignments. This process continues until the assignments no longer change, meaning the clusters have stabilized.

The final centroids represent the centers of the discovered groups, and the labels for each data point indicate which group it belongs to.

#### **Hands-On Lab 1: Finding Synthetic Clusters**

To understand how K-Means works in practice, we'll start with a synthetic dataset where the clusters are easy to visualize. We'll use `scikit-learn`'s `make_blobs` function to generate this data.

```python
import polars as pl
import matplotlib.pyplot as plt
from sklearn.datasets import make_blobs
from sklearn.cluster import KMeans

# 1. Generate synthetic data
# We create 300 samples divided among 4 distinct "blobs" or clusters.
X, y_true = make_blobs(
    n_samples=300,
    centers=4,
    cluster_std=0.70,
    random_state=42
)

# For visualization, let's put it in a DataFrame
df = pl.DataFrame(X, schema=["feature1", "feature2"])

# 2. Instantiate and fit the K-Means model
# We tell the model to find 4 clusters.
kmeans = KMeans(n_clusters=4, random_state=42, n_init=10)
kmeans.fit(X)

# 3. Get cluster assignments and centroids
# The model assigns a cluster label (0, 1, 2, or 3) to each data point.
y_kmeans = kmeans.predict(X)
centers = kmeans.cluster_centers_

# 4. Visualize the results
plt.figure(figsize=(8, 6))
plt.scatter(df["feature1"], df["feature2"], c=y_kmeans, s=50, cmap='viridis')
plt.scatter(centers[:, 0], centers[:, 1], c='red', s=200, alpha=0.75, marker='X')
plt.title("K-Means Clustering on Synthetic Data")
plt.xlabel("Feature 1")
plt.ylabel("Feature 2")
plt.show()
```

In the resulting plot, you will see the data points colored according to the cluster they were assigned to by the K-Means algorithm. The red 'X' markers indicate the final positions of the cluster centroids. This visualization clearly shows how the algorithm successfully identified the four distinct groups present in our synthetic data.

That's a great point. You are correct; performing a meaningful exploratory analysis to characterize clusters is not practical with a synthetic dataset because the features (`feature1`, `feature2`, etc.) have no real-world meaning.

To demonstrate this crucial step, we should use a dataset where the features are understandable. The **Iris dataset** is perfect for this. We can perform clustering on the four measurement features and then analyze the characteristics of the resulting clusters.

-----

### Characterizing Discovered Clusters

After identifying clusters, the next step for an analyst is to understand *what* each cluster represents. This is done by performing exploratory analysis on the clusters to build a "profile" or "persona" for each one.

Let's do this with the Iris dataset. We will cluster the flowers based only on their measurements and then analyze the resulting groups.

```python
import polars as pl
import pandas as pd
from sklearn.datasets import load_iris
from sklearn.cluster import KMeans

# 1. Load the Iris dataset
iris = load_iris()
# We are using pandas here for easy use of .groupby() and .mean() later
X_iris = pd.DataFrame(iris.data, columns=iris.feature_names)

# 2. Apply K-Means
# We know there are 3 species, so let's set K=3
kmeans_iris = KMeans(n_clusters=3, random_state=42, n_init=10)
X_iris['cluster'] = kmeans_iris.fit_predict(X_iris)

# 3. Characterize the clusters
# Now, let's group by the new cluster label and find the average of each feature.
cluster_profiles = X_iris.groupby('cluster').mean()

print("Cluster Profiles (Average feature values for each cluster):")
print(cluster_profiles)
```

#### **Analysis of the Results**

By examining the output table, you can now build a narrative for each cluster:

  * **Cluster 0**: This group has a very **small** petal length and petal width.
  * **Cluster 1**: This group has the **largest** petal length, petal width, and sepal length.
  * **Cluster 2**: This group falls in the **middle** for most measurements.

If we were to compare these profiles to our knowledge of the actual iris species, we would find that our unsupervised clustering algorithm has successfully rediscovered the `setosa`, `virginica`, and `versicolor` species based purely on the structure of the data. This demonstrates how an analyst can use clustering to identify meaningful segments in a dataset, even without having prior labels.

That's a great point. You are correct; performing a meaningful exploratory analysis to characterize clusters is not practical with a synthetic dataset because the features (`feature1`, `feature2`, etc.) have no real-world meaning.

To demonstrate this crucial step, we should use a dataset where the features are understandable. The **Iris dataset** is perfect for this. We can perform clustering on the four measurement features and then analyze the characteristics of the resulting clusters.
---

Of course. Here is your example, revised and framed specifically for the clustering section.

***
### **Business Application: Discovering Data-Driven Market Segments**

A company often structures its operations around pre-defined segments, such as geographic sales regions (e.g., Northeast, West Coast) or existing business units. While administratively useful, these groupings are often arbitrary from a customer behavior perspective.

**The Challenge:** Customers in the "Northeast" region might not all behave similarly. This group could contain a mix of high-value enterprise clients and small, independent businesses with vastly different needs and purchasing patterns. Marketing campaigns aimed at the entire region are therefore inefficient.

**The Clustering Solution:**
An analyst can use clustering on customer data—including features like purchase frequency, average order value, product categories bought, and support ticket volume—to discover the *actual*, data-driven segments that exist within the customer base.

The algorithm might identify **latent groups** such as:
* A "High-Volume, Low-Maintenance" cluster.
* A "New, High-Growth Potential" cluster.
* A "Legacy, At-Risk" cluster.

These data-driven segments are far more meaningful than the arbitrary geographic regions. By understanding the true underlying structure of the market, the company can create targeted strategies for sales, marketing, and product development that address the specific needs of each real customer group.

-----

### Characterizing Discovered Clusters

After identifying clusters, the next step for an analyst is to understand *what* each cluster represents. This is done by performing exploratory analysis on the clusters to build a "profile" or "persona" for each one.

Let's do this with the Iris dataset. We will cluster the flowers based only on their measurements and then analyze the resulting groups.

```python
import polars as pl
import pandas as pd
from sklearn.datasets import load_iris
from sklearn.cluster import KMeans

# 1. Load the Iris dataset
iris = load_iris()
# We are using pandas here for easy use of .groupby() and .mean() later
X_iris = pd.DataFrame(iris.data, columns=iris.feature_names)

# 2. Apply K-Means
# We know there are 3 species, so let's set K=3
kmeans_iris = KMeans(n_clusters=3, random_state=42, n_init=10)
X_iris['cluster'] = kmeans_iris.fit_predict(X_iris)

# 3. Characterize the clusters
# Now, let's group by the new cluster label and find the average of each feature.
cluster_profiles = X_iris.groupby('cluster').mean()

print("Cluster Profiles (Average feature values for each cluster):")
print(cluster_profiles)
```