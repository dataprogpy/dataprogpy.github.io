---
icon: material/numeric-3
---

# Data Ingestion and Initial Exploration

This phase is where the rubber meets the road. We'll load our dataset using Polars and then attempt our first visualizations based on the task list we created. This initial foray into the data often provides a crucial "reality check," revealing challenges and insights that will guide our next steps.

## Step 4: Ingesting Data with Polars 🅿️

Our first task is to load the King County housing dataset into a Polars DataFrame. Polars is a blazingly fast DataFrame library written in Rust, offering a modern and efficient way to work with tabular data in Python.

Let's assume our data is in a CSV file named `kc_house_data.csv`.

```python
# First, ensure you have Polars installed:
# pip install polars

import polars as pl

# Define the path to your dataset
# (Adjust this path to where your file is located)
file_path = 'kc_house_data.csv'

# Load the CSV file into a Polars DataFrame
try:
    housing_df = pl.read_csv(file_path)
    print("Dataset loaded successfully!")
except Exception as e:
    print(f"Error loading dataset: {e}")
    # In a real scenario, you might exit or handle this more gracefully
    housing_df = pl.DataFrame() # Create an empty DataFrame to avoid further errors in lesson
```

Once the data is loaded, the very next step is to perform some initial inspections to ensure it loaded correctly and to get a feel for its structure and contents. Polars provides several helpful methods for this:

1.  **`.head()` - View the First Few Rows:**
    This lets you see the column names and a sample of the data to make sure it looks as expected.

    ```python
    # Display the first 5 rows of the DataFrame
    if housing_df.height > 0: # Check if DataFrame is not empty
        print("First 5 rows of the dataset:")
        print(housing_df.head())
    else:
        print("DataFrame is empty. Cannot display head.")
    ```
    *Look at the output. Do the column names match our data dictionary? Do the data values look reasonable at first glance?*

2.  **`.schema` - Understand Data Types:**
    This attribute shows each column's name and its inferred data type. This is crucial because the data type affects how you can operate on a column (e.g., you can't perform mathematical calculations on a string type without conversion).

    ```python
    if housing_df.height > 0:
        print("\nSchema of the dataset (column names and data types):")
        print(housing_df.schema)
    ```
    *Pay close attention to the data types. For example:*

    * Is `price` a numerical type (like `pl.Float64` or `pl.Int64`)?
    * Is `date` loaded as a string (`pl.Utf8`) or has it been automatically parsed into a date/datetime type? (Often, dates in custom formats like "20141013T000000" will be read as strings initially.)
    * Are `bedrooms`, `bathrooms`, `floors` numerical?

3.  **`.describe()` - Get Summary Statistics:**
    This method provides descriptive statistics for numerical columns, such as count, mean, standard deviation, min, max, and quartiles. It's a great way to get a quick overview of the distribution and range of your numerical data and spot potential anomalies.

    ```python
    if housing_df.height > 0:
        print("\nSummary statistics for numerical columns:")
        print(housing_df.describe())
    ```
    *What can you learn from this?*

    * *For `price`:* What are the min, max, and mean prices? Does the standard deviation suggest a wide spread?
    * *For `bedrooms` or `bathrooms`:* Do the min/max values make sense? (e.g., a house with 0 bedrooms, or an unusually high number).
    * *For `sqft_liv`, `sqft_lot`:* What are the typical sizes? Are there extreme values?
    * *For `yr_built`*: What's the range of construction years?
    * *Look for columns with a `null_count` greater than 0 in the `describe` output; this indicates missing values.*

4.  **`.shape` - DataFrame Dimensions:**
    This attribute returns a tuple `(height, width)` representing the number of rows and columns.

    ```python
    if housing_df.height > 0:
        print(f"\nThe dataset has {housing_df.height} rows and {housing_df.width} columns.")
    ```

This initial inspection is vital. It helps you confirm the data integrity at a high level and identify areas that might need attention before you can effectively visualize or analyze it. For instance, if `price` was loaded as a string, you'd need to convert it to a number before creating a price distribution histogram.

