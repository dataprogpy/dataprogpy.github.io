--- 
icon: material/numeric-2
---
# Data Exploration


## **Getting Data In - Data IO**

The ability to import data from various sources is often the first step in data analysis. Polars provides efficient functions to read data from common file types directly into its primary data structure, the DataFrame.

**1. Loading Data into DataFrames**

Once data is read by Polars, it is typically stored in a DataFrame. A DataFrame is a two-dimensional, labeled data structure, analogous to a table in a database or a sheet in a spreadsheet program. You will assign the loaded data to a variable to work with it.

**2. Reading Comma-Separated Values (CSV) Files**

CSV files are one of the most common formats for storing and exchanging tabular data. Each line in a CSV file typically represents a row of data, with values separated by a delimiter, most often a comma.

  * **The `pl.read_csv()` Function:**
    The primary function for reading CSV files in Polars is `pl.read_csv()`. Its most basic usage requires the path to the file.

  * **Example: Loading Local CSV Files**
    We will use the [`customers.csv`](../../assets/data/customers.csv){:target="_blank"} and [`orders.csv`](../../assets/data/orders.csv){:target="_blank"}  files provided for this course. Assuming these files are in your current working directory (the same directory where your Python script or notebook is running), you can load them as follows:

    ```python
    import polars as pl

    # Load the customers dataset
    customers_df = pl.read_csv('customers.csv')

    # Load the orders dataset
    orders_df = pl.read_csv('orders.csv')

    # You can then display the first few rows to verify
    print(customers_df.head())
    print(orders_df.head())
    ```

  * **Common Parameters for `pl.read_csv()`:**

      * `source`: The first argument, which is the file path or URL.
      * `separator` (or `sep`): Specifies the delimiter used in the file. While the default is a comma (`,`), you might encounter files using semicolons (`;`), tabs (`\t`), or other characters. For example: `pl.read_csv('data.tsv', separator='\t')`.
      * `has_header`: A boolean indicating whether the first row of the CSV contains column names. This defaults to `True`. If your file does not have a header row, you would set `has_header=False`.
      * `dtypes`: Used to specify or override the data types for columns. (More on this in the next section).
      * `try_parse_dates`: If set to `True`, Polars will attempt to automatically parse columns that look like dates into `Date` or `Datetime` types. This can be convenient but requires caution if date formats are inconsistent.
      * There are many other parameters for handling various CSV complexities (e.g.,  `null_values` for defining strings that should be interpreted as nulls), which you can explore in the Polars documentation as needed.

  * **Specifying or Overriding Data Types (Schema) with `dtypes`**

    ??? note "What is a schema and why should you care?"

        In the context of data science and business analytics, a **schema** refers to the formal definition or blueprint of a dataset's structure. Think of it as the architectural plan for your data, outlining what the data looks like and how it's organized.

        Specifically, a schema typically defines:

        1.  **Column Names:** The labels or identifiers for each piece of information in the dataset (e.g., "CustomerID", "ProductName", "TransactionDate", "SalesAmount").
        2.  **Data Types:** The nature of the data stored in each column. Common data types include:
            * **Integers:** Whole numbers (e.g., for counts like `QuantityOrdered`).
            * **Floating-Point Numbers (Decimals):** Numbers with decimal points (e.g., for `UnitPrice` or `Revenue`).
            * **Strings (Text):** Alphanumeric characters (e.g., for `CustomerName` or `ProductDescription`).
            * **Booleans:** True or False values (e.g., for `IsActiveCustomer`).
            * **Dates and Timestamps:** Specific points in time (e.g., for `OrderDate` or `RegistrationTimestamp`).
            * **Categorical/Enum:** A fixed set of predefined values (e.g., `SalesRegion` being "North," "South," "East," or "West").
        3.  **Relationships (in relational databases):** How different datasets or tables link together (e.g., how a `CustomerID` in an orders table relates to a `CustomerID` in a customer details table).
        4.  **Constraints (sometimes):** Rules applied to the data, such as whether a value can be missing (null), if values must be unique (like an `OrderID`), or if they must fall within a specific range.

        **Why is Understanding and Defining a Schema Important in a Business Context?**

        A clear and accurate schema is not merely a technical detail; it is fundamental to effective data utilization and decision-making in business for several critical reasons:

        1.  **Ensuring Data Integrity and Quality:**
            * A schema acts as a first line of defense for data quality. By defining that `SalesAmount` must be a numerical value, any attempt to input text (e.g., "One Thousand Dollars") would be flagged as an error or an inconsistency that needs addressing. This prevents the "garbage in, garbage out" problem, leading to more reliable data.
            * It helps identify data collection or entry errors early. If a `TransactionDate` column, defined as a date type, contains values that cannot be interpreted as valid dates, it signals a problem in the data pipeline.

        2.  **Enabling Accurate Analysis and Reporting:**
            * Correct data types are essential for any meaningful computation. For example, you cannot accurately calculate total revenue if the `SalesAmount` column is mistakenly treated as text. Trend analysis based on dates requires the `OrderDate` column to be a proper date type, not just a string of characters.
            * It ensures that analytical operations like sums, averages, minimums, maximums, and comparisons yield correct and trustworthy results, which are the bedrock of informed business decisions.

        3.  **Facilitating Effective Data Integration:**
            * Businesses often need to combine data from disparate sources (e.g., CRM systems, sales databases, marketing analytics platforms). A clear understanding of the schema for each source is vital to correctly join or merge these datasets. Mismatched data types or misinterpretation of column meanings (e.g., joining on an ID that has different formats in two tables) can lead to failed integrations or, worse, subtly incorrect combined datasets.

        4.  **Supporting System Compatibility and Interoperability:**
            * Data frequently moves between different software systems—from operational databases to data warehouses, analytics tools (like Polars), and business intelligence dashboards. A well-defined schema ensures that data is interpreted consistently across these platforms, maintaining its meaning and usability.
            * For regulatory reporting or data exchange with partners, adherence to a predefined schema is often a requirement.

        5.  **Improving Efficiency in Data Processing:**
            * When data processing tools and databases are aware of the schema, they can optimize data storage and query execution. For example, operations on numerical data are generally faster and more memory-efficient than on text data if the system knows the type in advance.
            * When loading data, explicitly defining the schema can significantly speed up the import process and reduce memory consumption, as the system doesn't need to infer data types.

        6.  **Promoting Clear Communication and Collaboration:**
            * The schema serves as a common language and a point of reference for all stakeholders involved with the data—from data engineers and analysts to business managers. It provides clear documentation on what each data field represents and its expected format.

        7.  **Forming a Basis for Data Governance:**
            * Schema definitions are integral to data governance frameworks. These frameworks establish policies for data quality, security, and compliance. A well-understood schema helps enforce these policies and manage data as a valuable organizational asset.

        In essence, the schema provides the necessary structure and rules that allow businesses to transform raw data into reliable information, which in turn supports insightful analysis and strategic decision-making. Neglecting the schema can lead to significant errors, inefficiencies, and ultimately, flawed business intelligence.

    While Polars does a good job of inferring data types automatically, there are several reasons why you might want to explicitly define the schema when reading a CSV:

      * **Correctness:** To ensure columns are interpreted as the intended data type (e.g., an ID column that looks numeric but should be treated as a string, or ensuring a specific precision for floating-point numbers).
      * **Performance:** Specifying types can sometimes speed up parsing, especially for very large files, as Polars doesn't have to guess.
      * **Memory Efficiency:** You can choose more memory-efficient types if appropriate (e.g., `pl.Int32` instead of `pl.Int64` if the numbers in a column are known to be small enough).
      * **Handling Ambiguity:** For columns with mixed-format dates or numbers that could be misinterpreted, specifying the type (often as `pl.Utf8` initially, for later custom parsing) provides control.

    The `dtypes` parameter in `pl.read_csv()` accepts a dictionary where keys are column names and values are Polars data types.

    **Example: Specifying `dtypes` for `customers.csv`**

    Let's load `customers.csv` ([original file: `customers.csv`]) again, this time specifying data types for some columns:

    ```python
    import polars as pl

    # Define the desired schema for selected columns
    customer_schema = {
        'customer_id': pl.Int32,        # Specify as 32-bit integer
        'name': pl.Utf8,                # Explicitly Utf8 (string)
        'registration_date': pl.Utf8,   # Read as string; parsing will be handled later due to mixed formats
        'city': pl.Categorical,         # Use Categorical for columns with repetitive string values
        'age': pl.Float32               # Use Float32 for age (e.g., if non-integer ages were possible or for consistency)
    }

    # Load the customers dataset with the specified schema
    customers_custom_schema_df = pl.read_csv('customers.csv', dtypes=customer_schema)

    print(customers_custom_schema_df.dtypes)
    print(customers_custom_schema_df.head())
    ```

    **Common Polars Data Types for Schema Definition:**

      * Integers: `pl.Int8`, `pl.Int16`, `pl.Int32`, `pl.Int64` (default for integers)
      * Unsigned Integers: `pl.UInt8`, `pl.UInt16`, `pl.UInt32`, `pl.UInt64`
      * Floats: `pl.Float32`, `pl.Float64` (default for floats)
      * Strings: `pl.Utf8`
      * Booleans: `pl.Boolean`
      * Dates/Times: `pl.Date`, `pl.Datetime`, `pl.Duration`, `pl.Time`
      * Categorical: `pl.Enum`, `pl.Categorical` (for columns with a limited set of unique string values)

    For a comprehensive list, refer to the official Polars documentation on data types. Note that for complex date/time parsing directly within `read_csv` when formats are inconsistent, it's often more robust to read the column as `pl.Utf8` and then use Polars' specialized string-to-date conversion functions (covered in Module 5). The `try_parse_dates=True` parameter can be an alternative for simpler cases.


