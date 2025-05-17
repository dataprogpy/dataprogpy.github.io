Okay, great! Let's craft a lesson outline for your "Data Wrangling with Polars" session.

This outline is designed for graduate business students with little to no prior programming experience, keeping in mind they've had an introduction to Python and Colab. It aims to align with your Material for MkDocs site and starter Jupyter notebook structure.

**Course Session: Data Wrangling with Polars**

**Overall Goal:** Equip students with the foundational skills to load, inspect, clean, transform, and aggregate data using Polars, understanding its core API concepts and how these operations fit into a typical data analysis workflow.

---

**Module 1: Introduction to Data Wrangling & Polars (Approx. 15-20 mins)**

* **Topic/Concept:**
    * What is data wrangling (data munging/tidying)? Why is it essential (the 80/20 rule of data science)?
    * Introducing Polars: A fast, expressive DataFrame library.
    * Brief overview of Polars' philosophy (Expressions, Lazy vs. Eager execution - very high level).
    * Setting up: Importing Polars (`import polars as pl`).
* **Learning Objectives:**
    * Define data wrangling and articulate its importance in the data analysis lifecycle.
    * Recognize Polars as a modern tool for data manipulation in Python.
    * Understand the basic setup for using Polars in a notebook.
* **Key Polars API/Functions:** `import polars as pl`
* **Activity/Example Idea:**
    * Discuss a relatable business scenario where messy data needs cleaning before insights can be drawn (e.g., inconsistent sales entries, customer survey data with varied formats).
    * Show how to import Polars.
* **Connection to Workflow/Business Context:** Sets the stage for why data wrangling is a critical first step in any data-driven decision-making process. (LO2, LO5)

---

**Module 2: Getting Data In - Data IO (Approx. 20-25 mins)**

* **Topic/Concept:**
    * Loading data into Polars DataFrames.
    * Reading common file formats:
        * CSV files (`pl.read_csv()`) - including common parameters like `separator`, `has_header`.
        * Data from a URL (pointing to a CSV).
        * JSON files (`pl.read_json()`) - focusing on simple, common JSON structures (list of records).
* **Learning Objectives:**
    * Load data from CSV and JSON files into a Polars DataFrame.
    * Read data directly from a URL.
* **Key Polars API/Functions:** `pl.read_csv()`, `pl.read_json()`
* **Activity/Example Idea:**
    * Provide students with sample CSV and JSON files (or URLs).
    * Starter notebook: Cells for students to load these files into DataFrames.
* **Connection to Workflow/Business Context:** The foundational step of acquiring data for analysis. (LO2)

---

**Module 3: First Look - Initial Data Exploration & Understanding (Approx. 30-40 mins)**

* **Topic/Concept:**
    * Understanding DataFrame structure: rows and columns.
    * Inspecting the DataFrame:
        * Dimensions: `.shape`
        * First and last rows: `.head()`, `.tail()`
        * Data types of columns: `.dtypes`, `.schema`
        * A quick overview/summary: `.glimpse()` (if available/desired) or `.describe()`
    * Identifying missing values: `.is_null().sum()`
    * Unique values in a column: `.n_unique()`, `.unique()` (on a Series)
* **Learning Objectives:**
    * Determine the dimensions and structure of a DataFrame.
    * Inspect the data types of columns and identify potential issues.
    * Summarize the data using descriptive statistics.
    * Quantify missing values in the dataset.
    * Identify unique values within specific columns.
* **Key Polars API/Functions:** `.shape`, `.head()`, `.tail()`, `.dtypes`, `.schema`, `.describe()`, `.glimpse()`, `.is_null()`, `.sum()`, `.n_unique()`, `.unique()`
* **Activity/Example Idea:**
    * Using the DataFrames loaded in Module 2, students apply these functions to explore the data.
    * Starter notebook: Guide them to answer questions like "How many rows/columns?", "What are the data types?", "Are there missing values? In which columns?".
* **Connection to Workflow/Business Context:** Crucial for understanding data quality, identifying necessary cleaning steps, and forming initial hypotheses. (LO2)

---

**Module 4: Core Polars Concepts - The Building Blocks (Approx. 25-30 mins)**

