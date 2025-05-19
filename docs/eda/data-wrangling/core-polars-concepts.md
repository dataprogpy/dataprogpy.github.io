--- 
icon: material/numeric-3
---
# Core Polars Concepts

Great, let's proceed to **Module 4: Core Polars Concepts - The Building Blocks**.

This module delves into the fundamental components of Polars: DataFrames, Series, and most importantly, Expressions. A solid understanding of these building blocks is essential for leveraging the full power and expressiveness of the Polars library for data manipulation.

-----

**Module 4: Core Polars Concepts - The Building Blocks (Approx. 25-30 mins)**

Having learned how to load and perform initial inspections on your data, we now focus on the core data structures and the expression API that form the foundation of Polars.

```python
# Prerequisite: Ensure Polars is imported.
import polars as pl

# We will use customers_df and orders_df from previous modules.
# For stand-alone execution of this module's examples,
# ensure these DataFrames are loaded or use placeholders.
try:
    _ = customers_df.shape # Check if exists
except NameError:
    print("Placeholder: customers_df not loaded. Creating a minimal example.")
    customers_df = pl.DataFrame({
        "customer_id": [101, 102], "name": ["Alice Wonderland", "Bob The Builder"],
        "city": ["New York", "London"], "age": [28, 35]
    })

try:
    _ = orders_df.shape # Check if exists
except NameError:
    print("Placeholder: orders_df not loaded. Creating a minimal example.")
    orders_df = pl.DataFrame({
        "order_id": [201, 202], "customer_id": [101, 102],
        "product_category": ["Books", "Tools"], "quantity": [2, 1],
        "unit_price": [15.99, 199.50]
    })
```

**1. Polars DataFrame Revisited**

You've already been working with Polars DataFrames (e.g., `customers_df`, `orders_df`) since Module 2. Let's formally define it:

  * **Definition:** A Polars DataFrame is a 2-dimensional, in-memory, tabular data structure. It consists of an ordered collection of named columns, where each column can have a different data type (e.g., integer, string, float). Think of it as a more powerful version of a spreadsheet or an SQL table within your Python environment.

  * **Creation:** While DataFrames are commonly created by reading files, you can also construct them directly, for instance, from a Python dictionary where keys are column names and values are lists (or Polars Series) representing the column data.

    ```python
    # Example: Creating a DataFrame from scratch
    data_dict = {
        'StudentID': [1001, 1002, 1003],
        'Course': ['Finance', 'Marketing', 'Finance'],
        'MidtermScore': [85, 92, 78]
    }
    scores_df = pl.DataFrame(data_dict)
    print("DataFrame created from scratch:")
    print(scores_df)
    ```

**2. Polars Series**

  * **Definition:** A Polars Series is a 1-dimensional array-like object that represents a single column within a DataFrame. Every column in a DataFrame is, in fact, a Series. A Series has a name (usually inherited from its column name) and a single data type for all its elements.

  * **Accessing a Series:** You can extract a column as a Series from a DataFrame.

      * Using `DataFrame.get_column("column_name")`: This method directly returns the specified column as a Series.
      * Using `DataFrame["column_name"]`: This is a common shorthand to select a column, returning a Series.

    <!-- end list -->

    ```python
    # Extracting the 'city' column from customers_df as a Series
    city_series = customers_df.get_column('city')
    print("\n'city' column extracted as a Series:")
    print(city_series)
    print(f"Type of city_series: {type(city_series)}")
    print(f"Name of city_series: {city_series.name}")
    print(f"DataType of city_series: {city_series.dtype}")

    # Alternative shorthand (produces the same Series)
    # age_series = customers_df['age']
    # print("\n'age' column extracted as a Series (shorthand):")
    # print(age_series)
    ```

    While `df.select(pl.col('column_name'))` also selects a column, it returns a new single-column DataFrame. To get a Series from it, you'd use `df.select(pl.col('column_name')).to_series()`. Understanding Series is important as many expressions operate on them implicitly.

**3. Polars Expressions: The Core Engine**

Expressions are the cornerstone of Polars' power and flexibility. They allow you to define computations or transformations on your data in a declarative way.

  * **What are Expressions?** Think of expressions as **blueprints or recipes** for operations you want to perform. They describe *what* you want to do, not *how* to do it step-by-step. Polars' query optimizer can then figure out the most efficient way to execute these blueprints.
  * **Lazy Evaluation (in many contexts):** Expressions themselves are typically not executed immediately. They are evaluated when used within an "execution context" – a DataFrame method like `select()`, `filter()`, or `with_columns()`.

