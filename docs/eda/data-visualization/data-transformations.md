Okay, let's move on to **Section 5: Data Transformations for Visualization**.

This section is crucial for bridging data manipulation skills (which students have started with Polars) directly to the needs of effective visualization.

---
## **Section 5: Data Transformations for Visualization**

---
### **Material for MkDocs Site Content (Page: `05_data_transformations.md`)**

```markdown
---
title: Data Transformations for Visualization
---

Often, your raw data isn't in the perfect shape or format for the visualization you have in mind. You might need to filter it, create new categories, or calculate summary statistics before you can effectively plot it. Both Polars (your primary data manipulation tool) and Altair offer ways to perform these transformations.

## Why Transform Data for Visualization?

Common reasons to transform data before or during visualization include:

* **Filtering**: Focusing on a specific subset of your data (e.g., data for a particular year, region, or category).
* **Deriving New Features (Feature Engineering)**: Creating new columns from existing ones that are more suitable for plotting (e.g., extracting the year from a date, creating bins for a continuous variable, calculating ratios).
* **Aggregation**: Summarizing data to a higher level (e.g., calculating averages, counts, or sums per category) before plotting. This is essential for bar charts showing averages, summary line charts, etc.
* **Reshaping Data (Pivoting/Melting)**: Sometimes your data's structure (wide vs. long format) needs to be changed to suit certain plot types, though this is a more advanced topic we'll touch upon lightly if needed.

## Primary Approach: Polars for Pre-processing

For this course, we emphasize using **Polars for most of your data pre-processing needs** before you even pass the data to Altair. You've already started learning Polars for high-performance data manipulation.

**Advantages of using Polars for pre-transformation:**

* **Power and Flexibility**: Polars offers a rich and highly optimized API for a wide range of complex data manipulations.
* **Clarity of Workflow**: Separating data manipulation steps (in Polars) from visualization steps (in Altair) can make your overall workflow clearer and easier to debug.
* **Performance**: Polars is designed for speed, especially with larger datasets. Performing transformations efficiently upfront can be beneficial.
* **Reusability**: The transformed Polars DataFrame can be used for multiple different visualizations or other analytical tasks.

**Common Polars operations before plotting:**

1.  **Filtering Rows**:
    ```python
    # Assuming 'data_pl' is a Polars DataFrame
    filtered_data = data_pl.filter(pl.col('Year') == 2023)
    filtered_data = data_pl.filter(pl.col('Sales') > 1000)
    ```
2.  **Creating New Columns (Feature Engineering)**:
    ```python
    # Extracting year from a date column
    data_with_year = data_pl.with_columns(
        pl.col('Order_Date').dt.year().alias('Order_Year')
    )
    # Creating a new category based on a condition
    data_with_category = data_pl.with_columns(
        pl.when(pl.col('Value') > 50)
        .then(pl.lit('High'))
        .otherwise(pl.lit('Low'))
        .alias('Value_Category')
    )
    ```
3.  **Aggregating Data (`group_by().agg()`):** This is very common for creating summary charts.
    ```python
    summary_data = data_pl.group_by('Product_Category').agg(
        pl.mean('Sales').alias('Average_Sales'),
        pl.sum('Quantity').alias('Total_Quantity')
    )
    ```
    The resulting `summary_data` DataFrame is then passed to `alt.Chart()`.

## Altair's Built-in Transformations

Altair also provides its own set of transformations that can be applied directly within the charting pipeline. These are convenient for simpler, on-the-fly adjustments.

* **`transform_calculate`**: Create new fields (columns) based on an expression.
    ```python
    # chart.transform_calculate(
    #    new_field_name = "datum.existing_field * 2" # Vega expression syntax
    # )
    ```
    The expression uses Vega expression syntax, which can feel different from Python/Polars syntax.

* **`transform_filter`**: Filter data based on a condition (often used with selections, as seen in the previous section, or with Vega expressions).
    ```python
    # chart.transform_filter(
    #    'datum.some_field > 100' # Vega expression syntax for filtering
    # )
    ```

* **`transform_aggregate`**: Perform aggregations. This is implicitly used when you specify aggregations in encodings (e.g., `y='average(Sales):Q'`). You can also define it explicitly.
    ```python
    # chart.transform_aggregate(
    #    aggregated_sales='average(Sales)',
    #    groupby=['Category']
    # )
    ```

* **`transform_bin`**: Bin data, often used for histograms. This is implicitly used with `bin=True` in encodings (e.g., `alt.X('Horsepower:Q', bin=True)`).
    ```python
    # chart.transform_bin(
    #    'binned_horsepower',
    #    field='Horsepower',
    #    bin=alt.Bin(maxbins=10)
    # )
    ```

**When to use Altair transformations?**

* For very simple calculations or filters that are tightly coupled with a specific chart's logic.
* When interactivity drives the transformation (e.g., filtering based on a selection).
* For convenience with standard operations like binning or simple aggregation directly in encodings.

**General Recommendation:**
For clarity, complex logic, and consistency with your data manipulation skills, **prefer Polars for significant data transformations.** Use Altair's transformations for minor adjustments specific to a single chart or for enabling interactivity. This aligns with the course goal to interpret API design choices of major data science libraries like Polars and Altair to use them more effectively.
```