* **Topic/Concept:**
    * **DataFrame:** The primary 2D tabular data structure.
    * **Series:** A single column in a DataFrame.
    * **Expressions (`pl.col()`, `pl.lit()`):** The heart of Polars. How to define operations to be performed.
        * Referring to columns: `pl.col("column_name")`
        * Literal values: `pl.lit(value)`
    * Briefly explain that expressions are blueprints and are executed in contexts.
* **Learning Objectives:**
    * Differentiate between a DataFrame and a Series.
    * Understand the concept of a Polars Expression.
    * Write basic expressions to refer to columns and literal values.
* **Key Polars API/Functions:** `pl.DataFrame`, `pl.Series`, `pl.col()`, `pl.lit()`
* **Activity/Example Idea:**
    * Demonstrate creating a simple DataFrame from scratch.
    * Show how to select a single column (which results in a Series).
    * Illustrate creating a simple expression, e.g., `pl.col("price") * 1.1`.
* **Connection to Workflow/Business Context:** Understanding these core components is key to effectively using the Polars API for any transformation. (LO5)

---

**Module 5: Transforming Data - Selection, Filtering, and Modification (Approx. 60-75 mins)**

* **Topic/Concept:**
    * **Contexts for Expressions:**
        * Selecting columns: `df.select()`
        * Creating/modifying columns: `df.with_columns()`
        * Filtering rows: `df.filter()`
    * **Column Selection & Manipulation:**
        * Selecting specific columns: `df.select(pl.col("A"), pl.col("B"))`
        * Using Polars selectors (briefly): `cs.numeric()`, `cs.string()` for selecting columns by type. `df.select(cs.numeric())`
        * Renaming columns: `pl.col("old_name").alias("new_name")` within `select` or `with_columns`.
    * **Row Filtering:**
        * Based on conditions: `df.filter(pl.col("column_name") > value)`
        * Combining conditions: `&` (and), `|` (or)
    * **Data Type Conversion (Casting):**
        * Common conversions: `astype(pl.Int64)`, `astype(pl.Float64)`.
        * String to datetime: `pl.col("date_str_col").str.to_datetime()` (mention format string if necessary).
        * String to `pl.Categorical`: `pl.col("category_str_col").cast(pl.Categorical)` - for general categorical data.
        * String to `pl.Enum`: Define an enum `my_enum = pl.Enum(["Val1", "Val2"])`, then `pl.col("enum_str_col").cast(my_enum)` - for fixed, known categories. Discuss benefits (data integrity).
    * **Handling Missing Values (Basic):**
        * Filling nulls: `pl.col("column_name").fill_null(value_or_strategy)` (e.g., with a specific value, or mean/median for numerics - using expressions).
        * Dropping rows with nulls: `df.drop_nulls()` (use with caution, discuss implications).
    * **Sorting Data:** `df.sort("column_name", descending=False)`
* **Learning Objectives:**
    * Select and rename columns in a DataFrame.
    * Filter rows based on single or multiple conditions.
    * Create new columns or modify existing ones using expressions.
    * Convert column data types (numeric, string, datetime, categorical, enum).
    * Apply basic strategies for handling missing data.
    * Sort DataFrames by one or more columns.
* **Key Polars API/Functions:** `.select()`, `.with_columns()`, `.filter()`, `pl.col().alias()`, `cs` (selectors), `.cast()`, `pl.Int64`, `pl.Float64`, `pl.Utf8`, `pl.Datetime`, `pl.Categorical`, `pl.Enum`, `.str.to_datetime()`, `.fill_null()`, `.drop_nulls()`, `.sort()`
* **Activity/Example Idea:**
    * A series of guided exercises in the starter notebook:
        * Select only specific columns relevant to a business question.
        * Filter data (e.g., sales data for a particular region or time period).
        * Create a new column (e.g., calculate profit from revenue and cost).
        * Convert a date string column to datetime.
        * Convert a product category string column to `pl.Categorical` and then to a predefined `pl.Enum`.
        * Fill missing numerical values with the mean of the column.
        * Sort the data by sales amount.
* **Connection to Workflow/Business Context:** These are the core operations for cleaning data, preparing features for analysis or ML, and shaping the data to answer specific business questions. (LO2, LO3, LO5)

---

