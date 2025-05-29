---
icon: material/numeric-2 
title: Your First Plots with Altair
---

In the previous section, we saw *why* visualization is important and got a glimpse of Altair's basic structure. Now, let's dive into creating common chart types. We'll focus on understanding how to map your data to visual marks and encodings.

## Loading and Preparing Data

Altair works seamlessly with Pandas DataFrames and can also handle Polars DataFrames, often directly or with a simple conversion. For our examples, we'll primarily use datasets from the `vega_datasets` package and convert them to Polars DataFrames to align with the data manipulation tools you've learned.

**Loading a Sample Dataset (`cars` dataset):**
The `vega_datasets` library provides a variety of sample datasets. Let's consider the `cars` dataset.

```python linenums="1"
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
```python linenums="14"
# Use the columns 'Name', 'Miles_per_Gallon', 'Horsepower' from cars_pl_df DataFrame object
cars_pl_df.select(pl.col('Name', 'Miles_per_Gallon', 'Horsepower') #

# filter rows: get rows where "Origin" column has the value "USA"
cars_pl_df.filter(pl.col('Origin') == 'USA') #

# Drop any rows with null value in one or more columns
cars_pl_df.drop_nulls(
    subset=['Cylinders', 'Displacement']
)
# alternative syntax 
cars_pl_df.filter(
    ~pl.any_horizontal(
        pl.col('Cylinders', 'Displacement').is_null()
    )
)
```
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
```python linenums="1"
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

``` py linenums="1"
alt.Chart(
    cars_pl_df.drop_nulls(subset=['Horsepower'])
    ).mark_bar().encode(
    alt.X(
        'Horsepower:Q', 
        bin=alt.Bin(maxbins=20), 
        title='Horsepower Bins',
        ), 
    alt.Y('count():Q'),
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
4.  **Tweak DataFrame**: Tweak the DataFrame using Polars if you want to adjust properties that are strongly coupled to datapoints from the dataset. For example, the legend labels for categorical variables are easier to change in Polars than in Altair.
This iterative approach makes debugging easier and helps you build intuition for how each component affects the final visualization.