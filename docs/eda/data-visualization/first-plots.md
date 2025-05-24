Okay, let's craft the content for **Section 2: Your First Plots with Altair**.

---
## **Section 2: Your First Plots with Altair**

---
### **Material for MkDocs Site Content (Page: `02_first_plots_with_altair.md`)**

```markdown
---
title: Your First Plots with Altair
---

In the previous section, we saw *why* visualization is important and got a glimpse of Altair's basic structure. Now, let's dive into creating common chart types. We'll focus on understanding how to map your data to visual marks and encodings.

## Loading and Preparing Data

Altair works seamlessly with Pandas DataFrames and can also handle Polars DataFrames, often directly or with a simple conversion. For our examples, we'll primarily use datasets from the `vega_datasets` package and convert them to Polars DataFrames to align with the data manipulation tools you've learned.

**Loading a Sample Dataset (`cars` dataset):**
The `vega_datasets` library provides a variety of sample datasets. Let's consider the `cars` dataset.

```python
from vega_datasets import data as vega_data
import polars as pl

# Load as Pandas DataFrame first
cars_pd_df = vega_data.cars()

# Convert to Polars DataFrame
cars_pl_df = pl.from_pandas(cars_pd_df)

# Always a good idea to inspect your data
print(cars_pl_df.head())
print(cars_pl_df.shape)
print(cars_pl_df.null_count()) # Check for missing values
```

**Quick Polars Recap for Pre-Visualization:**
Before plotting, you might need to select specific columns, filter rows, or handle missing data using Polars. For example:
* `cars_pl_df.select(['Name', 'Miles_per_Gallon', 'Horsepower'])`
* `cars_pl_df.filter(pl.col('Origin') == 'USA')`
* `cars_pl_df.drop_nulls()` (Altair often handles nulls gracefully by default, but explicit handling can be necessary).

## Basic Chart Types and Encodings

The core of creating any plot in Altair involves:
1.  Initializing a `Chart` object with your data.
2.  Choosing a `mark` type (e.g., `mark_point()`, `mark_bar()`, `mark_line()`).
3.  Defining `encode()`ings to map data fields to visual properties.

**Altair Data Types:**
When specifying fields in encodings, Altair can often infer the data type. However, it's good practice to be explicit using shorthands:
* `:Q` for **Quantitative** (numerical values that can be measured, e.g., temperature, horsepower).
* `:N` for **Nominal** (categorical data with no inherent order, e.g., car origin, movie genre).
* `:O` for **Ordinal** (categorical data with a meaningful order, e.g., t-shirt size S/M/L, ratings).
* `:T` for **Temporal** (date/time values).

### 1. Scatter Plot

Scatter plots are used to visualize the relationship between two quantitative variables. Each point represents an observation.

* **Mark**: `mark_point()` or `mark_circle()`
* **Key Encodings**:
    * `x='field_name:Q'`: Maps a quantitative field to the x-axis.
    * `y='field_name:Q'`: Maps a quantitative field to the y-axis.
    * `color='field_name:N'`: Colors points by a nominal field (category).
    * `size='field_name:Q'`: Varies point size by a quantitative field.
    * `tooltip=['field1', 'field2']`: Shows specified fields on hover.

**Example:**
```python
import altair as alt