**3. Reading CSV Data from a URL**

Polars can also read CSV files directly from a URL, which is convenient for accessing publicly available datasets without needing to download them manually.

  * **Example: Loading a CSV from a URL**
    The `pl.read_csv()` function seamlessly handles URLs.

    ```python
    import polars as pl

    # Example URL for a public CSV file (ensure this URL is active and accessible)
    # This is a sample dataset of an airline's monthly passengers.
    airline_passengers_url = 'https://raw.githubusercontent.com/plotly/datasets/master/airline-passengers.csv'

    # Load data from the URL
    airline_passengers_df = pl.read_csv(airline_passengers_url)

    print(airline_passengers_df.head())
    ```

    *Note for students:* When working with data from URLs, ensure you have an active internet connection. The availability and content of external URLs can change.

**4. Reading JSON Files**

JSON (JavaScript Object Notation) is another widely used format for data interchange, especially for web-based data and APIs. Polars can read JSON files, typically those structured as a list of records (where each record is an object with key-value pairs).

  * **The `pl.read_json()` Function:**
    This function is used to read data from a JSON file. For the common "list of records" structure, Polars can infer the schema effectively.

  * **Example: Loading a JSON File**
    Suppose you have a JSON file named `product_inventory.json` in your working directory with the following content:

    ```json
    [
      {"product_id": "P1001", "product_name": "Laptop X1", "category": "Electronics", "stock_level": 150, "reorder_point": 50},
      {"product_id": "P1002", "product_name": "Wireless Mouse", "category": "Electronics", "stock_level": 300, "reorder_point": 100},
      {"product_id": "P1003", "product_name": "Office Chair Pro", "category": "Furniture", "stock_level": 85, "reorder_point": 30},
      {"product_id": "P1004", "product_name": "Standing Desk", "category": "Furniture", "stock_level": 60, "reorder_point": 20}
    ]
    ```

    To load this file into a Polars DataFrame:

    ```python
    import polars as pl

    # Assuming 'product_inventory.json' with the content above exists in your working directory.
    # If you are following along, you can create this file manually.
    try:
        inventory_df = pl.read_json('product_inventory.json')
        # print(inventory_df.head())
    except FileNotFoundError:
        print("Ensure 'product_inventory.json' exists in your working directory with the sample content.")
    except Exception as e: # Polars might raise a different exception if JSON is malformed or empty
        print(f"An error occurred: {e}")

    ```

    *Note for students:* The `pl.read_json()` function can also read JSON lines format if you specify `json_lines=True`. JSON structures can vary; `pl.read_json()` is optimized for array-of-objects or JSON lines formats. For more complex nested JSON, further parsing might be required after initial loading.

