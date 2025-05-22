--- 
icon: material/numeric-6
---
# Stiching and Saving Data

**Stitching and Saving Data**

In real-world scenarios, data often resides in multiple tables or files. This module covers how to "stitch" these related datasets together using joins. We will also cover how to save your processed DataFrames to files for future use or sharing. Finally, we'll briefly touch upon some workflow concepts.

??? note "Prerequisites"

    ```python
    # Prerequisites: Ensure Polars is imported.
    import polars as pl

    # --- Placeholder DataFrames (consistent with those used/modified in previous modules) ---
    # Re-establishing them here for clarity if this module is run standalone.

    customers_df = pl.DataFrame({
        "customer_id": [101, 102, 103, 104, 105, 106, 107, 108, 109, 110, 111, 112, 113, 114, 115],
        "name": ["Alice Wonderland", "Bob The Builder", "Charlie Brown", "Diana Prince", "Evan Almighty", "Fiona Gallagher", "George Jetson", "Hannah Montana", "Ian Malcolm", "Jane Doe", "Kevin McCallister", "Laura Palmer", "Michael Scott", "Nancy Drew", "Oscar Grouch"],
        "registration_date_str": ["2022-01-15", "2022-03-22", "2022-05-10", "2022-07-01", "2022-08-19", "2023-01-20", None, "2023-04-05", "2023-06-12", "2023-07-21", "2023-09-01", "2023-10-15", "2024-02-10", "03/15/2024", "2024-05-01"],
        "city": ["New York", "London", "Paris", "New York", "London", "New York", "Paris", "Berlin", "London", "New York", "Chicago", "Twin Peaks", "Scranton", "River Heights", "New York"],
        "age": [28, 35, 45, 3000, 42, 29, 50, 22, 55, None, 12, 17, 48, 18, 60]
    }).with_columns(
        pl.when(pl.col("age") > 100).then(None).otherwise(pl.col("age")).cast(pl.Int64, strict=False).alias("age_cleaned")
    )

    orders_df = pl.DataFrame({
        "order_id": [201, 202, 203, 204, 205, 206, 207, 208, 209, 210, 211, 212, 213, 214, 215, 216, 217, 218],
        "customer_id": [101, 102, 101, 103, 104, 102, 105, 101, 106, 108, 103, 107, 110, 111, 102, 113, 101, 115], # Includes customer_id 111 not in current customers_df for left join illustration
        "order_date_str": ["2023-01-20 10:30:00", "2023-02-15 11:05:30", "2023-02-28 14:12:55", "2023-03-10 09:00:15", "2023-03-12 17:45:00", "2023-04-05 12:00:00", "2023-04-22 16:20:30", "2023-05-01 10:00:00", "2023-05-15 08:55:10", "2023-06-01 11:30:00", "2023-06-20 13:40:00", "2023-07-01 00:00:00", "2023-07-25 10:10:10", "2023-09-10 14:20:30", "2023-11-05 19:00:00", "2024-02-15 09:30:00", "2024-03-01 10:00:00", "2024-05-05 12:12:12"],
        "product_category": ["Books", "Tools", "Electronics", "Home Goods", "Antiques", "Books", "Electronics", "Books", "Beauty", "Music", "Home Goods", "Electronics", "Clothing", "Toys", "Tools", "Office Supplies", "Electronics", "Home Goods"],
        "quantity": [2, 1, 1, 3, 1, 1, None, 3, 2, 5, 1, 1, 2, 3, 1, 10, 1, 1],
        "unit_price": [15.99, 199.50, 799.00, 25.00, 2500.00, 12.50, 99.99, 10.00, 45.75, 9.99, 150.00, 499.50, 75.00, 29.99, 75.00, 4.99, 1200.00, 12.00]
    }).with_columns([
        pl.col("quantity").cast(pl.Int64, strict=False),
        pl.col("unit_price").cast(pl.Float64, strict=False),
        # For simplicity, assuming discount_applied and order_datetime are handled if needed later
    ])

    # Let's refine customers_df to ensure all customer_ids in orders_df are present if we want to avoid nulls in inner joins
    # Or, keep it as is to demonstrate how different joins handle mismatches.
    # For this example, let's keep customers_df as is to show how left join differs.
    # Example customer_id in orders_df (e.g., 105) might not be in a shortened customers_df if it was trimmed.
    # The current customer_df includes 101-115. orders_df has customer_ids within this range.
    # Let's adjust orders_df slightly to have a customer_id that *might not* be in customers_df if it were a subset
    # For a clean join demonstration, we will use the full customers_df and orders_df where customer_ids align.
    # The `customers_df` covers 101-115. `orders_df` uses customer_ids within this range.
    # To illustrate left join better where left has unmatched, let's assume an order from customer 999
    # orders_df = orders_df.vstack(pl.DataFrame({"order_id": [999], "customer_id": [999], ...})) # this is complex for now
    # Instead, we'll focus on cases where customers might not have orders.

    ```

