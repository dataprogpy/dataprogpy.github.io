
### **Lesson 3: Uncovering Structure with Unsupervised Learning**

**Objectives:**

* Explain the goal of unsupervised learning, particularly clustering and dimensionality reduction.
* Apply the K-Means algorithm to group data into clusters.
* Use the "Elbow Method" to estimate the optimal number of clusters.
* Apply Principal Component Analysis (PCA) to reduce the number of features.
* **Synthesize learnings by using unsupervised techniques as a feature engineering step to improve a supervised model.**

**Key Concepts & Terminology:**

* Unsupervised Learning
* Clustering & **K-Means**
* Centroids & Inertia (WCSS)
* Dimensionality Reduction
* **Principal Component Analysis (PCA)**
* Explained Variance

**Proposed Outline:**

1.  **Introduction: Learning Without an Answer Key**

    * What if we don't have a `y` variable? Introduce Unsupervised Learning.
    * Two main goals:
        * **Clustering:** Finding natural groups in the data (e.g., customer segments).
        * **Dimensionality Reduction:** Simplifying the data by reducing the number of features.

2.  **Clustering with K-Means**

    * **Intuition:** Explain the K-Means algorithm conceptually (place K centroids, assign points to nearest centroid, move centroid to the mean, repeat).
    * **Hands-On Lab 1: Finding Clusters**
        * **Dataset:** Use `sklearn.datasets.make_blobs` to create synthetic, clearly separated clusters.
        * **Workflow:**
            1.  Instantiate `KMeans(n_clusters=3)`.
            2.  Fit the model: `kmeans.fit(X)`.
            3.  Visualize the results using a scatter plot, coloring points by the `kmeans.labels_` and plotting the `kmeans.cluster_centers_`.
        * **The 'k' Problem:** How do we choose the right number of clusters? Introduce the concept of **inertia** (within-cluster sum-of-squares).
        * Show the **Elbow Method**: Plot inertia for different values of 'k' and look for the "elbow" point.

3.  **Dimensionality Reduction with PCA**

    * **Motivation:** The "Curse of Dimensionality." Too many features can make models slow, complex, and prone to overfitting. Also, we can't visualize data in 10 dimensions.
    * **Intuition:** PCA finds new, artificial axes (principal components) that capture the most variance in the data, allowing us to "squish" the data onto fewer dimensions while losing minimal information.
    * **Hands-On Lab 2: Simplifying Data**
        * **Dataset:** Use the `load_digits` dataset, which has 64 features (8x8 pixels).
        * **Workflow:**
            1.  Instantiate `PCA(n_components=2)`.
            2.  Fit and transform the data: `X_pca = pca.fit_transform(X)`.
            3.  Visualize the 2-component data on a scatter plot, coloring by the actual digit. Show how the classes are still largely separable even in 2D.
            4.  Look at `pca.explained_variance_ratio_` to see how much information the first few components retain.

4.  **Putting It All Together: Unsupervised Techniques to Improve Supervised Models**

    * This is the key synthesis step that connects the whole module.
    * **Scenario 1: Clustering as a Feature.**
        * Go back to the **King County housing dataset**.
        * Use K-Means to cluster the houses based on `lat` and `long` to create "neighborhood" clusters.
        * Add the resulting cluster label as a *new categorical feature* to your `X` data.
        * Re-run the regression pipeline from Lesson 2 with this new feature. Does the MAE improve? (It often does!).
    * **Scenario 2 (Briefly): PCA for Preprocessing.**
        * Describe a scenario: "Imagine you had 50 features related to the size of the house (`sqft_living`, `sqft_above`, `sqft_basement`, etc.). They are all highly correlated."
        * You could use PCA to reduce these 50 features to just a few "size components" and feed *those* into your model instead. This can improve model stability and performance.

5.  **Conclusion and Module Wrap-Up:**

    * Review the three main areas covered: Supervised (Classification/Regression), and Unsupervised (Clustering/Dimensionality Reduction).
    * Emphasize that they are not isolated tools but parts of a comprehensive toolkit that can be combined to solve complex problems.
    * Congratulate students on completing the entire modeling workflow, from raw data to a tuned, predictive model.

    ---

    Yes, I have the outline for Lesson 3, "Uncovering Structure with Unsupervised Learning." We planned to cover clustering with K-Means, dimensionality reduction with PCA, and a final synthesis section on how these unsupervised techniques can be used to improve supervised models.