**5. A Note on Other Data Formats**

While CSV and JSON are common, Polars supports reading various other file formats, including:

  * **Parquet (`pl.read_parquet()`):** A columnar storage format popular in Big Data ecosystems, known for efficiency.
  * **Excel (`pl.read_excel()`):** For reading data from `.xlsx` or `.xls` files (often requires an additional engine like `xlsx2csv` or `openpyxl`).
  * **Database connections:** Polars can also read from SQL databases.

These options provide flexibility in accessing data from diverse enterprise sources. For this course, we will primarily focus on CSV and JSON.

## **First Look - Initial Data Exploration**

The data exploration step is where an analyst develops a deeper understanding of the structural characteristics of the ingested data, identifies the sequence of casting, transformation, and cleaning tasks that need to be performed, and implements the identified changes. This section will guide you through the fundamental Polars functions and techniques used to inspect and understand your DataFrames. We will primarily use the `customers_df` and `orders_df` DataFrames. Ensure you have these DataFrames available.

```python
# If you are using a local or non-Colab development environment,
# you may need to run the code below on your notebook or use a 
# dependency manager to install polars to your environment
# 
# !pip install polars
#
# Prerequisite: Ensure Polars is imported and your DataFrames are loaded.
import polars as pl

# Assuming 'customers.csv' and 'orders.csv' are in the current working directory:
customers_df = pl.read_csv('customers.csv')
orders_df = pl.read_csv('orders.csv')
```