## **1. Stitching Data: Joining DataFrames with `.join()`**

Often, the data you need for a comprehensive analysis is spread across multiple tables or files. For instance, you might have order details in one DataFrame and customer information in another. Joining allows you to combine these DataFrames based on common key columns.

  * **The `DataFrame.join()` Method:**
    The primary method for this is `df.join(other_df, on="key_column_or_columns", how="join_strategy")`.

      * `other_df`: The DataFrame to join with.
      * `on`: A string specifying the common column name, or a list of strings for multiple key columns. If key column names differ between DataFrames, use `left_on="left_col_name", right_on="right_col_name"`.
      * `how`: A string defining the type of join. Common types include:
          * **`inner` (default):** Keeps only rows where the key(s) exist in *both* DataFrames.
          * **`left`:** Keeps all rows from the *left* DataFrame (the one calling the `.join()` method) and the matched rows from the *right* DataFrame. If there's no match in the right DataFrame, columns from the right DataFrame will be filled with `null`.
          * **`outer`:** Keeps all rows from *both* DataFrames. If there's no match for a key in the other DataFrame, missing columns are filled with `null`.
          * Other types like `semi`, `anti`, and `cross` exist for more specific use cases.

  * **Example: Combining Orders with Customer Information**
    Let's join `orders_df` with `customers_df` to enrich each order with details about the customer who placed it. The common key is `customer_id`.

    ```python
    # Inner Join: Only orders with matching customers, and only customers with matching orders
    # (In this case, all customer_ids in orders_df are in customers_df based on full placeholders)
    merged_inner_df = orders_df.join(
        customers_df,
        on="customer_id",
        how="inner"
    )
    print("Inner Join - Orders with Customer Details:")
    print(merged_inner_df.select([
        "order_id", "customer_id", "name", "city", "product_category", "unit_price"
    ]).head())
    print(f"Shape of inner joined df: {merged_inner_df.shape}")


    # Left Join: All orders, with customer details if available
    # This is useful if you want to keep all orders, even if some customer details might be missing (though not in our current full dataset).
    # Or, more relevantly, if some customers in customers_df have no orders, they wouldn't appear in an inner join with orders_df as left.
    # Let's demonstrate with customers_df on the left, to see all customers and their orders.
    customer_orders_left_df = customers_df.join(
        orders_df, # Joining with orders data
        on="customer_id",
        how="left" # Keep all customers, match with their orders
    )
    print("\nLeft Join - All Customers with Their Orders (if any):")
    # Select relevant columns to display
    print(customer_orders_left_df.select([
        "customer_id", "name", "city", "order_id", "product_category"
    ]).sort("customer_id").head(10)) # Showing more rows to see customers with and without orders
    # Find customers who might not have orders (order_id would be null)
    print("\nCustomers with no orders (example from left join):")
    print(customer_orders_left_df.filter(pl.col("order_id").is_null()).select(["customer_id", "name"]))
    print(f"Shape of left joined df (customers as left): {customer_orders_left_df.shape}")
    ```

    *Business Context:* An inner join might be used to analyze only confirmed sales linked to known customers. A left join (e.g., `customers_df.join(orders_df, ..., how="left")`) is crucial for analyses like identifying customers who haven't placed an order.