Let's begin developing the content, starting with the first section as you suggested.

***

### **Lesson 3: Uncovering Structure with Unsupervised Learning**

### **1.0 Introduction: Learning Without an Answer Key**

In our previous lessons, we focused on **supervised learning**, where our primary goal was to predict a well-defined target variable (`y`). We had an "answer key" in our historical data, which allowed us to train and evaluate our models' ability to predict outcomes like house prices or iris species.

However, many business problems do not come with a clear-cut target variable. We often have large amounts of data and a general goal to "find something interesting" or "understand our customers better." This is where **unsupervised learning** comes in.

Unsupervised learning is a class of machine learning techniques used to find patterns, structures, and relationships in data that has not been labeled with a target outcome. Instead of predicting a known answer, the goal is to discover the inherent structure within the data itself.

In this lesson, we will explore the two most common types of unsupervised learning:

1.  **Clustering**: The task of automatically grouping similar data points together. This is widely used for applications like customer segmentation, where the goal is to discover distinct groups of customers based on their behavior or demographics.
2.  **Dimensionality Reduction**: The process of reducing the number of features in a dataset while retaining as much of the important information as possible. This is useful for simplifying models, improving performance, and enabling visualization of high-dimensional data.

Most importantly, we will see how these techniques are not just for descriptive analysis; they are powerful tools that can be used to enhance our supervised learning workflows by creating better, more informative features.

Of course. Providing concrete business applications is the best way to motivate the relevance of unsupervised learning. Let's enrich that introductory section with practical examples.

***
### **Lesson 3: Uncovering Structure with Unsupervised Learning**

### **1.0 Introduction: Learning Without an Answer Key**

In our previous lessons, we focused on **supervised learning**, where our goal was to predict a known target. However, many business challenges involve understanding the inherent structure of data when a clear target variable is absent. This is the domain of **unsupervised learning**. Its objective is not to predict an answer, but to discover patterns, groupings, and relationships directly from the data.

Let's explore some practical applications:

#### **Clustering: Finding Natural Groups** 🧑‍🤝‍🧑

Clustering algorithms automatically group similar data points together. This is fundamental to market and customer strategy.

* **Customer Segmentation**: A retail company can use clustering on customer purchase history and demographic data to identify distinct segments, such as "high-spending loyalists," "budget-conscious shoppers," or "new visitors." This allows for targeted marketing campaigns tailored to each group.
* **Market Segmentation**: Businesses can analyze survey data or public information to segment a market, identifying underserved niches or distinct user personas for product development.

#### **Association Rule Mining: Discovering Relationships** 🛒

These techniques uncover relationships between variables in large datasets.

* **Market Basket Analysis**: The classic example is discovering that customers who buy diapers also tend to buy beer. Supermarkets use this insight to optimize product placement and promotions, increasing sales. This "if-then" pattern discovery is a core unsupervised task.

#### **Anomaly Detection: Identifying Outliers** ⚠️

Anomaly detection focuses on identifying rare items or events that deviate significantly from the majority of the data.

* **Fraudulent Transactions**: By learning what "normal" transaction behavior looks like for a user, an anomaly detection system can flag unusual activities—like a purchase from a new country—as potentially fraudulent.
* **System Health Monitoring**: In IT operations, these models monitor server logs or network traffic to detect unusual patterns that could indicate a system failure or cyberattack.

#### **Dimensionality Reduction: Simplifying Complexity** 🧬

These techniques reduce the number of features in a dataset while preserving important information.

* **Feature Simplification**: A dataset might have hundreds or even thousands of features. Dimensionality reduction can consolidate these into a smaller set of powerful "meta-features," leading to faster training times and sometimes even better model performance by reducing noise.

As we'll see, these techniques are not only valuable on their own but can also be used to create powerful new features that enhance the supervised models we've already learned to build.

Excellent. Let's move on to the next section, where we'll dive into our first clustering algorithm, K-Means.

-----

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

Of course. Let's proceed with the next section, which introduces our second unsupervised learning technique: dimensionality reduction with PCA.

***
### **3.0 Dimensionality Reduction with PCA**

The second major type of unsupervised learning we will cover is **dimensionality reduction**. As its name suggests, this is the process of reducing the number of features (or dimensions) in a dataset while trying to preserve as much of the important information as possible.

#### **Motivation: Why Reduce Dimensions?**

