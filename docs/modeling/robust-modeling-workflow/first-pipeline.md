---
icon: material/numeric-4
---

# Building a Regression Pipeline

In this section, we'll apply the concepts of preprocessing and pipelines to a regression problem. Our goal is to build a basic model that predicts house prices from the King County dataset. This exercise will serve as the foundation for the more advanced tuning and evaluation techniques we'll cover next.

First, ensure you have the [`kc_house_data.csv`](https://github.com/dataprogpy/code-samples/blob/7431e8fe6b7b3502c031ca25b1ba070488b583f7/data/kc_house_data.csv) file available in your environment.

## **Setup: Importing Libraries and Loading Data**

We begin by importing the necessary libraries and loading our dataset into a Polars DataFrame.

???+ warning "Truncated code listing"

    The code shown in this page are to help your understanding of your concepts 
    and workflows being discussed in this specific lesson. Please see the 
    code demo notebook file for a fully functional code.

```python
import polars as pl
from sklearn.model_selection import train_test_split
# pipeline meta estimator
from sklearn.pipeline import (
    Pipeline,
    make_pipeline,
)
# transformers
from sklearn.preprocessing import (
    StandardScaler, 
    OneHotEncoder,
    FunctionTransformer,
)
# models
from sklearn.tree import DecisionTreeRegressor
#metrics
from sklearn.metrics import mean_absolute_error

# Load the dataset
df = pl.read_csv("kc_house_data.csv")
```

## **Step 1: Feature Selection and Data Preparation**

For this initial model, we will select a small subset of numerical features that we hypothesize will be predictive of the house price. We will define our feature matrix `X` and our target vector `y`.

```python
# Select a subset of features for our initial model
numeric_features = ['bedrooms', 'bathrooms', 'sqft_living', 'sqft_lot',
                    'floors', 'waterfront', 'view', 'condition', 'grade',
                    'sqft_above', 'sqft_basement', 'yr_built', 'yr_renovated', 
                    'lat', 'long', 'sqft_living15', 'sqft_lot15', ]

categorical_features = [
    # 'zipcode', 
    'school_district',
    ]
target = "price"

X = df
y = df.select(target)

# Display the shape of our feature matrix
print(f"Shape of X: {X.shape}")
```

## **Step 2: Create the Preprocessing Pipeline**

Different feature subsets often require unique preprocessing. We can approach this systematically with the following steps:

* **Identify Subsets & Steps**: First, identify the different feature types (e.g., numerical, categorical) and the specific preprocessing each requires. Note that these steps are often order-sensitive; for example, missing value imputation should typically occur before scaling or encoding.
* **Build Feature Pipelines**: For each feature subset, create a dedicated pipeline that performs its required sequence of transformations.
* **Combine into a Preprocessor**: Combine the individual feature pipelines into a single, comprehensive preprocessor. This composite transformer will apply the correct steps to the correct columns of your dataset.
* **Create a Final Model Pipeline**: Finally, create the full pipeline by adding your chosen machine learning model (the estimator) as the last step. This final object encapsulates the entire workflow, from data preprocessing to model training.

<!-- 1.  **`DecisionTreeRegressor()`**: Our regression model. We'll set a `random_state` for reproducibility. -->

<!-- end list -->

```python
# function that perform data cleaning step
def tweak_housing(df):
    pass

tweak_transformer = FunctionTransformer(tweak_housing)

# Create the transformer pipeline for a subset of features
numeric_transformer = make_pipeline(
    SimpleImputer(strategy='median'),
    StandardScaler(),
)

# Note the alternative syntax for creating a pipeline
categorical_transformer = Pipeline(
    steps=[
         ('onehot', OneHotEncoder(handle_unknown='ignore',
                              sparse_output=False)),
    # ('target', TargetEncoder()),
    # ('std', StandardScaler()),
    ])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat',categorical_transformer, categorical_features),
        ],
        # remainder='passthrough',
    )
```

#### **Step 3: Split, Train, and Predict**

We will now execute the familiar workflow: split the data into training and testing sets, then fit our entire pipeline on the training data. The `Pipeline` object handles passing the data through each step correctly. Finally, we'll make predictions on the test set.

```python
# Split the data into training and testing sets
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

pipe = Pipeline(steps=[
    ('tweak', tweak_transformer),
    ('preprocessor', preprocessor),
    ('dt', DecisionTreeRegressor()),
    ])
# Fit the entire pipeline on the training data
pipe.fit(X_train, y_train)

# Make predictions on the test data
predictions = pipe.predict(X_test)
```

#### **Step 4: Evaluate the Model**

Since this is a regression task, we can't use "accuracy." We will use the **Mean Absolute Error (MAE)**, which measures the average absolute difference between our model's predictions and the actual house prices. This gives us an error value in the same unit as our target (in this case, US dollars).

```python
# Calculate Mean Absolute Error
mae = mean_absolute_error(y_test, predictions)

print(f"Mean Absolute Error of the model: ${mae:,.2f}")
```

*This MAE value serves as our **baseline**. In the following sections, we will learn techniques like cross-validation and hyperparameter tuning to try and improve upon this initial result.*


## Improving Performance: Hyperparameter Tuning

Our initial pipeline provides a performance baseline, but it's unlikely to be optimal. The `DecisionTreeRegressor` we used was created with its default settings. To improve our model, we need to find the best **configuration** for our specific problem. This is accomplished through **hyperparameter tuning**.

It's crucial to distinguish between **parameters** and **hyperparameters**:

* **Parameters** are values the model learns *from the data* during the `.fit()` process. For a Decision Tree, these are the questions it learns to ask at each split (e.g., "is `sqft_living` > 2000?"). You don't set these yourself.
* **Hyperparameters** are settings you, the data scientist, choose *before* training the model. They are passed as arguments when you instantiate the model. For our `DecisionTreeRegressor`, examples include `max_depth` (the deepest the tree can go) or `min_samples_leaf` (the minimum number of data points required to be at a leaf node).

The process of finding the optimal combination of these settings is called **tuning**. Manually testing different combinations is tedious and inefficient. In the next section, we'll introduce a more robust and automated approach to both evaluate and tune our models.