Let's explore the fundamental components for building expressions:

  * **Referring to Columns: `pl.col()`**
    The primary way to refer to one or more columns within an expression is using `pl.col()`.

      * `pl.col("column_name")`: Refers to a single column by its name.
      * `pl.col(["colA", "colB"])`: Refers to multiple specified columns.
      * `pl.col("*")` or `pl.all()`: Refers to all columns. (We used `pl.all()` in Module 3).
      * You can also use regular expressions or data types with `pl.col()` to select columns.

    <!-- end list -->

    ```python
    # An expression referring to the 'age' column in customers_df
    age_expression = pl.col("age")
    print(f"\nA Polars expression for the 'age' column: {age_expression}")
    # Note: Printing the expression itself just shows its structure, it doesn't compute anything yet.
    ```

  * **Literal Values: `pl.lit()`**
    When you want to use a constant value (like a number, string, or boolean) within an expression, you should wrap it with `pl.lit()`. This tells Polars that the value is a literal, not a column name or another expression.

      * `pl.lit(value)`: Creates a literal expression from the given value.

    <!-- end list -->

    ```python
    # An expression representing the literal numeric value 10
    literal_ten_expr = pl.lit(10)
    print(f"A Polars expression for the literal value 10: {literal_ten_expr}")

    # An expression representing a literal string
    literal_string_expr = pl.lit("Approved")
    print(f"A Polars expression for a literal string: {literal_string_expr}")
    ```

  * **Basic Operations with Expressions:**
    Expressions can be combined using standard arithmetic, comparison, and logical operators to define more complex computations.

    ```python
    # Example 1: Arithmetic operation - Calculate discounted price
    # Assumes 'unit_price' and a hypothetical 'discount_rate' column (or use a literal for discount)
    # Let's use orders_df and assume we want to apply a fixed 0.05 discount if not specified
    # For illustration, let's define an expression for 'unit_price' * (1 - 0.05)
    original_price_expr = pl.col("unit_price")
    discount_rate_lit_expr = pl.lit(0.05) # 5% discount
    discounted_price_expr = original_price_expr * (pl.lit(1) - discount_rate_lit_expr)
    print(f"\nExpression for discounted price: {discounted_price_expr}")

    # Example 2: Comparison operation - Check if age is greater than 30
    age_col_expr = pl.col("age")
    threshold_age_lit_expr = pl.lit(30)
    is_older_than_30_expr = age_col_expr > threshold_age_lit_expr
    print(f"Expression for 'age > 30': {is_older_than_30_expr}")
    ```

  * **Expressions are Executed in Contexts (A Preview for Module 5):**
    As mentioned, these expressions are just plans. To see their effect, you must use them within a DataFrame method that provides an execution context. The `select()` method is one such context, allowing you to choose which columns or derived expressions to include in a new DataFrame.

    ```python
    # Illustrating expression execution with select() using orders_df
    # Let's select the product_category, unit_price, and our calculated discounted_price_expr
    print("\nExecuting expressions using orders_df.select():")
    selected_orders_info = orders_df.select([
        pl.col("product_category"),
        original_price_expr.alias("Original Price"), # original_price_expr is pl.col("unit_price")
        discounted_price_expr.alias("Calculated Discounted Price (5%)")
    ])
    print(selected_orders_info.head(3))

    # Illustrating a comparison expression with customers_df
    print("\nExecuting comparison expression using customers_df.select():")
    customer_age_check = customers_df.select([
        pl.col("name"),
        pl.col("age"),
        is_older_than_30_expr.alias("Older Than 30")
    ])
    print(customer_age_check.head(3))
    ```

    The `.alias("new_name")` method is used here to give a name to the new column generated by the expression.

**4. Recap and Importance**

  * **DataFrame:** Your primary multi-columnar data container.
  * **Series:** Represents a single column within a DataFrame.
  * **Expressions (using `pl.col()`, `pl.lit()`, and operators):** The fundamental way you define data transformations and computations in Polars.

Mastering the concept of expressions is crucial, as they form the basis for almost all data manipulation tasks you will perform in Polars, from simple selections and modifications to complex aggregations. Module 5 will heavily expand on the contexts where these expressions are applied.

-----

This structure for Module 4 formally defines DataFrames and Series, and then dedicates significant attention to introducing `pl.col()` and `pl.lit()` as the building blocks for expressions, along with basic operations and a preview of execution contexts like `select()`. This should provide a solid foundation for the students.

Are you happy with this for Module 4?

---
You're asking for a very insightful addition. Showing how expressions behave in different execution contexts, even as a preview, will significantly help students grasp the versatility and central role of the expression API in Polars. It beautifully sets the stage for Module 5 and 6.

Let's expand the "Expressions are Executed in Contexts (A Preview for Module 5)" subsection in Module 4 to include these illustrative examples.

-----

**Module 4: Core Polars Concepts - The Building Blocks (Approx. 25-30 mins)**

Having learned how to load and perform initial inspections on your data, we now focus on the core data structures and the expression API that form the foundation of Polars.