---
### **Starter Jupyter Notebook Content (Section 5)**

```python
# {{ Session Name: Data Visualization with Altair }}
# {{ Section 5: Data Transformations for Visualization }}

# Ensure libraries from previous sections are loaded
# import altair as alt
# import polars as pl
# from vega_datasets import data as vega_data
# alt.renderers.enable('colab')

# Load datasets for examples
cars_pd = vega_data.cars()
cars_pl = pl.from_pandas(cars_pd).drop_nulls() # Drop rows with any nulls for simplicity here

movies_pd = vega_data.movies. режиссеры() # movies. режиссеры() seems to be a typo, should be movies.data()
movies_pl = pl.from_pandas(vega_data.movies()).drop_nulls( # Corrected loading
    subset=['IMDB_Rating', 'Rotten_Tomatoes_Rating', 'Major_Genre', 'Release_Date']
).with_columns(
    pl.col("Release_Date").str.to_datetime().dt.year().alias("Release_Year")
)
```

```python
# ------------------------------------------------------------------------------
# 5.1 Recap: Why Transform Data?
# ------------------------------------------------------------------------------
# - To focus on relevant subsets (filtering).
# - To create more meaningful features for plotting (deriving new columns).
# - To summarize data for overview charts (aggregation).
#
# We'll primarily use Polars for this, leveraging skills you're already building.
```

```python
# ------------------------------------------------------------------------------
# 5.2 Polars for Pre-processing (Primary Focus)
# ------------------------------------------------------------------------------

# Example 1: Filtering data with Polars before plotting
# Let's plot Horsepower vs. MPG only for cars made in 'USA' after 1975.

cars_usa_post1975_pl = cars_pl.filter(
    (pl.col('Origin') == 'USA') & (pl.col('Year') > pl.datetime(1975, 1, 1)) # Assuming 'Year' is datetime
)
# If 'Year' in cars_pl is just year number, adjust filter:
# cars_pl_with_year_num = cars_pl.with_columns(pl.col('Year').dt.year().alias('Year_Number'))
# cars_usa_post1975_pl = cars_pl_with_year_num.filter(
#    (pl.col('Origin') == 'USA') & (pl.col('Year_Number') > 1975)
# )
# Let's assume cars_pl has Year as datetime for now, or re-create with year number
cars_pl_reloaded = pl.from_pandas(vega_data.cars()).drop_nulls().with_columns(
    pl.col("Year").str.to_datetime().dt.year().alias("Year_Number") # Ensure Year_Number exists
)
cars_usa_post1975_pl = cars_pl_reloaded.filter(
    (pl.col('Origin') == 'USA') & (pl.col('Year_Number') > 1975)
)


scatter_filtered_cars = alt.Chart(cars_usa_post1975_pl).mark_circle(size=60).encode(
    x='Horsepower:Q',
    y='Miles_per_Gallon:Q',
    color='Cylinders:O',
    tooltip=['Name:N', 'Year_Number:O']
).properties(
    title='USA Cars (Post-1975): Horsepower vs. MPG'
)

print("\nScatter plot of filtered USA cars (post-1975):")
scatter_filtered_cars
```

```python
# Example 2: Creating a new categorical column with Polars
# Create 'Weight_Category' (Light, Medium, Heavy) based on 'Weight_in_lbs'.

cars_with_weight_cat = cars_pl_reloaded.with_columns(
    pl.when(pl.col('Weight_in_lbs') < 2500).then(pl.lit('Light'))
    .when(pl.col('Weight_in_lbs') < 3500).then(pl.lit('Medium'))
    .otherwise(pl.lit('Heavy'))
    .alias('Weight_Category')
)

# Plot average MPG by this new 'Weight_Category'
avg_mpg_by_weight_cat = cars_with_weight_cat.group_by('Weight_Category').agg(
    pl.mean('Miles_per_Gallon').alias('Avg_MPG')
).sort('Weight_Category') # Sorting for consistent bar order if desired

bar_weight_cat = alt.Chart(avg_mpg_by_weight_cat).mark_bar().encode(
    x='Weight_Category:N', # Order might not be ideal, could make it Ordinal with sort order
    y='Avg_MPG:Q',
    color='Weight_Category:N',
    tooltip=['Weight_Category:N', 'Avg_MPG:Q']
).properties(
    title='Average MPG by Derived Weight Category'
)

print("\nBar chart using a derived categorical column:")
bar_weight_cat
```

