--- 
icon: material/numeric-4
---
# Data Transformation

We'll use the `customers_df` and `orders_df` DataFrames. For realistic examples, especially for date parsing and missing value handling, ensure these DataFrames reflect the structure and "messiness" of the original CSV files.

??? note "Prerequisite"

    ```python
    # Prerequisites: Ensure Polars is imported and selectors if used.
    import polars as pl
    import polars.selectors as cs # For using column selectors like cs.numeric()

    # --- Placeholder DataFrames (mimicking loaded CSVs for standalone module execution) ---
    # These should reflect the structure from your customers.csv and orders.csv,
    # including mixed date formats, missing values, etc.

    customers_df = pl.DataFrame({
        "customer_id": [101, 102, 103, 104, 105, 106, 107, 108, 109, 110, 111, 112, 113, 114, 115],
        "name": ["Alice Wonderland", "Bob The Builder", "Charlie Brown", "Diana Prince", "Evan Almighty", "Fiona Gallagher", "George Jetson", "Hannah Montana", "Ian Malcolm", "Jane Doe", "Kevin McCallister", "Laura Palmer", "Michael Scott", "Nancy Drew", "Oscar Grouch"],
        "registration_date_str": ["2022-01-15", "2022-03-22", "2022-05-10", "2022-07-01", "2022-08-19", "2023-01-20", None, "2023-04-05", "2023-06-12", "2023-07-21", "2023-09-01", "2023-10-15", "2024-02-10", "03/15/2024", "2024-05-01"],
        "city": ["New York", "London", "Paris", "New York", "London", "New York", "Paris", "Berlin", "London", "New York", "Chicago", "Twin Peaks", "Scranton", "River Heights", "New York"],
        "age": [28, 35, 45, 3000, 42, 29, 50, 22, 55, None, 12, 17, 48, 18, 60]
    }).with_columns(pl.col("age").cast(pl.Int64, strict=False)) # Cast age to Int64, allowing nulls

    orders_df = pl.DataFrame({
        "order_id": [201, 202, 203, 204, 205, 206, 207, 208, 209, 210, 211, 212, 213, 214, 215, 216, 217, 218],
        "customer_id": [101, 102, 101, 103, 104, 102, 105, 101, 106, 108, 103, 107, 110, 111, 102, 113, 101, 115],
        "order_date_str": ["2023-01-20 10:30:00", "2023-02-15 11:05:30", "2023-02-28 14:12:55", "2023-03-10 09:00:15", "2023-03-12 17:45:00", "2023-04-05 12:00:00", "2023-04-22 16:20:30", "2023-05-01 10:00:00", "2023-05-15 08:55:10", "2023-06-01 11:30:00", "2023-06-20 13:40:00", "2023-07-01 00:00:00", "2023-07-25 10:10:10", "2023-09-10 14:20:30", "2023-11-05 19:00:00", "2024-02-15 09:30:00", "2024-03-01 10:00:00", "2024-05-05 12:12:12"],
        "product_category": ["Books", "Tools", "Electronics", "Home Goods", "Antiques", "Books", "Electronics", "Books", "Beauty", "Music", "Home Goods", "Electronics", "Clothing", "Toys", "Tools", "Office Supplies", "Electronics", "Home Goods"],
        "quantity": [2, 1, 1, 3, 1, 1, None, 3, 2, 5, 1, 1, 2, 3, 1, 10, 1, 1],
        "unit_price": [15.99, 199.50, 799.00, 25.00, 2500.00, 12.50, 99.99, 10.00, 45.75, 9.99, 150.00, 499.50, 75.00, 29.99, 75.00, 4.99, 1200.00, 12.00],
        "discount_applied": [0.05, 0.10, None, 0.0, 0.15, 0.0, 0.05, None, 0.10, 0.0, 0.05, 0.10, None, 0.05, 0.0, 0.0, 0.15, 0.0]
    }).with_columns([
        pl.col("quantity").cast(pl.Int64, strict=False), # Ensure numeric types, allowing nulls
        pl.col("unit_price").cast(pl.Float64, strict=False),
        pl.col("discount_applied").cast(pl.Float64, strict=False)
    ])
    ```


**Transforming Data - Selection, Filtering, and Modification**

This module covers the primary operations for transforming data: selecting specific columns, creating or modifying columns, filtering rows, changing data types, handling missing values, and sorting data. These operations are fundamental to preparing data for analysis.

## **1. Introduction to Data Transformation Contexts**