???+ question "**💡 Activity: Initial Data Check**"

    1.  Run the code snippets above to load and inspect the `kc_house_data.csv` dataset.
    2.  Review the output of `.head()`, `.schema`, and `.describe()`.
    3.  Jot down 2-3 initial observations or potential data issues you notice. For example:
        * Is the `date` column a string that will need parsing?
        * Are there any surprising min/max values in `describe()`?
        * Does the `schema` show any unexpected data types for key variables you plan to use from your Visualization Task List?

### **Performing a Quick Data "Smoke Test"** 🔥🧪

Before we even attempt our first chart, it's wise to perform a quick "smoke test" on the data. This isn't an exhaustive validation, but rather a set of simple checks to catch glaring issues that would immediately derail any analysis or visualization attempts. Think of it as asking: "Does this data even remotely make sense at a high level?"

Here are some common smoke tests you can perform with Polars, building on what you've already seen:

1.  **Check for Unexpected Nulls in Critical Columns:**
    While `.describe()` gives you `null_count`, you might want to specifically check columns crucial for your initial visualizations (from your Step 3 task list). For example, if `price` or `sqft_liv` are key to your first few charts:

    ```python
    if housing_df.height > 0:
        critical_cols = ['price', 'sqft_liv', 'bedrooms', 'bathrooms']
        for col_name in critical_cols:
            if col_name in housing_df.columns:
                null_count = housing_df[col_name].is_null().sum()
                if null_count > 0:
                    print(f"Warning: Column '{col_name}' has {null_count} null values.")
            else:
                print(f"Warning: Expected critical column '{col_name}' not found.")
    ```

2.  **Basic Sanity Check on Numerical Ranges:**
    Does `price` contain negative values or zeros where it shouldn't? Are there impossible values for `bedrooms` (e.g., 0 if that's unexpected, or an extremely high number like 50)?

    ```python
    if housing_df.height > 0 and 'price' in housing_df.columns:
        # Example: Check for non-positive prices if they are not expected
        non_positive_prices = housing_df.filter(pl.col('price') <= 0).height
        if non_positive_prices > 0:
            print(f"Warning: Found {non_positive_prices} records with non-positive prices.")

    if housing_df.height > 0 and 'bedrooms' in housing_df.columns:
        # Example: Check for an unusually high number of bedrooms
        unusual_bedrooms = housing_df.filter(pl.col('bedrooms') > 20).height # Assuming >20 is unusual
        if unusual_bedrooms > 0:
            print(f"Warning: Found {unusual_bedrooms} records with more than 20 bedrooms.")
    ```

3.  **Check Unique Values for Key Categorical/Boolean-like Columns:**
    For columns like `waterfront` (expected to be 0 or 1) or `condition` (expected 1-5), quickly check their unique values.

    ```python
    if housing_df.height > 0 and 'waterfront' in housing_df.columns:
        unique_waterfront = housing_df['waterfront'].unique().sort()
        print(f"\nUnique values in 'waterfront': {unique_waterfront.to_list()}")
        # Expected: [0, 1] or similar based on data dictionary

    if housing_df.height > 0 and 'condition' in housing_df.columns:
        unique_condition = housing_df['condition'].unique().sort()
        print(f"Unique values in 'condition': {unique_condition.to_list()}")
        # Expected: [1, 2, 3, 4, 5]
    ```