```python
# Example 3: Aggregating data with Polars for summary charts
# Average IMDB rating of movies per Major_Genre, only for genres with at least 10 movies.

genre_counts = movies_pl.group_by('Major_Genre').agg(
    pl.count().alias('Movie_Count'),
    pl.mean('IMDB_Rating').alias('Avg_IMDB_Rating')
).filter(
    pl.col('Movie_Count') >= 10 # Keep genres with a decent number of movies
).sort('Avg_IMDB_Rating', descending=True)


bar_avg_rating_genre = alt.Chart(genre_counts).mark_bar().encode(
    x=alt.X('Avg_IMDB_Rating:Q', title='Average IMDB Rating'),
    y=alt.Y('Major_Genre:N', sort='-x'), # Sort bars by the x-value (avg rating)
    tooltip=['Major_Genre:N', 'Avg_IMDB_Rating:Q', 'Movie_Count:Q']
).properties(
    title='Average IMDB Rating by Major Genre (Min. 10 Movies)',
    width=500
)

print("\nBar chart of aggregated movie ratings per genre:")
bar_avg_rating_genre
```

```python
# Student Task: Polars Pre-processing
# 1. Using the `cars_pl_reloaded` DataFrame:
#    a. Filter for cars that have more than 4 Cylinders AND were made by 'Europe' or 'Japan'.
#    b. Create a new column 'HP_per_Cylinder' = Horsepower / Cylinders.
#    c. Calculate the average 'HP_per_Cylinder' for each 'Origin' within this filtered group.
# 2. Create a bar chart in Altair showing the average 'HP_per_Cylinder' by 'Origin' for this selection.
#    - Ensure your chart has a title and informative tooltips.

# YOUR CODE HERE for Student Task (Polars Pre-processing & Altair Chart)
# Example Solution:
# filtered_cars_hp_cyl = cars_pl_reloaded.filter(
#     (pl.col('Cylinders') > 4) & (pl.col('Origin').is_in(['Europe', 'Japan']))
# ).with_columns(
#     (pl.col('Horsepower') / pl.col('Cylinders')).alias('HP_per_Cylinder')
# )

# avg_hp_per_cyl_origin = filtered_cars_hp_cyl.group_by('Origin').agg(
#     pl.mean('HP_per_Cylinder').alias('Avg_HP_per_Cylinder')
# )

# hp_per_cyl_bar_chart = alt.Chart(avg_hp_per_cyl_origin).mark_bar().encode(
#     x='Origin:N',
#     y='Avg_HP_per_Cylinder:Q',
#     color='Origin:N',
#     tooltip=['Origin:N', 'Avg_HP_per_Cylinder:Q']
# ).properties(
#     title='Avg. HP per Cylinder (Europe/Japan, >4 Cylinders)'
# )
# hp_per_cyl_bar_chart
```

```python
# ------------------------------------------------------------------------------
# 5.3 Altair's Built-in Transformations (Brief Overview)
# ------------------------------------------------------------------------------
# Altair can perform some transformations directly. Useful for simple cases or interactivity.

# Example: `transform_calculate` (deriving a field within Altair)
# Let's convert Horsepower to Kilowatts (1 HP = 0.7457 KW) for a plot.
# Note: Vega expression syntax 'datum.FieldName'
scatter_hp_to_kw = alt.Chart(cars_pl_reloaded.head(50)).mark_point().encode( # Using a sample for brevity
    x='Kilowatts:Q', # Use the newly calculated field
    y='Miles_per_Gallon:Q',
    tooltip=['Name:N', 'Kilowatts:Q']
).transform_calculate(
    Kilowatts = "datum.Horsepower * 0.7457"
).properties(
    title='Kilowatts (Calculated in Altair) vs. MPG'
)

print("\nScatter plot with Altair's `transform_calculate`:")
scatter_hp_to_kw
```

```python
# Example: `transform_filter` (filtering within Altair)
# Show cars with Horsepower > 200. Vega expression syntax.
scatter_high_hp_altair_filter = alt.Chart(cars_pl_reloaded).mark_point().encode(
    x='Horsepower:Q',
    y='Miles_per_Gallon:Q',
    tooltip=['Name:N', 'Horsepower:Q']
).transform_filter(
    'datum.Horsepower > 200'
).properties(
    title='Cars with Horsepower > 200 (Altair Filter)'
)

print("\nScatter plot with Altair's `transform_filter`:")
scatter_high_hp_altair_filter
```

```python
# `transform_aggregate` and `transform_bin` are often used implicitly
# via encoding shorthands like `y='average(Miles_per_Gallon):Q'` or `alt.X('Horsepower:Q', bin=True)`.

# Key takeaway:
# - Polars is generally preferred for complex or reusable data transformations.
# - Altair's transforms are handy for simple, chart-specific adjustments and interactivity.
# Understanding both helps you use these libraries effectively, a key goal of this course.
```

This completes Section 5. It strongly encourages Polars for pre-processing, aligning with the course structure, while also making students aware of Altair's capabilities for simpler, inline transformations. The `movies. режиссеры()` typo was corrected during generation. The student task reinforces the Polars-first approach.

Ready for Section 6: "Best Practices & Data Storytelling Workflow"?