??? info "Error handling code"

    If you stored your data files on the session stroage previously and 
    encountered `NameError`, try the code below:

    ```python
    # For the examples below, we'll assume customers_df and orders_df are already loaded.
    # If you are starting a new session, uncomment and run the lines above.
    # To make examples runnable, let's create placeholder DataFrames if they don't exist.
    # In a real notebook, these would be loaded from files as in Module 2.
    try:
        _ = customers_df.shape # Check if exists
    except NameError:
        print("Placeholder: customers_df not loaded. Creating a minimal example.")
        customers_df = pl.DataFrame({
            "customer_id": [101, 102, 107, 110, 104],
            "name": ["Alice Wonderland", "Bob The Builder", "George Jetson", "Jane Doe", "Diana Prince"],
            "registration_date": ["2022-01-15", "2022-03-22", None, "2023-07-21", "2022-07-01"],
            "city": ["New York", "London", "Paris", "New York", "New York"],
            "age": [28, 35, 50, None, 3000]
        }).with_columns(pl.col("age").cast(pl.Int64, strict=False)) # Ensure age is int for describe

    try:
        _ = orders_df.shape # Check if exists
    except NameError:
        print("Placeholder: orders_df not loaded. Creating a minimal example.")
        orders_df = pl.DataFrame({
            "order_id": [201, 202, 203, 207, 208],
            "customer_id": [101, 102, 101, 105, 101],
            "order_date": ["2023-01-20 10:30:00", "2023-02-15 11:05:30", "2023-02-28 14:12:55", "2023-04-22 16:20:30", "2023-05-01 10:00:00"],
            "product_category": ["Books", "Tools", "Electronics", "Electronics", "Books"],
            "quantity": [2, 1, 1, None, 3],
            "unit_price": [15.99, 199.50, 799.00, 99.99, 10.00],
            "discount_applied": [0.05, 0.10, None, 0.05, None]
        }).with_columns([
            pl.col("quantity").cast(pl.Int64, strict=False),
            pl.col("unit_price").cast(pl.Float64, strict=False),
            pl.col("discount_applied").cast(pl.Float64, strict=False)
        ])
    ```

**1. Understanding DataFrame Structure: Dimensions**

The `.shape` attribute remains the direct way to get dimensions.

  * **`.shape` Attribute:**
    Returns a tuple representing the dimensions of the DataFrame: `(number_of_rows, number_of_columns)`.

    ```python
    # Get the dimensions of the customers DataFrame
    customer_dimensions = customers_df.shape
    print(f"Customers DataFrame - Rows: {customer_dimensions[0]}, Columns: {customer_dimensions[1]}")

    # Get the dimensions of the orders DataFrame
    order_dimensions = orders_df.shape
    print(f"Orders DataFrame - Rows: {order_dimensions[0]}, Columns: {order_dimensions[1]}")
    ```

**2. Inspecting Data Content: First and Last Rows**

`.head()` and `.tail()` are direct DataFrame methods for quick data previews.

  * **`.head(n)` Method:**
    Displays the first `n` rows. Defaults to 5.

    ```python
    print("First 3 rows of customers_df:")
    print(customers_df.head(3))
    ```

  * **`.tail(n)` Method:**
    Displays the last `n` rows. Defaults to 5.

    ```python
    print("\nLast 3 rows of orders_df:")
    print(orders_df.tail(3))
    ```