# Assuming cars_pl_df is your Polars DataFrame
scatter_plot = alt.Chart(cars_pl_df.drop_nulls(subset=['Horsepower', 'Miles_per_Gallon'])).mark_circle(size=60).encode(
    x='Horsepower:Q',
    y='Miles_per_Gallon:Q',
    color='Origin:N',  # Color by country of origin
    tooltip=['Name:N', 'Horsepower:Q', 'Miles_per_Gallon:Q']
).properties(
    title='Horsepower vs. Miles per Gallon'
)
# scatter_plot.show() # To display
```

### 2. Bar Chart

Bar charts are excellent for comparing a quantitative measure across different categories or showing counts.

* **Mark**: `mark_bar()`
* **Key Encodings**:
    * `x='category_field:N'`: Nominal field for categories on the x-axis.
    * `y='quantitative_field:Q'`: Quantitative field for bar height (or `y='count():Q'` for frequencies).
    * Or, `x='quantitative_field:Q'`, `y='category_field:N'` for horizontal bars.
    * `color='category_field:N'`: Colors bars by category (often redundant if categories are already on an axis, but useful for stacked/grouped bars).

**Example (Average MPG per Origin):**
Altair can perform simple aggregations.
```python
avg_mpg_bar_chart = alt.Chart(cars_pl_df.drop_nulls(subset=['Miles_per_Gallon', 'Origin'])).mark_bar().encode(
    x='Origin:N',
    y='average(Miles_per_Gallon):Q', # Altair aggregation
    color='Origin:N',
    tooltip=['Origin:N', 'average(Miles_per_Gallon):Q']
).properties(
    title='Average Miles per Gallon by Origin'
)
```
Alternatively, you can pre-aggregate with Polars:
```python
# Polars aggregation
avg_mpg_origin_pl = cars_pl_df.drop_nulls(subset=['Miles_per_Gallon', 'Origin']) \
    .group_by('Origin') \
    .agg(pl.mean('Miles_per_Gallon').alias('avg_MPG'))

avg_mpg_bar_chart_polars = alt.Chart(avg_mpg_origin_pl).mark_bar().encode(
    x='Origin:N',
    y='avg_MPG:Q',
    color='Origin:N',
    tooltip=['Origin:N', 'avg_MPG:Q']
).properties(
    title='Average Miles per Gallon by Origin (Polars Pre-aggregated)'
)
```

### 3. Line Chart

Line charts are ideal for showing trends over a continuous or ordered sequence, typically time.

* **Mark**: `mark_line()`
* **Key Encodings**:
    * `x='ordered_field:T'` (Temporal) or `:O` (Ordinal) or `:Q` (if a continuous quantitative sequence).
    * `y='quantitative_field:Q'`.
    * `color='category_field:N'`: For plotting multiple lines from different categories.

**Example (Seattle Weather - Max Temperature Over Time):**
We'll use the `seattle-weather` dataset.
```python
# Load seattle_weather data
weather_pd_df = vega_data.seattle_weather()
weather_pl_df = pl.from_pandas(weather_pd_df)

line_chart = alt.Chart(weather_pl_df).mark_line().encode(
    x='date:T',  # 'T' for Temporal data
    y='temp_max:Q',
    color='weather:N', # Show different weather conditions as separate lines
    tooltip=['date:T', 'temp_max:Q', 'weather:N']
).properties(
    title='Maximum Daily Temperature in Seattle Over Time by Weather Condition',
    width=600
)
```

### 4. Histogram

Histograms visualize the distribution of a single quantitative variable by dividing the data range into bins and showing the frequency of observations in each bin.

* **Mark**: `mark_bar()`
* **Key Encodings**:
    * `x='quantitative_field:Q'` with `bin=True` or `alt.X('quantitative_field:Q', bin=alt.Bin(maxbins=20))` for more control.
    * `y='count():Q'` (Altair automatically computes the count for histograms).

**Example (Distribution of Horsepower):**
```python
histogram = alt.Chart(cars_pl_df.drop_nulls(subset=['Horsepower'])).mark_bar().encode(
    alt.X('Horsepower:Q', bin=alt.Bin(maxbins=20), title='Horsepower Bins'), # Explicit binning
    y='count():Q',
    tooltip=[alt.Tooltip('Horsepower:Q', bin=True), 'count():Q']
).properties(
    title='Distribution of Car Horsepower',
    width=400
)
```

## Workflow Tip: Incremental Development

When building visualizations, especially complex ones:
1.  **Start Simple**: Get your data loaded and create the most basic version of your chart (e.g., `alt.Chart(data).mark_point().encode(x='col_A', y='col_B')`).
2.  **Add Encodings Gradually**: Introduce color, size, tooltips, etc., one by one. Check the output at each step.
3.  **Refine Properties**: Adjust titles, labels, sizes, and scales last.

This iterative approach makes debugging easier and helps you build intuition for how each component affects the final visualization.
```

