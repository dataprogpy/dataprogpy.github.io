
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