4.  **Sufficient Data Volume:**
    Is the DataFrame completely empty or has an unexpectedly low number of rows after loading? (`housing_df.height` check we've been using).

If these basic smoke tests reveal significant problems (e.g., a critical column is entirely missing, `price` is all nulls, `waterfront` has values like 'Yes'/'No' instead of 0/1 when 0/1 is expected by downstream code), you'd likely need to revisit the data source or ingestion script before proceeding.

Passing these smoke tests doesn't guarantee the data is perfect, but it gives you reasonable confidence that the fundamentals are in place for you to start the more detailed work of visualization and iterative cleaning.

???+ question "**💡 Activity: Run Your Smoke Test**"

    1.  Adapt and run some of the smoke test code snippets above on the housing dataset.
    2.  Focus on variables that are key to the first 1-2 visualizations you planned in your Step 3 Task List.
    3.  Did you find any immediate "red flags" that would stop you from proceeding directly to visualization?
    
    If your data passes this initial smoke screen, you're ready to try building your first visual!

---

## Step 5: First Pass Visualization & The "Reality Check" 📉🔎

This is where your planning meets practice. You'll take one of the visualization tasks from your list (from Step 3) and attempt to implement it using Python and Altair. This first attempt is incredibly valuable, not because it's expected to be perfect, but because it often provides a stark "reality check" about your data or your initial visual design.

**The Goal:** To create one of your planned visualizations and, more importantly, to observe what happens – what works, what doesn't, and what surprises the data throws at you.

**Let's Try an Example:**
Suppose from our Step 3 Visualization Task List, we had the following task, aimed at answering Sarah the Savvy Homebuyer's question about the relationship between living area and price:

| Research Question                      | Data Variables                     | Mark Type | Key Encodings (X, Y, Color, Size, Tooltip, etc.)                                        | Anticipated Chart Type |
| :------------------------------------- | :--------------------------------- | :-------- | :------------------------------------------------------------------------------------ | :--------------------- |
| How does `sqft_liv` relate to `price`? | `price`, `sqft_liv`                | `point`   | `x: sqft_liv (Q)`, `y: price (Q)`, `tooltip: [price, sqft_liv, bedrooms, bathrooms]` | Scatter Plot           |

Now, let's try to code this in Altair using our `housing_df` Polars DataFrame:

```python
import altair as alt
# Assuming housing_df is your Polars DataFrame loaded in Step 4

# Ensure Polars data can be directly used by Altair
# (Altair typically expects pandas DataFrames or dict/list of dicts,
# but can often work with objects that support the __dataframe__ protocol, which Polars does)
# For explicit conversion if needed: housing_pandas_df = housing_df.to_pandas()

if housing_df.height > 0:
    try:
        scatter_plot = alt.Chart(housing_df).mark_point().encode(
            x=alt.X('sqft_liv:Q', title='Living Area (sq ft)'), # :Q denotes Quantitative
            y=alt.Y('price:Q', title='Sale Price (USD)', axis=alt.Axis(format='$,.0f')),
            tooltip=['price', 'sqft_liv', 'bedrooms', 'bathrooms']
        ).properties(
            title='Price vs. Living Area',
            width=600,
            height=400
        ).interactive() # Adds panning and zooming

        # To display the chart in many environments (like a Jupyter notebook or an IDE with an Altair renderer):
        scatter_plot.show()
        # In MkDocs, you might save it to a JSON file and embed it, or use a Python-to-JS bridge.
        # For now, we'll assume you can display it or inspect its structure.
        print("Scatter plot generated (or attempted).")

    except Exception as e:
        print(f"Error generating scatter plot: {e}")
else:
    print("DataFrame is empty. Cannot generate scatter plot.")

```

**The "Uh-Oh" Moment: What Could Go Wrong or Look... Not Quite Right?** 😲

You run the code. What might you see? This is where the "reality check" happens. Here are some common scenarios:

1.  **Error Messages:**
    * **Incorrect Data Types:** If `price` or `sqft_liv` were accidentally loaded as strings (`pl.Utf8`) and you didn't convert them to numerical types (`pl.Float64` or `pl.Int64`), Altair (or the underlying Vega-Lite) would likely complain or produce an incorrect chart.
        * *Uh-Oh Example:* Your `price` axis might show alphabetical sorting if it's a string, or the plot might fail entirely.
    * **Column Not Found:** A typo in a column name (`'sqft_living'` instead of `'sqft_liv'`) will cause an error.

2.  **The "Blob" / Overplotting:**
    * With many thousands of houses in our dataset, a simple scatter plot might just look like a dense, unreadable blob of points, especially in areas with many similarly priced/sized homes. You can't distinguish individual points or see the density clearly.
        * *Uh-Oh Example:* Our `price` vs. `sqft_liv` plot will almost certainly suffer from this.

3.  **Skewed Distributions & Outliers:**
    * The `price` variable, in particular, is often right-skewed (many houses at lower-to-mid prices, and a few very expensive ones). These outliers can "squish" the majority of data points into a small area of the chart, making it hard to see patterns among them.
        * *Uh-Oh Example:* Most points on your scatter plot might be clustered in the bottom-left, with a few far-flung points dominating the axes. Your axes scales might be automatically set to accommodate these outliers, hiding detail in the denser regions.

4.  **Ineffective Encodings:**
    * Perhaps your initial choice of encodings doesn't effectively answer the question. Maybe a linear scale for `price` isn't as revealing as a logarithmic scale would be, given the skew. Or perhaps adding a `color` encoding for `grade` would make the scatter plot much more insightful.

5.  **Date/Time Issues:**
    * If your question involved the `date` column (e.g., "How do prices change over time?") and you haven't converted the "20141013T000000" string format into a proper datetime object, Altair won't be able to create a meaningful time-series plot. It would treat the x-axis as simple strings or numbers.
        * *Uh-Oh Example:* Your "time" axis might be sorted incorrectly or not show a continuous flow of time.

**This is Normal and Productive!**
If your first plot looks messy, is uninformative, or throws errors – **don't worry!** This is a completely normal and incredibly valuable part of the data storytelling workflow. These "Uh-Oh" moments are not failures; they are **discoveries**. They tell you:

* What **data cleaning or transformation** is needed (e.g., convert data types, handle outliers, parse dates).
* How your **visualization strategy might need to be refined** (e.g., use opacity for overplotting, try log scales, add different encodings, choose a different mark type).

These observations directly feed into the next phase: **Iterative Cleaning and Visualization**. The "problems" you see now will create the task list for data cleaning.



**This is Normal and Productive! Revisit Your Plan.**

If your first plot looks messy, is uninformative, or throws errors – **don't worry!** This is a completely normal and incredibly valuable part of the data storytelling workflow. These "Uh-Oh" moments are not failures; they are **discoveries**. They tell you:

* What **data cleaning or transformation** is needed (e.g., convert data types, handle outliers, parse dates).
* How your **visualization strategy might need to be refined** (e.g., use opacity for overplotting, try log scales, add different encodings, choose a different mark type).

**Crucially, this is the point to look back at your Data Visualization Task List from Step 3.**

* How did reality compare to your initial plan for this specific visualization?
* Was the mark type appropriate, or does the "Uh-Oh" moment suggest a different one?
* Were your initial encoding ideas effective, or do they need adjustment based on what you see (or don't see) in the plot?
* What assumptions did you make in your initial plan that the data has now challenged?

These observations directly feed into the next phase: **Iterative Cleaning and Visualization**. The "problems" you see now, and the reflections on your initial plan, will help you create a more informed data cleaning task list and refine your visualization task list (creating a V2).


???+ question "**💡 Activity: Your First Visualization Attempt, Reality Check & Plan Reflection**"

    1.  Choose **one** visualization task from the list you created at the end of Step 3.
    2.  Write the Python and Altair code to generate this visualization using the `housing_df`.
    3.  Run the code and observe the output (or any errors).
    4.  **Document your observations:**
        * Did the chart generate successfully? If not, what was the error?
        * If it did generate, how does it look? Does it effectively answer the analytical question you had in mind?
        * Note any "Uh-Oh" moments:
            * Is there overplotting?
            * Are outliers skewing the view?
            * Are the scales appropriate?
            * Do the data types seem correct for the encodings used?
            * Does it look how you expected? What surprised you?
    5.  **Reflect and Annotate Your Task List:**
        * Go back to your **Data Visualization Task List (V1)**.
        * For the visualization you just attempted, make notes directly on your list. What worked? What didn't? What data issues did you uncover? What potential changes to the visual specification (mark, encodings, transformations) might be needed?

    This reflection is crucial. The issues you spot here, and your thoughts on refining your plan, will become your immediate to-do list for data cleaning and visual refinement in the next phase.

---
