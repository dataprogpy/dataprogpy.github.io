---
icon: material/numeric-4
---

# Feature Engineering Using Unsupervised Models


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
