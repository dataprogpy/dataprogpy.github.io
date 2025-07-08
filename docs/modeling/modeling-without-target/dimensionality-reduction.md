---
icon: material/numeric-3
---

# Dimensionality Reduction

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