As previewed in Module 4, Polars expressions are executed within specific DataFrame methods (contexts):

  * `select()`: For choosing, renaming, or deriving a specific set of columns.
  * `with_columns()`: For adding new columns or modifying existing ones, keeping all other columns.
  * `filter()`: For selecting a subset of rows based on conditions.

## **2. Column Selection and Manipulation with `select`**

The `select()` method is used when you want to create a new DataFrame with a specific subset or transformation of columns from an existing DataFrame.

  * **Selecting Specific Columns:**
    Provide a list of column names (as strings) or `pl.col()` expressions.

    ```python
    # Select only name and city from customers_df
    customer_locations_df = customers_df.select([
        pl.col("name"),
        pl.col("city")
    ])
    print("Selected customer names and cities:")
    print(customer_locations_df.head())
    ```

  * **Renaming Columns using `alias()`:**
    The `.alias()` method is used within an expression to rename a column in the output.

    ```python
    # Select customer_id and rename it to 'ID', select name and rename to 'Customer Name'
    renamed_customers_df = customers_df.select([
        pl.col("customer_id").alias("ID"),
        pl.col("name").alias("Customer Name")
    ])
    print("\nCustomers DataFrame with renamed columns:")
    print(renamed_customers_df.head())
    ```

  * **Using Polars Selectors:**
    Polars offers powerful column selectors via the `cs` module (imported as `import polars.selectors as cs`). These allow selection based on patterns or data types.

    ```python
    # Select all string columns from orders_df
    string_cols_orders_df = orders_df.select(cs.string())
    print("\nString columns from orders_df:")
    print(string_cols_orders_df.head())

    # Select all numeric columns from customers_df
    numeric_cols_customers_df = customers_df.select(cs.numeric())
    print("\nNumeric columns from customers_df:")
    print(numeric_cols_customers_df.head())

    # Select columns starting with 'order'
    order_related_cols_df = orders_df.select(cs.starts_with("order_"))
    print("\nColumns starting with 'order_' from orders_df:")
    print(order_related_cols_df.head())
    ```

    Selectors are very useful for DataFrames with many columns.

## **3. Adding or Modifying Columns with `with_columns`**

When you want to add new columns or change existing ones while keeping all other columns, `with_columns()` is the appropriate method. It takes a list of expressions.

  * **Creating New Columns:**
    Define an expression and assign it a name using `.alias()`.

    ```python
    # Add a 'total_price' column to orders_df (quantity * unit_price)
    # Also, add a column indicating if a discount was applied.
    orders_enhanced_df = orders_df.with_columns([
        (pl.col("quantity") * pl.col("unit_price")).alias("total_price"),
        pl.col("discount_applied").is_not_null().alias("has_discount") # True if discount_applied is not null
    ])
    print("\nOrders DataFrame with 'total_price' and 'has_discount' columns:")
    print(orders_enhanced_df.head())
    ```

  * **Modifying Existing Columns:**
    If an expression produces a column with an existing name, it replaces that column.

    ```python
    # Convert customer names to uppercase
    customers_upper_name_df = customers_df.with_columns(
        pl.col("name").str.to_uppercase().alias("name") # .alias("name") ensures it replaces the 'name' column
    )
    # Alternatively, if alias matches existing name, it overwrites:
    # customers_upper_name_df = customers_df.with_columns(
    #     pl.col("name").str.to_uppercase()
    # )
    print("\nCustomers DataFrame with names in uppercase:")
    print(customers_upper_name_df.head())
    ```

## **4. Filtering Rows with `filter`**

The `filter()` method is used to select rows that meet certain criteria, defined by one or more boolean expressions.

  * **Filtering Based on Single Conditions:**

    ```python
    # Filter orders for 'Electronics' category
    electronics_orders_df = orders_df.filter(
        pl.col("product_category") == pl.lit("Electronics")
    )
    print("\nElectronics orders:")
    print(electronics_orders_df.head())

    # Filter customers older than 50 (excluding Diana Prince due to outlier age)
    # First, let's handle the outlier age for a more realistic filter example
    customers_cleaned_age_df = customers_df.with_columns(
        pl.when(pl.col("age") > 100).then(None).otherwise(pl.col("age")).alias("age_cleaned")
    )
    older_customers_df = customers_cleaned_age_df.filter(
        pl.col("age_cleaned") > pl.lit(50)
    )
    print("\nCustomers older than 50 (age cleaned):")
    print(older_customers_df.select(["name", "age_cleaned", "city"]))
    ```

  * **Combining Conditions using `&` (AND) and `|` (OR):**
    Wrap individual conditions in parentheses when combining them.

    ```python
    # Filter orders for 'Books' category with quantity > 1
    specific_book_orders_df = orders_df.filter(
        (pl.col("product_category") == pl.lit("Books")) & (pl.col("quantity") > pl.lit(1))
    )
    print("\nBook orders with quantity > 1:")
    print(specific_book_orders_df)

    # Filter customers from 'New York' OR 'Paris'
    ny_paris_customers_df = customers_df.filter(
        (pl.col("city") == pl.lit("New York")) | (pl.col("city") == pl.lit("Paris"))
    )
    print("\nCustomers from New York or Paris:")
    print(ny_paris_customers_df.select(["name", "city"]).head())
    ```

  * **Using `is_in()` for Multiple Values:**
    To filter rows where a column's value is one of several specified values.

    ```python
    # Filter customers from 'London', 'Berlin', or 'Chicago'
    selected_cities = ["London", "Berlin", "Chicago"]
    customers_from_selected_cities_df = customers_df.filter(
        pl.col("city").is_in(selected_cities)
    )
    print(f"\nCustomers from {selected_cities}:")
    print(customers_from_selected_cities_df.select(["name", "city"]))
    ```