---
### **Starter Jupyter Notebook Content (Section 2)**

```python
# {{ Session Name: Data Visualization with Altair }}
# {{ Section 2: Your First Plots with Altair }}

# Ensure libraries from Section 1 are loaded
# import altair as alt
# import polars as pl
# from vega_datasets import data as vega_data
# alt.renderers.enable('colab')

# ------------------------------------------------------------------------------
# 2.0 Loading and Preparing Data
# ------------------------------------------------------------------------------
# Altair works well with Polars DataFrames. We'll use `vega_datasets` for sample data.

# Load the 'cars' dataset from vega_datasets
cars_pd = vega_data.cars()
# Convert to a Polars DataFrame
cars_pl = pl.from_pandas(cars_pd)

print("Cars dataset loaded into a Polars DataFrame:")
print(cars_pl.head())
print(f"\nShape of the cars dataset: {cars_pl.shape}")

# Quick check for missing values (important before plotting some fields)
print("\nNull counts per column:")
print(cars_pl.null_count())

# For some plots, it's useful to drop rows with missing values in key columns.
# For example, if plotting Horsepower vs. Miles_per_Gallon:
cars_pl_cleaned = cars_pl.drop_nulls(subset=['Horsepower', 'Miles_per_Gallon', 'Origin', 'Name'])
print(f"\nShape after dropping some nulls: {cars_pl_cleaned.shape}")
```

```python
# ------------------------------------------------------------------------------
# 2.1 Scatter Plots: Exploring Relationships
# ------------------------------------------------------------------------------
# Scatter plots help visualize the relationship between two quantitative variables.
# Mark: mark_point() or mark_circle()
# Encodings: x, y, color, size, tooltip

# Activity: Create a scatter plot of 'Horsepower' vs. 'Miles_per_Gallon'.
# Color the points by 'Origin'. Add tooltips for 'Name', 'Horsepower', and 'Miles_per_Gallon'.

scatter_plot_cars = alt.Chart(cars_pl_cleaned).mark_circle(size=80, opacity=0.7).encode(
    x='Horsepower:Q',  # Q for Quantitative
    y='Miles_per_Gallon:Q',
    color='Origin:N',   # N for Nominal (categorical)
    tooltip=['Name:N', 'Horsepower:Q', 'Miles_per_Gallon:Q', 'Origin:N']
).properties(
    title='Car Horsepower vs. Miles per Gallon by Origin',
    width=500,
    height=350
)

print("\nScatter Plot (Horsepower vs. MPG):")
scatter_plot_cars
```

```python
# Student Task:
# 1. Modify the scatter plot above to show 'Acceleration:Q' on the x-axis and 'Weight_in_lbs:Q' on the y-axis.
# 2. Keep the color encoding by 'Origin'.
# 3. Update the tooltips and title appropriately.

# YOUR CODE HERE for Student Task (Scatter Plot)
# Example solution:
# acceleration_weight_scatter = alt.Chart(cars_pl_cleaned).mark_point().encode(
#     x='Acceleration:Q',
#     y='Weight_in_lbs:Q',
#     color='Origin:N',
#     tooltip=['Name:N', 'Acceleration:Q', 'Weight_in_lbs:Q']
# ).properties(
#     title='Car Acceleration vs. Weight by Origin'
# )
# acceleration_weight_scatter
```

```python
# ------------------------------------------------------------------------------
# 2.2 Bar Charts: Comparing Categories or Showing Counts
# ------------------------------------------------------------------------------
# Bar charts compare a quantitative measure across different categories.
# Mark: mark_bar()
# Encodings: x (categorical), y (quantitative/aggregate), color

# Example 1: Number of cars from each 'Origin' (Frequency)
count_by_origin_bar = alt.Chart(cars_pl_cleaned).mark_bar().encode(
    x='Origin:N',
    y='count():Q', # Altair's way to count occurrences
    color='Origin:N', # Optional: color bars by origin
    tooltip=['Origin:N', 'count():Q']
).properties(
    title='Number of Cars by Origin'
)

print("\nBar Chart (Count by Origin):")
count_by_origin_bar
```

