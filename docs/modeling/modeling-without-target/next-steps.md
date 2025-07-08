---
icon: material/numeric-7
---

# Next Steps and Active Learning

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