## **5. Data Type Conversion (Casting)**

Often, data is not in the correct type (e.g., numbers read as strings, dates as strings). Use `pl.col().cast(DataType)` within `with_columns` to convert types.

  * **Common Numeric and Boolean Types:**

    ```python
    # Example: Suppose 'customer_id' was read as string and we want it as integer
    # For our placeholder, it's already int. Let's imagine converting age to Float for some reason.
    customers_age_float_df = customers_df.with_columns(
        pl.col("age").cast(pl.Float64).alias("age_float")
    )
    print("\nCustomers DataFrame with age as Float64:")
    print(customers_age_float_df.select(["name", "age_float"]).head())
    ```

  * **String to `pl.Categorical`:**
    Useful for columns with a limited number of unique string values. Can improve performance and memory usage.

    ```python
    customers_city_categorical_df = customers_df.with_columns(
        pl.col("city").cast(pl.Categorical).alias("city_categorical")
    )
    print("\nCustomers DataFrame with city as Categorical:")
    print(customers_city_categorical_df.select(["name", "city_categorical"]).head())
    print(f"Data type of 'city_categorical': {customers_city_categorical_df.get_column('city_categorical').dtype}")
    ```

  * **String to `pl.Enum` (for fixed, known categories):**
    Enums are stricter than Categoricals. You define the set of allowed values. This is useful for data validation.

    ```python
    # Define the allowed product categories for an Enum
    # Let's say these are the only valid primary categories we expect for a report
    allowed_categories = ["Books", "Electronics", "Home Goods", "Tools", "Clothing"]
    ProductEnum = pl.Enum(allowed_categories)

    # Attempt to cast 'product_category' to this Enum.
    # Values not in ProductEnum will become null if strict=False (default), or error if strict=True in cast.
    # For demonstration, let's use with_columns and handle potential nulls or filter them.
    orders_enum_df = orders_df.with_columns(
        pl.col("product_category").cast(ProductEnum, strict=False).alias("category_enum")
    )
    print("\nOrders DataFrame with product_category as Enum (non-matching become null):")
    print(orders_enum_df.select(["product_category", "category_enum"]).head(10))
    print(f"Data type of 'category_enum': {orders_enum_df.get_column('category_enum').dtype}")
    # You might filter out nulls if they represent unexpected categories:
    # valid_category_orders = orders_enum_df.filter(pl.col("category_enum").is_not_null())
    ```

  * **String to Datetime/Date:**
    Polars provides powerful string parsing capabilities.

      * **Standard Format (`.str.to_datetime()`):** For `orders_df.order_date_str` which is in `YYYY-MM-DD HH:MM:SS`.

        ```python
        orders_parsed_dates_df = orders_df.with_columns(
            pl.col("order_date_str").str.to_datetime(format="%Y-%m-%d %H:%M:%S").alias("order_datetime")
        )
        print("\nOrders DataFrame with parsed order_datetime:")
        print(orders_parsed_dates_df.select(["order_id", "order_datetime"]).head())
        print(f"Data type of 'order_datetime': {orders_parsed_dates_df.get_column('order_datetime').dtype}")
        ```

      * **Handling Mixed Date Formats (`.str.to_date()` and `pl.coalesce()`):**
        The `customers_df.registration_date_str` has mixed formats (`YYYY-MM-DD` and `MM/DD/YYYY`) and nulls. We can try parsing with multiple formats and use `pl.coalesce` to pick the first successful parse.

        ```python
        customers_parsed_reg_dates_df = customers_df.with_columns(
            pl.coalesce([
                pl.col("registration_date_str").str.to_date(format="%Y-%m-%d", strict=False),
                pl.col("registration_date_str").str.to_date(format="%m/%d/%Y", strict=False)
            ]).alias("registration_date")
        )
        print("\nCustomers DataFrame with parsed registration_date (handling mixed formats):")
        print(customers_parsed_reg_dates_df.select(["name", "registration_date_str", "registration_date"]))
        print(f"Data type of 'registration_date': {customers_parsed_reg_dates_df.get_column('registration_date').dtype}")
        ```

        `strict=False` allows parsing to return `null` on failure for a given format, letting `coalesce` try the next one.