```python
# Example 2: Average 'Horsepower' for each 'Origin'.
# Altair can do simple aggregations like 'average', 'sum', 'min', 'max'.
avg_hp_by_origin_bar = alt.Chart(cars_pl_cleaned).mark_bar().encode(
    x='Origin:N',
    y='average(Horsepower):Q',
    color='Origin:N',
    tooltip=['Origin:N', 'average(Horsepower):Q']
).properties(
    title='Average Horsepower by Origin'
)

print("\nBar Chart (Average Horsepower by Origin - Altair Aggregation):")
avg_hp_by_origin_bar

# Alternatively, pre-aggregate with Polars for more complex scenarios or clarity:
avg_hp_origin_polars = cars_pl_cleaned.group_by('Origin').agg(
    pl.mean('Horsepower').alias('Mean_Horsepower')
).sort('Origin')

avg_hp_by_origin_bar_polars = alt.Chart(avg_hp_origin_polars).mark_bar().encode(
    x='Origin:N',
    y='Mean_Horsepower:Q',
    color='Origin:N', # Or a fixed color: alt.value('steelblue')
    tooltip=['Origin:N', 'Mean_Horsepower:Q']
).properties(
    title='Average Horsepower by Origin (Polars Pre-aggregated)'
)
# avg_hp_by_origin_bar_polars # Uncomment to display
```

```python
# Student Task:
# 1. Create a bar chart showing the average 'Displacement' for cars with different numbers of 'Cylinders'.
#    - X-axis: 'Cylinders:O' (Ordinal, as cylinder count has an order)
#    - Y-axis: Average 'Displacement'
#    - Pre-aggregate the data using Polars.
#    - Add appropriate tooltips and a title.

# YOUR CODE HERE for Student Task (Bar Chart)
# Example solution:
# avg_displacement_by_cyl_pl = cars_pl_cleaned.group_by('Cylinders').agg(
#     pl.mean('Displacement').alias('Avg_Displacement')
# ).sort('Cylinders')

# avg_displacement_bar = alt.Chart(avg_displacement_by_cyl_pl).mark_bar().encode(
#     x='Cylinders:O', # Ordinal because there's an order
#     y='Avg_Displacement:Q',
#     tooltip=['Cylinders:O', 'Avg_Displacement:Q'],
#     color='Cylinders:N' # Treat as nominal for color, or ordinal if scheme supports
# ).properties(
#     title='Average Displacement by Number of Cylinders'
# )
# avg_displacement_bar
```

```python
# ------------------------------------------------------------------------------
# 2.3 Line Charts: Showing Trends Over Time or Sequence
# ------------------------------------------------------------------------------
# Line charts are excellent for visualizing trends.
# Mark: mark_line()
# Encodings: x (temporal or ordered), y (quantitative), color (for multiple series)

# Load the 'seattle-weather' dataset
weather_pd = vega_data.seattle_weather()
weather_pl = pl.from_pandas(weather_pd)

print("\nSeattle Weather dataset (first few rows):")
print(weather_pl.head()) # Note the 'date' column

# Activity: Plot the 'temp_max' over 'date'. Color by 'weather' condition.
# We use ':T' to specify temporal data for the 'date' field.
seattle_max_temp_line = alt.Chart(weather_pl).mark_line().encode(
    x='date:T',
    y='temp_max:Q',
    color='weather:N', # Different line for each weather type
    tooltip=['date:T', 'temp_max:Q', 'weather:N']
).properties(
    title='Maximum Daily Temperature in Seattle by Weather Condition',
    width=600,
    height=350
)

print("\nLine Chart (Seattle Max Temperature):")
seattle_max_temp_line
```

