--- 
icon: material/numeric-5
---
# Data Aggregation


**Aggregating & Reshaping Data - Summarizing for Insights**

After cleaning and transforming individual data points, the next step is often to aggregate data to extract higher-level insights and summaries. This module covers grouping data and applying aggregation functions, along with a brief introduction to pivoting and window functions.

??? note "Prerequisites"

    ```python
    # Prerequisites: Ensure Polars is imported.
    import polars as pl

    # --- Placeholder DataFrames (consistent with those used/modified in Module 5) ---
    # Re-establishing them here for clarity if this module is run standalone.
    # In a continuous notebook, these would carry over.

    customers_df = pl.DataFrame({
        "customer_id": [101, 102, 103, 104, 105, 106, 107, 108, 109, 110, 111, 112, 113, 114, 115],
        "name": ["Alice Wonderland", "Bob The Builder", "Charlie Brown", "Diana Prince", "Evan Almighty", "Fiona Gallagher", "George Jetson", "Hannah Montana", "Ian Malcolm", "Jane Doe", "Kevin McCallister", "Laura Palmer", "Michael Scott", "Nancy Drew", "Oscar Grouch"],
        "registration_date_str": ["2022-01-15", "2022-03-22", "2022-05-10", "2022-07-01", "2022-08-19", "2023-01-20", None, "2023-04-05", "2023-06-12", "2023-07-21", "2023-09-01", "2023-10-15", "2024-02-10", "03/15/2024", "2024-05-01"],
        "city": ["New York", "London", "Paris", "New York", "London", "New York", "Paris", "Berlin", "London", "New York", "Chicago", "Twin Peaks", "Scranton", "River Heights", "New York"],
        "age": [28, 35, 45, 3000, 42, 29, 50, 22, 55, None, 12, 17, 48, 18, 60]
    }).with_columns(
        pl.when(pl.col("age") > 100).then(None).otherwise(pl.col("age")).cast(pl.Int64, strict=False).alias("age_cleaned") # Cleaned age
    )

    orders_df = pl.DataFrame({
        "order_id": [201, 202, 203, 204, 205, 206, 207, 208, 209, 210, 211, 212, 213, 214, 215, 216, 217, 218],
        "customer_id": [101, 102, 101, 103, 104, 102, 105, 101, 106, 108, 103, 107, 110, 111, 102, 113, 101, 115],
        "order_date_str": ["2023-01-20 10:30:00", "2023-02-15 11:05:30", "2023-02-28 14:12:55", "2023-03-10 09:00:15", "2023-03-12 17:45:00", "2023-04-05 12:00:00", "2023-04-22 16:20:30", "2023-05-01 10:00:00", "2023-05-15 08:55:10", "2023-06-01 11:30:00", "2023-06-20 13:40:00", "2023-07-01 00:00:00", "2023-07-25 10:10:10", "2023-09-10 14:20:30", "2023-11-05 19:00:00", "2024-02-15 09:30:00", "2024-03-01 10:00:00", "2024-05-05 12:12:12"],
        "product_category": ["Books", "Tools", "Electronics", "Home Goods", "Antiques", "Books", "Electronics", "Books", "Beauty", "Music", "Home Goods", "Electronics", "Clothing", "Toys", "Tools", "Office Supplies", "Electronics", "Home Goods"],
        "quantity": [2, 1, 1, 3, 1, 1, None, 3, 2, 5, 1, 1, 2, 3, 1, 10, 1, 1],
        "unit_price": [15.99, 199.50, 799.00, 25.00, 2500.00, 12.50, 99.99, 10.00, 45.75, 9.99, 150.00, 499.50, 75.00, 29.99, 75.00, 4.99, 1200.00, 12.00],
        "discount_applied": [0.05, 0.10, None, 0.0, 0.15, 0.0, 0.05, None, 0.10, 0.0, 0.05, 0.10, None, 0.05, 0.0, 0.0, 0.15, 0.0]
    }).with_columns([
        pl.col("quantity").cast(pl.Int64, strict=False),
        pl.col("unit_price").cast(pl.Float64, strict=False),
        pl.col("discount_applied").cast(pl.Float64, strict=False),
        pl.col("order_date_str").str.to_datetime(format="%Y-%m-%d %H:%M:%S", strict=False).alias("order_datetime") # Parse date
    ])
    ```

## **1. Group By and Aggregations**

The "group by" operation is a common data manipulation pattern often described as "split-apply-combine":

  * **Split:** The data is divided into groups based on the unique values in one or more specified key columns.
  * **Apply:** An aggregation function (e.g., sum, mean, count) is applied to each group independently.
  * **Combine:** The results of the applications are combined into a new DataFrame.

