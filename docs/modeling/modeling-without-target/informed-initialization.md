---
icon: material/numeric-5
---

# Clustering + Dimensionality Reduction

This section demonstrates a useful technique that combines dimensionality reduction (PCA) and clustering (KMeans) to solve a common problem in clustering: **finding good starting points**.

``` python
from sklearn.cluster import KMeans
from sklearn.decomposition import PCA

kmeans = KMeans(init="k-means++", n_clusters=n_digits, n_init=4, random_state=0)

kmeans = KMeans(init="random", n_clusters=n_digits, n_init=4, random_state=0)

pca = PCA(n_components=n_digits).fit(data)
kmeans = KMeans(init=pca.components_, n_clusters=n_digits, n_init=1)

```

## The Problem: Where to Start?

The KMeans algorithm works by iteratively refining the position of its cluster centers (centroids). However, its final performance can be very sensitive to where those centroids are placed *initially*.

* **Random Initialization (`init="random"`)**: This is the simplest approach. It places centroids randomly. If it gets an unlucky start (e.g., placing two centroids very close together in the middle of a single cluster), the final result can be poor. That's why it needs to be run multiple times (`n_init=4`) to hope for a good outcome.
* **K-Means++ Initialization (`init="k-means++"`)**: This is a "smarter" random start. It tries to place the initial centroids far away from each other, which usually leads to much better and more consistent results than the purely random method. It is the default and recommended starting point.

## The Solution: Using PCA as an Intelligent Guide

The third method, **PCA-based initialization**, offers an even more informed starting strategy. Here’s the thinking behind it:

1.  **What does PCA do?** As we've learned, PCA finds the directions of maximum **variance** in the data. These "principal components" are the axes along which the data is most spread out. These directions represent the fundamental structure of the dataset.

2.  **How does this help KMeans?** The directions where the data is most spread out are also the directions where clusters are most likely to be separated. Instead of starting with random points, we can give KMeans a powerful hint.

3.  **The Code in Action:**
    * `pca = PCA(n_components=n_digits).fit(data)`: First, we run PCA to find the 10 most important "directions" in our 64-dimensional data.
    * `kmeans = KMeans(init=pca.components_, ...)`: This is the key step. We are telling KMeans: **"Don't start with random points. Instead, use the 10 principal components you found as the initial locations for the 10 cluster centroids."**

By initializing the centroids along the primary axes of variation, we are seeding the algorithm with strong prior information about the data's structure. This gives it a significant head start, often leading to a stable and high-quality result without needing multiple random restarts (which is why `n_init` is set to `1`).

This example is a perfect demonstration of how unsupervised techniques can be used in conjunction to create a more robust and intelligent modeling process.

While feature engineering typically focuses on transforming the **input data** (`X`), a more advanced strategy involves using data insights to transform the **model itself**.

The "PCA-based" KMeans initialization described here is a perfect example of this. Here we didn't change the data the KMeans algorithm processed; instead, we used a feature extraction method (PCA) to find the data's underlying structure and passed that information to the model's `init` hyperparameter.

Think of this as **"feature engineering for the model's starting state."** It's a powerful demonstration of how insights from one algorithm can be used to make another algorithm more effective, showcasing the deep synergy that is possible in a machine learning workflow.


## **Learning Activity: Evaluating Initialization Strategies**

See these concepts in action. Run the complete code block in your accompanying Jupyter notebook. Once the code finishes, you will see an output table comparing the three different initialization strategies.

### **Analyze the Results**

Examine the output table closely and answer the following questions:

1.  **Performance Metrics**: Compare the scores for `k-means++` and `PCA-based` initialization across the different metrics (e.g., ARI, AMI, Silhouette). Which method achieved a better clustering performance on this dataset?

2.  **Execution Time**: Look at the `time` column. How does the execution time of the `PCA-based` method compare to the others?

3.  **Synthesis**: Based on your observations, what is the trade-off between using the `k-means++` and the `PCA-based` initialization methods for this particular problem? When might you choose one over the other?