**3. Examining Data Types and Schema**

`.dtypes` and `.schema` are attributes for metadata inspection.

  * **`.dtypes` Attribute:**
    Returns a list of `DataType` for each column.

    ```python
    print("Data types for customers_df:")
    print(customers_df.dtypes)
    ```

  * **`.schema` Attribute:**
    Provides a structured view of column names and their `DataType`.

    ```python
    print("\nSchema for orders_df:")
    print(orders_df.schema)
    ```

**4. Obtaining Descriptive Statistics and a Quick Overview**

`.describe()` and `.glimpse()` are higher-level DataFrame methods for summary.

  * **`.describe()` Method:**
    Computes summary statistics for numerical columns.

    ```python
    print("Descriptive statistics for orders_df:")
    print(orders_df.describe())
    ```

    *Self-reflection:* Observe the `null_count` row in the describe output. This is another way to spot missing numerical data.

  * **`.glimpse()` Method:**
    Provides a transposed, concise summary of column names, types, and initial values.

    ```python
    print("\nGlimpse of customers_df:")
    customers_df.glimpse()

    print("\nGlimpse of orders_df:")
    orders_df.glimpse()
    ```

**5. Identifying and Quantifying Missing Values using Expressions**

Missing data is a common issue. Polars expressions allow for flexible ways to identify and count nulls.

  * **Using `pl.all().is_null().sum()` within `select`:**
    To count null values for all columns simultaneously using an explicit expression, you can use `pl.all()` to refer to all columns, apply `is_null()` to each, and then `sum()` the boolean results (where `True` is 1 and `False` is 0).

    ```python
    # Count missing values in each column of customers_df using expressions
    missing_customers_expr = customers_df.select(
        pl.all().is_null().sum()
    )
    print("Missing values per column in customers_df (via expression):")
    print(missing_customers_expr)

    # Count missing values in each column of orders_df using expressions
    missing_orders_expr = orders_df.select(
        pl.all().is_null().sum()
    )
    print("\nMissing values per column in orders_df (via expression):")
    print(missing_orders_expr)
    ```

    This approach demonstrates how expressions can operate across multiple columns. The result is a new DataFrame where each column represents the sum of nulls for the corresponding column in the original DataFrame.

**6. Analyzing Unique Values in Columns using Expressions**

Understanding unique values helps characterize your data.

  * **Counting Unique Values with `pl.col().n_unique()`:**
    To get the number of unique values in one or more specific columns, use the `pl.col("column_name").n_unique()` expression within a `select` context.

    ```python
    # Get the number of unique values in the 'city' column of customers_df
    unique_cities_count_expr = customers_df.select(
        pl.col('city').n_unique().alias("unique_city_count")
    )
    print("\nNumber of unique cities in customers_df (via expression):")
    print(unique_cities_count_expr)

    # Get the number of unique values for 'product_category' and 'customer_id' in orders_df
    unique_counts_orders_expr = orders_df.select([
        pl.col('product_category').n_unique().alias("unique_product_categories"),
        pl.col('customer_id').n_unique().alias("unique_customer_ids_in_orders")
    ])
    print("\nNumber of unique product categories and customer IDs in orders_df (via expression):")
    print(unique_counts_orders_expr)
    ```

    Using `.alias()` within the expression allows you to name the resulting column in the output DataFrame.

  * **Retrieving Unique Values with `pl.col().unique()`:**
    To retrieve the actual unique values from a specific column, use the `pl.col("column_name").unique()` expression.

    ```python
    # Get the unique values in the 'product_category' column of orders_df
    unique_product_categories_expr = orders_df.select(
        pl.col('product_category').unique().sort() # .sort() is optional, for consistent order
    )
    print("\nUnique product categories in orders_df (via expression):")
    print(unique_product_categories_expr)

    # Get unique cities from customers_df
    unique_cities_expr = customers_df.select(
        pl.col('city').unique().sort() # .sort() is optional
    )
    print("\nUnique cities in customers_df (via expression):")
    print(unique_cities_expr)
    ```

    The result of `pl.col().unique()` is a column containing only the unique, non-null values.