## **2. Saving DataFrames to Files**

After performing your data wrangling tasks—cleaning, transforming, joining—you'll often want to save the resulting DataFrame. This allows you to persist your work for later use, share it with colleagues, or use it as input for other tools or models.

  * **Saving to CSV (`.write_csv()`):**
    Writes the DataFrame to a Comma-Separated Values file.

    ```python
    # Example: Saving the merged_inner_df (orders with customer details) to a CSV file
    # Let's select a subset of columns for a cleaner output file
    output_df_for_csv = merged_inner_df.select([
        "order_id", "customer_id", "name", "city", "product_category", "quantity", "unit_price"
    ]).sort("order_id")

    try:
        output_df_for_csv.write_csv("enriched_orders.csv")
        print("\nSuccessfully saved 'enriched_orders.csv'")
    except Exception as e:
        print(f"Error saving CSV: {e}")
    ```

    You can specify other parameters like `separator` if you don't want a comma.

  * **Saving to JSON (`.write_json()`):**
    Writes the DataFrame to a JSON file. A common format is a list of records (row-oriented).

    ```python
    # Example: Saving the same output_df_for_csv to a JSON file
    try:
        # row_oriented=True creates a list of JSON objects, one per row.
        # pretty=True makes the JSON output human-readable (indented).
        output_df_for_csv.write_json("enriched_orders.json", row_oriented=True, pretty=True)
        print("Successfully saved 'enriched_orders.json' (row-oriented, pretty)")
    except Exception as e:
        print(f"Error saving JSON: {e}")
    ```

    Polars' `write_json` can also produce column-oriented JSON if `row_oriented=False`.

## **3. Workflow and Execution Notes**

  * **Method Chaining for Cleaner Pipelines:**
    Polars encourages chaining multiple operations together to create concise and readable data transformation pipelines.

    ```python
    # Example: Filter electronics orders, calculate total price, select relevant columns, and sort
    # (Assuming orders_df has 'quantity' and 'unit_price')
    final_electronics_report_df = (
        orders_df
        .filter(pl.col("product_category") == pl.lit("Electronics"))
        .with_columns(
            (pl.col("quantity") * pl.col("unit_price")).alias("total_sale_amount")
        )
        .select(["order_id", "customer_id", "total_sale_amount"])
        .sort("total_sale_amount", descending=True)
    )
    print("\nChained operations for electronics report:")
    print(final_electronics_report_df.head())
    # This final_electronics_report_df could then be saved.
    # final_electronics_report_df.write_csv("electronics_sales_report.csv")
    ```

  * **Lazy Execution and `.collect()` Revisited:**

      * As mentioned before, Polars often builds an optimized plan for your operations (lazy execution) rather than executing each step immediately. This is especially true when you start a chain with `df.lazy()`.
      * When you have a "LazyFrame" (e.g., from `df.lazy().some_operation()`), the computations are only performed when you explicitly call `.collect()`.
      * Many "eager" operations (like most of what we've used directly on DataFrames like `df.filter()`) will execute and return a new DataFrame immediately. However, understanding lazy evaluation is key to appreciating Polars' performance with complex queries.

    <!-- end list -->

    ```python
    # Conceptual example of a lazy query
    lazy_result_plan = (
        orders_df.lazy() # Start a lazy query
        .filter(pl.col("unit_price") > pl.lit(100))
        .group_by("product_category")
        .agg(pl.sum("quantity").alias("total_high_value_quantity"))
    )
    print("\nLazy query plan constructed (no execution yet):")
    print(lazy_result_plan) # Shows the logical plan

    # To execute the plan and get the actual DataFrame:
    # final_high_value_summary_df = lazy_result_plan.collect()
    # print("\nExecuted lazy query result:")
    # print(final_high_value_summary_df)
    ```

    For many operations in an introductory context, Polars feels "eager," but its lazy core is always there for optimization.