You might wonder why we would intentionally discard features. There are several key motivations:

* **The "Curse of Dimensionality"**: As the number of features grows, the amount of data required to support a robust model grows exponentially. Too many features can make models perform worse on unseen data, a phenomenon known as the curse of dimensionality.
* **Visualization**: Humans can't visualize data beyond three dimensions. To plot and visually explore a dataset with many features, we must first reduce it to two or three dimensions.
* **Efficiency**: Fewer features mean that models require less memory and can be trained much faster, which is a critical consideration in production environments.

#### **Principal Component Analysis (PCA): The Intuition**

One of the most popular techniques for dimensionality reduction is **Principal Component Analysis (PCA)**. In essence, PCA works by finding new, artificial axes for the data. Instead of using the original features (like `petalLength` or `sepalWidth`), it creates a set of new, combined features called **principal components**.

These new components are designed to capture the maximum possible **variance** (or spread) in the data. The first principal component (PC1) is the single axis that captures the most variance. The second principal component (PC2) is the axis that captures the most *remaining* variance, and so on.

By using only the first few principal components (e.g., PC1 and PC2), we can often represent a significant portion of the original dataset's information in a much lower-dimensional space, making it easier to visualize and model.

You're right to question that—your example is a perfect illustration of **clustering**, not dimensionality reduction. The goal you described is to find better *groups of observations* (market segments), which is what clustering algorithms like K-Means do.

A better example for dimensionality reduction focuses on consolidating *features* into underlying concepts.

***

### A Concrete Example for PCA and Latent Structure

Imagine a company that wants to understand customer satisfaction and conducts a detailed survey with 20 questions.

#### The Observed Features

The raw data consists of customer ratings (1-5) for questions like:
* *Q1: How would you rate our product's price?*
* *Q2: Do you feel our product offers good value?*
* *Q3: Was our customer support agent helpful?*
* *Q4: Was your support issue resolved quickly?*
* *Q5: Do you trust our brand?*
* *Q6: Would you recommend us to a friend?*
* ...and 14 other questions.

Building a model on all 20 features can be complex and inefficient, as many questions are related.

#### Discovering the Latent Structure with PCA

Instead of treating all 20 questions as independent, PCA can analyze the correlation patterns in the responses and discover the underlying, or **latent**, drivers of satisfaction.

It might find that the responses are primarily driven by three "meta-features":
1.  **Component 1 (Value Perception):** A new, single feature that captures the combined essence of the questions about price and value (Q1, Q2).
2.  **Component 2 (Service Quality):** A second feature that summarizes the questions about the support experience (Q3, Q4).
3.  **Component 3 (Brand Loyalty):** A third feature that represents the themes of trust and recommendation (Q5, Q6).

#### The Business Value

Instead of working with 20 noisy and correlated features, the company can now use just these 3 powerful principal components. This allows them to build simpler, faster models and gain a clearer understanding of what truly drives customer satisfaction.

Yes, that's an excellent point. PCA is fundamentally a technique for uncovering the **latent structure** in data.

---
### The Connection to Latent Structure

The term **latent structure** refers to the underlying, unobserved patterns or "meta-features" that are believed to be driving the variation in the data you can actually see.

For example, in a customer satisfaction survey with 50 questions, the answers might not be driven by 50 independent factors. Instead, they could be driven by just a few latent concepts like "customer service quality," "product value," and "brand perception." PCA is a tool that helps you discover and quantify these underlying themes from the observed data. The principal components it finds are, in essence, a representation of this latent structure.

### Recommendation for this Course

For this introductory course, it's often more effective to explain the *concept* without necessarily using the formal term "latent structure," which can feel a bit abstract.

You could frame it like this:

> "Think of PCA as a tool for consolidating many correlated features into a few powerful, underlying 'themes' or 'components'. For instance, features like `sqft_living`, `sqft_above`, and `sqft_basement` are all related to a general concept of 'house size'. PCA helps us capture this overarching theme in a single, new feature (a principal component)."

This approach gives students the deep intuition you're aiming for without introducing potentially confusing academic jargon.

Excellent. Let's proceed with the hands-on lab for PCA, where students can see these concepts in action.

-----

### **Hands-On Lab 2: Simplifying Data with PCA**