```python
# Student Task:
# Using the `seattle_weather` dataset:
# 1. Create a line chart showing the 'precipitation' over 'date'.
# 2. Only include data for days where precipitation was greater than 0. (Hint: Filter with Polars first).
# 3. Do NOT color by weather type for this one (i.e., a single line).
# 4. Add appropriate tooltips and a title.

# YOUR CODE HERE for Student Task (Line Chart)
# Example solution:
# rainy_days_pl = weather_pl.filter(pl.col('precipitation') > 0)
# precipitation_line = alt.Chart(rainy_days_pl).mark_line(color='blue').encode(
#     x='date:T',
#     y='precipitation:Q',
#     tooltip=['date:T', 'precipitation:Q']
# ).properties(
#     title='Daily Precipitation in Seattle (on Rainy Days)',
#     width=600
# )
# precipitation_line
```

```python
# ------------------------------------------------------------------------------
# 2.4 Histograms: Visualizing Distributions
# ------------------------------------------------------------------------------
# Histograms show the distribution of a single quantitative variable.
# Mark: mark_bar()
# Encodings: x (quantitative, binned), y (count)

# Activity: Create a histogram of 'Miles_per_Gallon' from the 'cars' dataset.
# Use `alt.X()` for more control over binning.
mpg_histogram = alt.Chart(cars_pl_cleaned).mark_bar().encode(
    alt.X('Miles_per_Gallon:Q', bin=alt.Bin(maxbins=15), title='Miles per Gallon'), # Explicitly define bins
    y='count():Q',
    tooltip=[alt.Tooltip('Miles_per_Gallon:Q', bin=True), 'count():Q'] # Show binned range in tooltip
).properties(
    title='Distribution of Miles per Gallon',
    width=400
)

print("\nHistogram (Distribution of MPG):")
mpg_histogram
```

```python
# Student Task:
# 1. Create a histogram for the 'Horsepower' column from the `cars_pl_cleaned` DataFrame.
# 2. Experiment with the `maxbins` parameter (e.g., 10, 20, 30) to see how it changes the plot.
# 3. Add appropriate tooltips and a title.

# YOUR CODE HERE for Student Task (Histogram)
# Example solution:
# horsepower_histogram = alt.Chart(cars_pl_cleaned).mark_bar().encode(
#     alt.X('Horsepower:Q', bin=alt.Bin(maxbins=20), title='Horsepower'),
#     y='count():Q',
#     tooltip=[alt.Tooltip('Horsepower:Q', bin=True), 'count():Q']
# ).properties(
#     title='Distribution of Car Horsepower (maxbins=20)',
#     width=400
# )
# horsepower_histogram
```

```python
# ------------------------------------------------------------------------------
# 2.5 Workflow Tip: Incremental Development
# ------------------------------------------------------------------------------
# When building visualizations:
# 1. Start with the most basic chart: `alt.Chart(data).mark_type().encode(x=..., y=...)`
# 2. Gradually add complexity: color, size, tooltips, specific binning, etc.
# 3. Refine aesthetics and properties: titles, labels, chart width/height.
# This makes it easier to understand how each piece contributes and to debug if something goes wrong.

# Example: Building a scatter plot incrementally

# Step 1: Basic scatter
# chart_step1 = alt.Chart(cars_pl_cleaned).mark_point().encode(
#     x='Displacement:Q',
#     y='Acceleration:Q'
# )
# chart_step1.display()

# Step 2: Add color by Origin
# chart_step2 = alt.Chart(cars_pl_cleaned).mark_point().encode(
#     x='Displacement:Q',
#     y='Acceleration:Q',
#     color='Origin:N'
# )
# chart_step2.display()

# Step 3: Add tooltips and a title
# chart_step3 = alt.Chart(cars_pl_cleaned).mark_point().encode(
#     x='Displacement:Q',
#     y='Acceleration:Q',
#     color='Origin:N',
#     tooltip=['Name:N', 'Displacement:Q', 'Acceleration:Q']
# ).properties(
#     title='Car Displacement vs. Acceleration by Origin',
#     width=500
# )
# chart_step3.display()
```

This covers Section 2. The MkDocs content explains the chart types and their basic syntax, while the notebook provides runnable code, direct activities for students to try, and reinforces the Polars integration. Explicit data type hinting (`:Q`, `:N`, `:T`, `:O`) is emphasized.

Ready for Section 3: "Enhancing Your Visualizations"?