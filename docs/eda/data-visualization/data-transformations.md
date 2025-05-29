---
title: Data Transformations for Visualization
icon: material/numeric-5
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