This lab demonstrates how to use PCA to reduce the dimensionality of a dataset. We will use the `load_digits` dataset, which contains 8x8 pixel images of handwritten digits. Each image is "unrolled" into a row with 64 features, making it impossible to visualize directly. Our goal is to reduce these 64 features to just 2 principal components so we can plot and see the underlying structure.

```python
import polars as pl
import matplotlib.pyplot as plt
from sklearn.datasets import load_digits
from sklearn.decomposition import PCA
from sklearn.preprocessing import StandardScaler

# 1. Load the digits dataset
digits = load_digits()
X = digits.data
y = digits.target

print(f"Original data shape: {X.shape}")

# 2. Scale the data before applying PCA
# PCA is affected by scale, so it's standard practice to scale your data first.
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

# 3. Instantiate and fit PCA
# We want to reduce the data to 2 dimensions for visualization.
pca = PCA(n_components=2)
X_pca = pca.fit_transform(X_scaled)

print(f"Data shape after PCA: {X_pca.shape}")

# 4. Analyze the results
# We can check how much variance our new components captured.
explained_variance = pca.explained_variance_ratio_
print(f"Explained variance by PC1: {explained_variance[0]:.2%}")
print(f"Explained variance by PC2: {explained_variance[1]:.2%}")
print(f"Total variance captured by 2 components: {explained_variance.sum():.2%}")

# 5. Visualize the 2D representation
plt.figure(figsize=(10, 8))
plt.scatter(X_pca[:, 0], X_pca[:, 1], c=y, cmap='viridis', edgecolor='none', alpha=0.7, s=40)
plt.title('PCA of Handwritten Digits Dataset')
plt.xlabel('First Principal Component (PC1)')
plt.ylabel('Second Principal Component (PC2)')
plt.colorbar(label='Digit Label')
plt.show()
```

#### **Analysis of the Visualization**

In the resulting scatter plot, each point represents one of the original 64-dimensional images, now projected onto our two new principal components. Notice how, even in this radically simplified 2D view, clusters of digits have formed. You can see distinct groups for '0', '6', '4', and others. This demonstrates the power of PCA: it has successfully captured the most important structural information from the original 64 features and represented it in just two, allowing us to visualize the inherent separation between the different digit classes.

Of course. Let's proceed with the synthesis section, where we connect unsupervised and supervised learning.

-----

### **4.0 Putting It All Together: Unsupervised Techniques to Improve Supervised Models**

While clustering and PCA are valuable for exploratory analysis, their most powerful application in a business context is often to improve the performance of supervised models. By discovering the latent structure in the data, we can engineer new, powerful features that lead to more accurate and robust predictions.

This process, known as **feature engineering**, is a critical and creative aspect of machine learning. We will now demonstrate two common strategies for this.

#### **Scenario 1: Clustering for Feature Engineering**

Geographic location is a critical factor in house prices, but using raw latitude and longitude can be difficult for some models. We can use K-Means to group houses into "location clusters" and then add this cluster ID as a new categorical feature to our regression model.

```python
import polars as pl
from sklearn.model_selection import train_test_split
from sklearn.pipeline import make_pipeline
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.compose import ColumnTransformer
from sklearn.cluster import KMeans
from sklearn.ensemble import GradientBoostingRegressor
from sklearn.metrics import mean_absolute_error

# Load data and select initial features
df = pl.read_csv("kc_house_data.csv")
numerical_features = ["bedrooms", "bathrooms", "sqft_living", "grade"]
location_features = ["lat", "long"]
target = "price"

X = df.select(numerical_features + location_features)
y = df.select(target)

# --- Create location-based clusters ---
# 1. Fit KMeans on the location data
kmeans = KMeans(n_clusters=10, random_state=42, n_init=10) # Find 10 neighborhood clusters
location_clusters = kmeans.fit_predict(X.select(location_features))

# 2. Add the new cluster feature to our dataset
# Note: The cluster labels need to be strings for one-hot encoding
X = X.with_columns(
    pl.Series(name="location_cluster", values=location_clusters.astype(str))
)

# 3. Build a new supervised learning pipeline with the cluster feature
# We need ColumnTransformer to apply different preprocessing to different columns
preprocessor = ColumnTransformer(transformers=[
    ('num', StandardScaler(), numerical_features),
    ('cat', OneHotEncoder(), ["location_cluster"])
])

# Create the full pipeline
pipe_with_clusters = make_pipeline(
    preprocessor,
    GradientBoostingRegressor(random_state=42)
)

# 4. Evaluate the model
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
pipe_with_clusters.fit(X_train, y_train)
mae = mean_absolute_error(y_test, pipe_with_clusters.predict(X_test))

print(f"MAE of model with location clusters: ${mae:,.2f}")
```