In Polars, this is primarily achieved using `df.group_by("key_column").agg([...])`.

  * **Creating a `GroupBy` Object:**
    `df.group_by("key_column_name")` or `df.group_by(["col1", "col2"])` creates a `GroupBy` object, which represents the grouped data. No computation happens at this stage.

  * **Applying Aggregations with `.agg()`:**
    The `.agg()` method is called on the `GroupBy` object. It takes a list of Polars expressions that define the aggregations to be performed on each group.

  * **Common Aggregation Expressions:**

      * `pl.sum("col_name")` or `pl.col("col_name").sum()`: Sum of values.
      * `pl.mean("col_name")` or `pl.col("col_name").mean()`: Average of values.
      * `pl.median("col_name")` or `pl.col("col_name").median()`: Median of values.
      * `pl.min("col_name")` or `pl.col("col_name").min()`: Minimum value.
      * `pl.max("col_name")` or `pl.col("col_name").max()`: Maximum value.
      * `pl.count()`: Number of rows in each group. (Note: `pl.col("col_name").count()` counts non-null values in that specific column within the group).
      * `pl.n_unique("col_name")` or `pl.col("col_name").n_unique()`: Number of unique values.
      * `pl.first("col_name")` or `pl.col("col_name").first()`: First value.
      * `pl.last("col_name")` or `pl.col("col_name").last()`: Last value.

  * **Examples:**

    ```python
    # Example 1: Sales summary by product_category from orders_df
    category_summary_df = orders_df.group_by("product_category").agg([
        pl.sum("quantity").alias("total_quantity_sold"),
        pl.mean("unit_price").alias("average_unit_price"),
        pl.col("order_id").count().alias("number_of_orders"), # Counts non-null order_ids per group
        pl.n_unique("customer_id").alias("unique_customers_in_category")
    ]).sort("total_quantity_sold", descending=True)

    print("Sales summary by product category:")
    print(category_summary_df)

    # Example 2: Customer demographics by city from customers_df
    # Using 'age_cleaned' which handles the outlier and converts to Int64 (allowing nulls for mean calculation)
    city_demographics_df = customers_df.group_by("city").agg([
        pl.count().alias("number_of_customers"), # Total customers per city
        pl.mean("age_cleaned").alias("average_age"),
        pl.min("age_cleaned").alias("min_age"),
        pl.max("age_cleaned").alias("max_age")
    ]).sort("number_of_customers", descending=True)

    print("\nCustomer demographics by city:")
    print(city_demographics_df)
    ```

  * **Grouping by Multiple Columns:**
    Pass a list of column names to `group_by()`.

    ```python
    # Example: Total quantity of products sold by each customer for each product category
    customer_category_sales_df = orders_df.group_by(["customer_id", "product_category"]).agg(
        pl.sum("quantity").alias("total_quantity_per_customer_category")
    ).sort(["customer_id", "product_category"])

    print("\nTotal quantity by customer and product category:")
    print(customer_category_sales_df.head(10))
    ```

## **2. Pivoting Data with `df.pivot()`**

Pivoting is a way to reshape data from a "long" format to a "wide" format, where unique values from one column become new column headers.

  * `df.pivot(values="values_col", index="index_col", columns="columns_col", aggregate_function=None)`:

      * `values`: The column whose data will populate the new pivoted columns.
      * `index`: Column(s) that will remain as rows (identifying each row).
      * `columns`: The column whose unique values will become the new column headers.
      * `aggregate_function` (optional): If there are multiple `values` for a given `index` and `column` combination, this function is used to aggregate them (e.g., 'first', 'sum', 'mean'). If not specified or data is already unique for combinations, it takes the first value.

  * **Example:** Create a wide table showing total quantity sold per product category for each year.
    First, ensure we have an 'order\_year' column.

    ```python
    orders_with_year_df = orders_df.with_columns(
        pl.col("order_datetime").dt.year().alias("order_year")
    )

    # Aggregate to get total quantity per year and product category (needed for pivot if not 1-to-1)
    yearly_category_sales = orders_with_year_df.group_by(["order_year", "product_category"]).agg(
        pl.sum("quantity").alias("total_quantity")
    ).sort(["order_year", "product_category"])

    print("\nYearly sales by category (long format):")
    print(yearly_category_sales)

    # Pivot to get product categories as columns
    pivoted_yearly_sales_df = yearly_category_sales.pivot(
        values="total_quantity",
        index="order_year",
        columns="product_category"
    ).fill_null(0) # Fill categories with no sales in a year with 0

    print("\nPivoted yearly sales (product categories as columns):")
    print(pivoted_yearly_sales_df)
    ```

    *Business Context:* This wide format can be useful for reports or visualizations where you want to compare categories side-by-side over years.

## **3. Window Functions**

Window functions perform calculations across a set of table rows that are somehow related to the current row – this set is called a "window." Unlike `group_by().agg()` operations which typically reduce the number of rows, window functions compute a value for *each* row based on its window.

  * **Concept:** Imagine you want to calculate a running total of sales, or rank products within their categories based on sales quantity, or find the difference between a customer's order amount and the average order amount for their city. These require looking at other rows related to the current one.

  * **Use Cases:** Running totals, moving averages, ranking, calculations relative to a group (e.g., percentage of group total).

  * **Polars Syntax (High-Level):** Window functions are often applied using the `.over("grouping_column_or_expression")` method on an expression.
    For example, `pl.col("sales").sum().over("category")` would compute, for each row, the total sales for that row's category (effectively broadcasting the group sum to each member of the group). `pl.col("sales").rank().over("category")` would rank sales within each category.

    ```python
    # Illustrative example (syntax can be complex for beginners):
    # Calculate running total of quantity per product category over time

    # Ensure data is sorted correctly for a meaningful running total
    orders_sorted_for_window = orders_with_year_df.sort(["product_category", "order_datetime"])

    orders_with_running_total = orders_sorted_for_window.with_columns(
        pl.col("quantity").cumsum().over("product_category").alias("running_total_quantity_in_category")
    )
    print("\nOrders with running total quantity within category (illustrative):")
    print(orders_with_running_total.filter(pl.col("product_category") == "Books").select([
        "order_datetime", "product_category", "quantity", "running_total_quantity_in_category"
    ]))
    ```

    *Note for Students:* Window functions are very powerful but have a steeper learning curve. They are mentioned here for awareness. For many business reporting tasks, standard group-by aggregations are often sufficient.