```python
# Prerequisite: Ensure Polars is imported.
import polars as pl

# We will use customers_df and orders_df from previous modules.
# For stand-alone execution of this module's examples,
# ensure these DataFrames are loaded or use placeholders.
try:
    _ = customers_df.shape # Check if exists
except NameError:
    print("Placeholder: customers_df not loaded. Creating a minimal example.")
    customers_df = pl.DataFrame({
        "customer_id": [101, 102, 103, 104],
        "name": ["Alice Wonderland", "Bob The Builder", "Charlie Brown", "Diana Prince"],
        "city": ["New York", "London", "Paris", "New York"],
        "age": [28, 35, 45, 28] # Added more variety for filtering
    })

try:
    _ = orders_df.shape # Check if exists
except NameError:
    print("Placeholder: orders_df not loaded. Creating a slightly more diverse example for context demonstration.")
    orders_df = pl.DataFrame({
        "order_id": [201, 202, 203, 204, 205, 206],
        "customer_id": [101, 102, 101, 103, 102, 104],
        "product_category": ["Books", "Tools", "Books", "Home Goods", "Books", "Electronics"],
        "quantity": [2, 1, 3, 1, 1, 2],
        "unit_price": [15.99, 199.50, 10.00, 25.00, 12.00, 79.99]
    })
```

**(Previous sections of Module 4: DataFrame, Series, Expressions, pl.col(), pl.lit(), Basic Operations with Expressions - remain as previously defined)**

  * **Expressions are Executed in Contexts (A Preview for Modules 5 & 6):**
    As emphasized, expressions are plans. They come to life when used within a DataFrame method that provides an "execution context." Different contexts use expressions for different purposes. Let's briefly preview some common contexts with simple examples. Modules 5 and 6 will cover these in detail.

      * **`select` Context:** Used to choose, derive, or rename columns, creating a new DataFrame with only the specified columns. Expressions in `select` typically define what data each column in the new DataFrame will contain.

        ```python
        # Example: Selecting customer name and deriving a 'is_minor' status
        print("\nPreview: Expression in `select` context")
        customer_status_df = customers_df.select([
            pl.col("name"),
            pl.col("age"),
            (pl.col("age") < pl.lit(18)).alias("is_minor")
        ])
        print(customer_status_df.head())
        ```

        *Observation:* The expression `(pl.col("age") < pl.lit(18))` creates a new boolean column.

      * **`with_columns` Context:** Used to add new columns or modify existing ones in a DataFrame. The DataFrame returned will contain all original columns plus any new or altered ones defined by the expressions.

        ```python
        # Example: Adding a 'total_price' column to orders_df (quantity * unit_price)
        print("\nPreview: Expression in `with_columns` context")
        orders_with_total = orders_df.with_columns(
            (pl.col("quantity") * pl.col("unit_price")).alias("total_price")
        )
        print(orders_with_total.head())
        ```

        *Observation:* The `total_price` column is added to the existing columns of `orders_df`.

      * **`filter` Context:** Used to select a subset of rows from a DataFrame based on one or more conditions. Expressions in `filter` must evaluate to a boolean (True/False) value for each row. Rows where the expression evaluates to `True` are kept.

        ```python
        # Example: Filtering customers who are older than 30
        print("\nPreview: Expression in `filter` context")
        adult_customers_df = customers_df.filter(
            pl.col("age") > pl.lit(30)
        )
        print(adult_customers_df)
        ```

        *Observation:* The expression `pl.col("age") > pl.lit(30)` determines which rows are included in the result.

      * **`group_by().agg()` Context:** Used for grouping rows that have the same values in specified columns and then performing aggregate calculations (like sum, mean, count) on each group. Expressions within `.agg()` define these aggregate calculations.

        ```python
        # Example: Calculating total quantity sold per product_category
        print("\nPreview: Expression in `group_by().agg()` context")
        sales_by_category = orders_df.group_by("product_category").agg([
            pl.sum("quantity").alias("total_quantity_sold"), # Aggregates the 'quantity' column
            pl.mean("unit_price").alias("average_unit_price")
        ])
        # You can also write pl.col("quantity").sum().alias(...)
        print(sales_by_category)
        ```

        *Observation:* The `pl.sum("quantity")` expression calculates a sum for each `product_category` group.

    These examples illustrate that the same fundamental building blocks (`pl.col()`, `pl.lit()`, operators) are used to construct expressions, but their outcome is determined by the context in which they are executed.

**4. Recap and Importance**

  * **DataFrame:** Your primary multi-columnar data container.
  * **Series:** Represents a single column within a DataFrame.
  * **Expressions (using `pl.col()`, `pl.lit()`, and operators):** The fundamental way you define data transformations and computations in Polars. Their behavior is realized within execution contexts like `select`, `with_columns`, `filter`, and `group_by().agg()`.

Mastering the concept of expressions and how they interact with different execution contexts is crucial, as they form the basis for almost all data manipulation tasks you will perform in Polars. Module 5 will heavily expand on the `select`, `with_columns`, and `filter` contexts.

-----

This expanded subsection should give students a much clearer initial idea of how versatile expressions are and how their role changes depending on the DataFrame method (context) they are used in. This nicely bridges to the more detailed discussions in subsequent modules.

Does this enhancement for Module 4 align with your vision?