By adding a single, powerful "location\_cluster" feature, we often see a significant improvement in our model's predictive accuracy compared to using latitude and longitude alone.

-----

#### **Scenario 2: PCA for Preprocessing**

Imagine a dataset with many highly correlated features, such as `sqft_living`, `sqft_above`, and `sqft_lot`. We can use PCA to consolidate these into a few "size components" and feed those components into our model instead. This can reduce noise and improve model stability.

```python
# In this conceptual example, we'll apply PCA to our numerical features
# and build a pipeline with the PCA-transformed data.

from sklearn.decomposition import PCA

# 1. Define the PCA pipeline
pipe_with_pca = make_pipeline(
    StandardScaler(),
    PCA(n_components=5), # Reduce to 5 principal components
    GradientBoostingRegressor(random_state=42)
)

# 2. Evaluate the PCA-based model
# We'll use only the original numerical and location features for this example
X_original = df.select(numerical_features + location_features)
X_train_orig, X_test_orig, y_train_orig, y_test_orig = train_test_split(
    X_original, y, test_size=0.2, random_state=42
)

pipe_with_pca.fit(X_train_orig, y_train_orig)
mae_pca = mean_absolute_error(y_test_orig, pipe_with_pca.predict(X_test_orig))

print(f"MAE of model with PCA features: ${mae_pca:,.2f}")
```

This demonstrates how PCA can be seamlessly integrated as a preprocessing step within a supervised learning pipeline to potentially create a more efficient and robust model.

Yes, this is the final section. It includes recommended exercises for practice and a summary of both this lesson and the entire modeling module.

***
### **5.0 Next Steps and Module Summary**

This section provides recommended exercises to practice the concepts of unsupervised learning and concludes with a summary of the entire modeling module.

#### **Homework and Active Learning**

1.  **Experiment with a Different Clustering Algorithm**: `KMeans` is excellent for spherical clusters, but other algorithms have different strengths.
    * **Task**: Use the `make_moons` dataset from `sklearn.datasets`. Apply both `KMeans` and another algorithm called `DBSCAN` to it. Visualize the results from both.
    * **Analysis**: How does `DBSCAN`'s performance on this non-spherical data compare to `KMeans`?

2.  **Explore an Alternative for Visualization**: While PCA is great, t-SNE is another powerful technique specifically designed for visualizing high-dimensional data.
    * **Task**: Import `TSNE` from `sklearn.manifold`. Apply it to the `digits` dataset (reducing it to 2 components) and create a scatter plot, just as you did with PCA.
    * **Analysis**: Compare the t-SNE visualization to the PCA visualization. Does t-SNE create more distinct visual clusters for the different digits?

3.  **Feature Engineering with PCA**: In our lab, we used clustering to create a feature. Now, apply PCA for the same purpose.
    * **Task**: Take all the numerical `sqft_` features from the King County housing dataset (`sqft_living`, `sqft_lot`, `sqft_above`, etc.). Use a `Pipeline` to scale them and then apply `PCA` to reduce them to just two "size components." Add these two new features to your dataset and train a regression model.
    * **Analysis**: Does your model's performance improve compared to using the original `sqft_` features?

4.  **Fine-Tune Your Clustering Model**:
    * **Task**: Apply the Elbow Method to the Iris dataset's features to programmatically determine the optimal number of clusters.
    * **Analysis**: Does the result from the Elbow Method correctly suggest `k=3`, which we know to be the true number of species?

***
### **Lesson and Module Summary**

In this lesson, you explored **unsupervised learning**, discovering how to find inherent structure in data without a target variable. You learned to use **K-Means** for clustering to identify meaningful segments and **PCA** for dimensionality reduction to simplify and visualize complex data. Most importantly, you saw how these powerful techniques can be used to engineer better features for your supervised models.

This concludes our module on modeling. You have progressed from understanding the basic supervised learning workflow in Lesson 1 to building robust, automated pipelines with hyperparameter tuning in Lesson 2. Now, in Lesson 3, you've added unsupervised learning to your toolkit. You now possess a comprehensive framework for building, evaluating, and improving machine learning models to solve a wide array of data science problems.