## **6. Handling Missing Values**

  * **Filling Nulls with `fill_null()`:**
    You can fill missing values using a literal value or a defined strategy.

    ```python
    # Fill missing 'age' in customers_df with the mean age (after cleaning the outlier)
    # For simplicity here, let's first ensure 'age_cleaned' exists as from filter example
    if "age_cleaned" not in customers_cleaned_age_df.columns:
         customers_cleaned_age_df = customers_cleaned_age_df.with_columns(
            pl.when(pl.col("age") > 100).then(None).otherwise(pl.col("age")).cast(pl.Float64).alias("age_cleaned") # Cast to float for mean
        )

    mean_age_val = customers_cleaned_age_df.select(pl.col("age_cleaned").mean()).item() # Get the actual mean value

    customers_filled_age_df = customers_cleaned_age_df.with_columns(
        pl.col("age_cleaned").fill_null(mean_age_val).alias("age_filled_mean")
    )
    # Alternative using strategy string (Polars will compute the mean internally)
    # customers_filled_age_df = customers_cleaned_age_df.with_columns(
    #     pl.col("age_cleaned").fill_null(strategy="mean").alias("age_filled_mean_strategy")
    # )
    print(f"\nCustomers DataFrame with 'age_cleaned' nulls filled with mean ({mean_age_val:.2f}):")
    print(customers_filled_age_df.filter(customers_df["age"].is_null()).select(["name", "age", "age_cleaned", "age_filled_mean"]))


    # Fill missing 'discount_applied' in orders_df with 0
    orders_filled_discount_df = orders_df.with_columns(
        pl.col("discount_applied").fill_null(0.0).alias("discount_applied_filled")
    )
    print("\nOrders DataFrame with 'discount_applied' nulls filled with 0:")
    print(orders_filled_discount_df.filter(pl.col("discount_applied").is_null()).select(["order_id", "discount_applied", "discount_applied_filled"]).head())
    ```

  * **Dropping Rows with Nulls using `drop_nulls()`:**
    Use with caution as it involves data loss. The `subset` parameter can specify columns to consider for nulls.

    ```python
    # Drop rows from orders_df where 'quantity' is null
    orders_dropped_null_quantity_df = orders_df.drop_nulls(subset=["quantity"])
    print(f"\nOriginal orders count: {orders_df.height}, After dropping null quantity: {orders_dropped_null_quantity_df.height}")

    # Drop rows from customers_df if 'registration_date_str' OR 'age' is null
    # customers_dropped_any_df = customers_df.drop_nulls(subset=["registration_date_str", "age"])
    # print(f"\nOriginal customers count: {customers_df.height}, After dropping if reg_date or age is null: {customers_dropped_any_df.height}")
    ```

    *Business Context:* Discuss implications: Dropping an order because quantity is missing might be acceptable if quantity is essential for all analyses. Dropping a customer due to a missing registration date might be undesirable if other customer information is valuable.

## **7. Sorting Data with `sort()`**

The `sort()` method orders the DataFrame by one or more columns.

```python
# Sort customers_df by age in descending order
sorted_customers_by_age_df = customers_df.sort("age", descending=True)
print("\nCustomers sorted by age (descending):")
print(sorted_customers_by_age_df.select(["name", "age", "city"]).head())

# Sort orders_df by 'product_category' (ascending) then by 'total_price' (descending)
# We need 'total_price' from a previous example
if "total_price" not in orders_enhanced_df.columns: # ensure it exists
    orders_enhanced_df = orders_enhanced_df.with_columns(
            (pl.col("quantity") * pl.col("unit_price")).alias("total_price")
    )

sorted_orders_df = orders_enhanced_df.sort(
    by=["product_category", "total_price"],
    descending=[False, True] # False for product_category (asc), True for total_price (desc)
)
print("\nOrders sorted by product_category (asc) then total_price (desc):")
print(sorted_orders_df.select(["product_category", "order_id", "total_price"]).head(10))
```