**Module 6: Aggregating & Reshaping Data - Summarizing for Insights (Approx. 40-50 mins)**

* **Topic/Concept:**
    * **Group By and Aggregations:** `df.group_by("key_column").agg([...])`
        * Common aggregations: `pl.sum()`, `pl.mean()`, `pl.median()`, `pl.min()`, `pl.max()`, `pl.count()`, `pl.n_unique()`.
        * Applying to specific columns: `pl.col("value_column").sum().alias("total_sales")`.
        * Grouping by multiple columns.
    * **(Optional/Brief) Pivoting:** `df.pivot()` - Introduce with a very simple, intuitive example (e.g., reshaping sales data to have years as columns). Mention this is for changing data layout. If time is tight, this can be a "further exploration" topic.
    * **(Mention as Advanced) Window Functions:** Briefly explain the concept (calculations across a set of table rows that are somehow related to the current row) and mention common use cases (e.g., running totals, moving averages) without deep diving into the API unless there's strong student interest and time.
* **Learning Objectives:**
    * Group data by one or more columns and compute aggregate statistics for each group.
    * Understand the basic concept and utility of pivoting data (if covered).
* **Key Polars API/Functions:** `.group_by()`, `.agg()`, `pl.sum()`, `pl.mean()`, `pl.count()`, etc., (Optional: `.pivot()`)
* **Activity/Example Idea:**
    * Calculate total/average sales per region or product category.
    * Count the number of customers per segment.
    * Starter notebook: Provide a dataset and ask students to derive specific aggregate insights.
* **Connection to Workflow/Business Context:** Essential for summarizing data, generating reports, and deriving higher-level insights from granular data. (LO2)

---

**Module 7: (Optional, if time permits) Combining DataFrames (Approx. 15-20 mins)**

* **Topic/Concept:**
    * Joining DataFrames: `df1.join(df2, on="key_column", how="inner/left")`
    * Brief explanation of inner and left joins.
* **Learning Objectives:**
    * Combine two DataFrames based on a common key using a simple join.
* **Key Polars API/Functions:** `.join()`
* **Activity/Example Idea:**
    * Provide two small DataFrames (e.g., one with order details, another with customer details) and join them.
* **Connection to Workflow/Business Context:** Data often resides in multiple tables/sources; joining is key to creating a unified view. (LO2)

---

**Module 8: Saving Data & Recap (Approx. 10-15 mins)**

* **Topic/Concept:**
    * Saving DataFrames to files: `df.write_csv()`, `df.write_json()`.
    * Recap of the data wrangling workflow with Polars.
    * Power of method chaining to build clean pipelines.
    * Briefly touch upon lazy execution (`.collect()`) if it hasn't been naturally introduced. Frame it as a way Polars optimizes queries, and `collect()` is used to get the final result of a lazy query.
* **Learning Objectives:**
    * Save a Polars DataFrame to a CSV or JSON file.
    * Summarize the key steps in a Polars data wrangling workflow.
* **Key Polars API/Functions:** `.write_csv()`, `.write_json()`, `.collect()`
* **Activity/Example Idea:**
    * Students save one of their transformed DataFrames.
    * Show a chained sequence of operations as an example of a mini-pipeline.
* **Connection to Workflow/Business Context:** Persisting cleaned/transformed data for future use, reporting, or input into other systems/models. Reinforces the overall process. (LO2, LO5)

---

**General Considerations for the Session:**

* **Dataset:** Use a single, relatable business-oriented dataset (or a couple of small related ones for joins) throughout the examples to provide continuity. E.g., sales data, customer data, product data.
* **Pacing:** Given they are beginners, prioritize understanding over breadth. Be prepared to slow down or skip optional/advanced topics if needed.
* **Interactive Learning:** The starter Jupyter notebook should have code cells for students to run examples and empty cells for them to complete exercises.
* **Error Messages:** Briefly explain common errors they might encounter and how to approach debugging (e.g., reading the error, checking column names, data types). (LO5)
* **Documentation & AI Tools:** Remind them of Learning Objective 5: "Leverage library documentation, online resources, and generative AI tools as aids for independent learning and troubleshooting." Encourage them to look up Polars functions.

This outline provides a comprehensive structure. You can adjust the timings based on the flow and student engagement. Good luck!