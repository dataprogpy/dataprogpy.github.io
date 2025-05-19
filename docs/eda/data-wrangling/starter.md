Okay, creating a starter Jupyter Notebook is an excellent way to facilitate active learning. This notebook will integrate the explanations and code examples we've developed for each module, providing scaffolding for students, and will conclude with a set of tasks for them to practice.

Here's a structure for the Jupyter Notebook. I'll provide it in a format where each cell is denoted, which you can then easily convert into an `.ipynb` file.

-----

```json
{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Data Wrangling with Polars\n",
    "\n",
    "**Overall Goal:** Equip students with the foundational skills to load, inspect, clean, transform, and aggregate data using Polars, understanding its core API concepts and how these operations fit into a typical data analysis workflow."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Setup\n",
    "\n",
    "First, let's import the Polars library. The standard alias is `pl`."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "import polars as pl\n",
    "import polars.selectors as cs # For using column selectors like cs.numeric()\n",
    "\n",
    "# Print Polars version for reference\n",
    "print(f\"Polars version: {pl.__version__}\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "---",
    "\n",
    "## Module 1: Introduction to Data Wrangling & Polars\n",
    "\n",
    "This module introduces the foundational concepts of data wrangling and the Polars library, a powerful tool for this purpose. Effective data wrangling is a critical precursor to sound business analysis and data-driven decision-making.\n",
    "\n",
    "### 1. The Imperative of Data Wrangling in Business Analytics\n",
    "\n",
    "In the context of business analytics, **data wrangling** refers to the process of transforming and mapping raw data from its original state into a clean, structured, and validated format suitable for analysis. This process is also commonly referred to as data munging or data tidying.\n",
    "\n",
    "* **The \"80/20 Rule\" in Data Preparation:** It is widely recognized within the data science community that data preparation activities, including wrangling, can consume as much as 80% of the total time and effort in an analytical project. The remaining 20% is typically spent on performing the actual analysis and generating insights. This highlights the substantial resources dedicated to ensuring data quality and usability.\n",
    "* **Data Sources and the Need for Repurposing:**\n",
    "    Data utilized for business analytics is often sourced from systems not initially designed for that specific analytical objective. These sources can include:\n",
    "    * **Online Transaction Processing (OLTP) Systems:** Operational systems (e.g., sales, CRM, ERP) optimized for transaction recording, not complex queries.\n",
    "    * **Online Analytical Processing (OLAP) Systems:** Designed for analysis, but specific questions may require further manipulation or integration.\n",
    "    * **Data Lakes:** Repositories of vast raw data needing significant wrangling.\n",
    "    * **Governance and Regulatory Data Archival Systems:** Data structured for compliance, often requiring transformation for analytics.\n",
    "\n",
    "    Because these diverse systems serve different primary functions, the data they produce rarely aligns perfectly with the schema (structure) required for a specific analytical investigation. Consequently, data wrangling becomes an essential intermediary step.\n",
    "\n",
    "* **Illustrative Data Challenges (Preview for our datasets `customers.csv` and `orders.csv`):\n",
    "    * Inconsistent date formats.\n",
    "    * Missing values in critical fields.\n",
    "    * Outliers or erroneous data entries.\n",
    "\n",
    "### 2. Introduction to Polars: A High-Performance DataFrame Library\n",
    "\n",
    "* **Overview:** Polars is a modern DataFrame library implemented in Rust, designed for high performance and an expressive API in Python.\n",
    "* **Advantages:** Speed, expressive syntax, modern capabilities.\n",
    "\n",
    "### 3. Core Polars Concepts: A High-Level View\n",
    "\n",
    "* **Expressions:** Blueprints for operations (`pl.col()`, `pl.lit()`).\n",
    "* **Execution Strategies (Lazy vs. Eager):** Polars can optimize a plan of operations (lazy) or execute immediately (eager).\n",
    "\n",
    "### 4. Setup: Importing the Polars Library\n",
    "\n",
    "We've already done this in the setup cell above: `import polars as pl`."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "---",
    "\n",
    "## Module 2: Getting Data In - Data IO\n",
    "\n",
    "This module focuses on loading data into Polars DataFrames from common file formats.\n",
    "\n",
    "**Note for Google Colab users:** You will need to upload `customers.csv` and `orders.csv` to your Colab environment. You can do this by clicking the folder icon on the left sidebar, then the \"Upload to session storage\" icon."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# TODO: If using Google Colab, uncomment and run these lines to upload files.\n",
    "# from google.colab import files\n",
    "# print(\"Please upload customers.csv and orders.csv\")\n",
    "# uploaded = files.upload()\n",
    "# \n",
    "# for fn in uploaded.keys():\n",
    "#   print('User uploaded file \"{name}\" with length {length} bytes'.format(\n",
    "#       name=fn, length=len(uploaded[fn])))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 1. Reading Comma-Separated Values (CSV) Files\n",
    "\n",
    "We use `pl.read_csv()` to load data from CSV files."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Load the customers dataset\n",
    "try:\n",
    "    customers_df = pl.read_csv('customers.csv')\n",
    "    print(\"customers.csv loaded successfully.\")\n",
    "    # Display a sample to verify (more on this in Module 3)\n",
    "    # print(\"Sample of customers_df:\")\n",
    "    # print(customers_df.head(3))\n",
    "except Exception as e:\n",
    "    print(f\"Error loading customers.csv: {e}\")\n",
    "    print(\"Please ensure 'customers.csv' is in the same directory as this notebook or uploaded to Colab.\")\n",
    "\n",
    "# Load the orders dataset\n",
    "try:\n",
    "    orders_df = pl.read_csv('orders.csv')\n",
    "    print(\"\\norders.csv loaded successfully.\")\n",
    "    # Display a sample to verify\n",
    "    # print(\"Sample of orders_df:\")\n",
    "    # print(orders_df.head(3))\n",
    "except Exception as e:\n",
    "    print(f\"Error loading orders.csv: {e}\")\n",
    "    print(\"Please ensure 'orders.csv' is in the same directory as this notebook or uploaded to Colab.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#### Specifying or Overriding Data Types (Schema) with `dtypes`\n",
    "\n",
    "You can explicitly define column data types during import for correctness, performance, or memory efficiency."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Define the desired schema for selected columns in customers.csv\n",
    "customer_schema = {\n",
    "    'customer_id': pl.Int32,\n",
    "    'name': pl.Utf8,\n",
    "    'registration_date': pl.Utf8, # Read as string; parsing will be handled later\n",
    "    'city': pl.Categorical,\n",
    "    'age': pl.Int64 # Allow for nulls, but keep as Int64 for now\n",
    "}\n",
    "\n",
    "try:\n",
    "    customers_custom_schema_df = pl.read_csv('customers.csv', dtypes=customer_schema)\n",
    "    print(\"\\ncustomers.csv loaded with custom schema successfully.\")\n",
    "    # print(\"Schema of customers_custom_schema_df:\")\n",
    "    # print(customers_custom_schema_df.schema)\n",
    "except Exception as e:\n",
    "    print(f\"Error loading customers.csv with custom schema: {e}\")\n",
    "\n",
    "# For subsequent modules, we'll primarily use the initially loaded customers_df and orders_df \n",
    "# (without the custom schema for customers_df initially) to demonstrate transformations like type casting.\n",
    "# However, let's rename the original date columns to clarify they are strings before parsing in later modules.\n",
    "customers_df = customers_df.rename({\"registration_date\": \"registration_date_str\"})\n",
    "orders_df = orders_df.rename({\"order_date\": \"order_date_str\"})"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 2. Reading CSV Data from a URL"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "airline_passengers_url = 'https://raw.githubusercontent.com/plotly/datasets/master/airline-passengers.csv'\n",
    "try:\n",
    "    airline_passengers_df = pl.read_csv(airline_passengers_url)\n",
    "    print(\"\\nAirline passengers data loaded successfully from URL.\")\n",
    "    # print(airline_passengers_df.head())\n",
    "except Exception as e:\n",
    "    print(f\"Error loading data from URL: {e}\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 3. Reading JSON Files\n",
    "\n",
    "Polars can read JSON files, typically those structured as a list of records."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "First, let's create a sample JSON file named `product_inventory.json` in our working directory. You can do this by running the code cell below, which writes the JSON string to a file."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "product_inventory_json_content = """\n",
    "[\n",
    "  {\"product_id\": \"P1001\", \"product_name\": \"Laptop X1\", \"category\": \"Electronics\", \"stock_level\": 150, \"reorder_point\": 50},\n",
    "  {\"product_id\": \"P1002\", \"product_name\": \"Wireless Mouse\", \"category\": \"Electronics\", \"stock_level\": 300, \"reorder_point\": 100},\n",
    "  {\"product_id\": \"P1003\", \"product_name\": \"Office Chair Pro\", \"category\": \"Furniture\", \"stock_level\": 85, \"reorder_point\": 30},\n",
    "  {\"product_id\": \"P1004\", \"product_name\": \"Standing Desk\", \"category\": \"Furniture\", \"stock_level\": 60, \"reorder_point\": 20}\n",
    "]\n",
    """"\n",
    "\n",
    "with open('product_inventory.json', 'w') as f:\n",
    "    f.write(product_inventory_json_content)\n",
    "\n",
    "print(\"'product_inventory.json' created successfully.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "try:\n",
    "    inventory_df = pl.read_json('product_inventory.json')\n",
    "    print(\"\\nProduct inventory data loaded successfully from JSON.\")\n",
    "    # print(inventory_df.head())\n",
    "except Exception as e:\n",
    "    print(f\"Error loading product_inventory.json: {e}\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "---",
    "\n",
    "## Module 3: First Look - Initial Data Exploration & Understanding\n",
    "\n",
    "This module covers fundamental techniques to inspect DataFrames.\n",
    "\n",
    "**Make sure `customers_df` and `orders_df` are loaded from Module 2 before proceeding.**"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 1. Understanding DataFrame Structure: Dimensions (`.shape`)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'customers_df' in locals() and 'orders_df' in locals():\n",
    "    customer_dimensions = customers_df.shape\n",
    "    print(f\"Customers DataFrame - Rows: {customer_dimensions[0]}, Columns: {customer_dimensions[1]}\")\n",
    "\n",
    "    order_dimensions = orders_df.shape\n",
    "    print(f\"Orders DataFrame - Rows: {order_dimensions[0]}, Columns: {order_dimensions[1]}\")\n",
    "else:\n",
    "    print(\"Please load customers_df and orders_df first (see Module 2).\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 2. Inspecting Data Content: First and Last Rows (`.head()`, `.tail()`)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'customers_df' in locals():\n",
    "    print(\"First 3 rows of customers_df:\")\n",
    "    print(customers_df.head(3))\n",
    "else:\n",
    "    print(\"customers_df not loaded.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Your turn: Display the last 4 rows of the orders_df\n",
    "if 'orders_df' in locals():\n",
    "    print(\"\\nLast 4 rows of orders_df:\")\n",
    "    # TODO: Write your code here\n",
    "    print(orders_df.tail(4))\n",
    "else:\n",
    "    print(\"orders_df not loaded.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 3. Examining Data Types and Schema (`.dtypes`, `.schema`)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'customers_df' in locals():\n",
    "    print(\"Data types for customers_df:\")\n",
    "    print(customers_df.dtypes)\n",
    "\n",
    "    print(\"\\nSchema for customers_df:\")\n",
    "    print(customers_df.schema)\n",
    "else:\n",
    "    print(\"customers_df not loaded.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 4. Obtaining Descriptive Statistics and a Quick Overview (`.describe()`, `.glimpse()`)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'orders_df' in locals():\n",
    "    print(\"Descriptive statistics for orders_df:\")\n",
    "    print(orders_df.describe()) # Typically shows numerical columns\n",
    "else:\n",
    "    print(\"orders_df not loaded.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'customers_df' in locals():\n",
    "    print(\"\\nGlimpse of customers_df:\")\n",
    "    customers_df.glimpse()\n",
    "else:\n",
    "    print(\"customers_df not loaded.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 5. Identifying and Quantifying Missing Values using Expressions"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'customers_df' in locals():\n",
    "    missing_customers_expr = customers_df.select(\n",
    "        pl.all().is_null().sum()\n",
    "    )\n",
    "    print(\"Missing values per column in customers_df (via expression):\")\n",
    "    print(missing_customers_expr)\n",
    "else:\n",
    "    print(\"customers_df not loaded.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Your turn: Count missing values in each column of orders_df using the expression API\n",
    "if 'orders_df' in locals():\n",
    "    print(\"\\nMissing values per column in orders_df (via expression):\")\n",
    "    # TODO: Write your code here\n",
    "    missing_orders_expr = orders_df.select(pl.all().is_null().sum())\n",
    "    print(missing_orders_expr)\n",
    "else:\n",
    "    print(\"orders_df not loaded.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 6. Analyzing Unique Values in Columns using Expressions"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'customers_df' in locals():\n",
    "    unique_cities_count_expr = customers_df.select(\n",
    "        pl.col('city').n_unique().alias(\"unique_city_count\")\n",
    "    )\n",
    "    print(\"Number of unique cities in customers_df (via expression):\")\n",
    "    print(unique_cities_count_expr)\n",
    "\n",
    "    unique_cities_expr = customers_df.select(\n",
    "        pl.col('city').unique().sort() # .sort() is optional, for consistent order\n",
    "    )\n",
    "    print(\"\\nUnique cities in customers_df (via expression):\")\n",
    "    print(unique_cities_expr)\n",
    "else:\n",
    "    print(\"customers_df not loaded.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "---",
    "\n",
    "## Module 4: Core Polars Concepts - The Building Blocks\n",
    "\n",
    "This module delves into DataFrames, Series, and Expressions."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 1. Polars DataFrame Revisited\n",
    "A 2D, in-memory, tabular data structure."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "data_dict = {\n",
    "    'StudentID': [1001, 1002, 1003],\n",
    "    'Course': ['Finance', 'Marketing', 'Finance'],\n",
    "    'MidtermScore': [85, 92, 78]\n",
    "}\n",
    "scores_df = pl.DataFrame(data_dict)\n",
    "print(\"DataFrame created from scratch:\")\n",
    "print(scores_df)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 2. Polars Series\n",
    "A 1D array-like object representing a single column."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'customers_df' in locals():\n",
    "    city_series = customers_df.get_column('city')\n",
    "    print(\"'city' column extracted as a Series:\")\n",
    "    print(city_series.head())\n",
    "    print(f\"Type of city_series: {type(city_series)}\")\n",
    "    print(f\"Name of city_series: {city_series.name}\")\n",
    "    print(f\"DataType of city_series: {city_series.dtype}\")\n",
    "else:\n",
    "    print(\"customers_df not loaded.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 3. Polars Expressions: The Core Engine\n",
    "Blueprints for operations, evaluated in contexts.\n",
    "\n",
    "#### Referring to Columns: `pl.col()`\n",
    "#### Literal Values: `pl.lit()`\n",
    "#### Basic Operations with Expressions"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'orders_df' in locals() and 'customers_df' in locals():\n",
    "    original_price_expr = pl.col(\"unit_price\")\n",
    "    discount_rate_lit_expr = pl.lit(0.05) # 5% discount\n",
    "    discounted_price_expr = original_price_expr * (pl.lit(1) - discount_rate_lit_expr)\n",
    "    print(f\"Expression for discounted price: {discounted_price_expr}\")\n",
    "\n",
    "    is_older_than_30_expr = pl.col(\"age\") > pl.lit(30)\n",
    "    print(f\"Expression for 'age > 30': {is_older_than_30_expr}\")\n",
    "else:\n",
    "    print(\"Ensure orders_df and customers_df are loaded.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#### Expressions are Executed in Contexts (Preview for Modules 5 & 6)\n",
    "Different contexts use expressions for different purposes."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'customers_df' in locals():\n",
    "    print(\"Preview: Expression in `select` context\")\n",
    "    customer_status_df = customers_df.select([\n",
    "        pl.col(\"name\"),\n",
    "        pl.col(\"age\"),\n",
    "        (pl.col(\"age\") < pl.lit(18)).alias(\"is_minor\")\n",
    "    ])\n",
    "    print(customer_status_df.head())\n",
    "else:\n",
    "    print(\"customers_df not loaded.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'orders_df' in locals():\n",
    "    print(\"\\nPreview: Expression in `with_columns` context\")\n",
    "    orders_with_total = orders_df.with_columns(\n",
    "        (pl.col(\"quantity\") * pl.col(\"unit_price\")).alias(\"total_price\")\n",
    "    )\n",
    "    print(orders_with_total.head())\n",
    "else:\n",
    "    print(\"orders_df not loaded.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'customers_df' in locals():\n",
    "    print(\"\\nPreview: Expression in `filter` context\")\n",
    "    # Using the raw 'age' column for this example. Note the outlier.\n",
    "    adult_customers_df = customers_df.filter(\n",
    "        pl.col(\"age\") > pl.lit(30) \n",
    "    )\n",
    "    print(adult_customers_df.select([\"name\", \"age\"])) # Showing relevant columns\n",
    "else:\n",
    "    print(\"customers_df not loaded.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'orders_df' in locals():\n",
    "    print(\"\\nPreview: Expression in `group_by().agg()` context\")\n",
    "    sales_by_category = orders_df.group_by(\"product_category\").agg([\n",
    "        pl.sum(\"quantity\").alias(\"total_quantity_sold\"),\n",
    "        pl.mean(\"unit_price\").alias(\"average_unit_price\")\n",
    "    ])\n",
    "    print(sales_by_category)\n",
    "else:\n",
    "    print(\"orders_df not loaded.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "---",
    "\n",
    "## Module 5: Transforming Data - Selection, Filtering, and Modification\n",
    "\n",
    "This module covers primary operations for transforming data.\n",
    "\n",
    "**Ensure `customers_df` and `orders_df` are loaded and column names `registration_date_str` and `order_date_str` are set as per Module 2 modifications.**"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 1. Column Selection and Manipulation with `select`"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'customers_df' in locals():\n",
    "    customer_locations_df = customers_df.select([\n",
    "        pl.col(\"name\"),\n",
    "        pl.col(\"city\")\n",
    "    ])\n",
    "    print(\"Selected customer names and cities:\")\n",
    "    print(customer_locations_df.head())\n",
    "\n",
    "    renamed_customers_df = customers_df.select([\n",
    "        pl.col(\"customer_id\").alias(\"ID\"),\n",
    "        pl.col(\"name\").alias(\"Customer Name\")\n",
    "    ])\n",
    "    print(\"\\nCustomers DataFrame with renamed columns:\")\n",
    "    print(renamed_customers_df.head())\n",
    "else:\n",
    "    print(\"customers_df not loaded.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#### Using Polars Selectors (`cs`)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'orders_df' in locals():\n",
    "    string_cols_orders_df = orders_df.select(cs.string())\n",
    "    print(\"String columns from orders_df:\")\n",
    "    print(string_cols_orders_df.head())\n",
    "\n",
    "    # Your turn: Select all numeric columns from orders_df\n",
    "    print(\"\\nNumeric columns from orders_df:\")\n",
    "    # TODO: Write your code here\n",
    "    numeric_cols_orders_df = orders_df.select(cs.numeric())\n",
    "    print(numeric_cols_orders_df.head())\n",
    "else:\n",
    "    print(\"orders_df not loaded.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 2. Adding or Modifying Columns with `with_columns`"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'orders_df' in locals():\n",
    "    orders_enhanced_df = orders_df.with_columns([\n",
    "        (pl.col(\"quantity\") * pl.col(\"unit_price\")).alias(\"total_price\"),\n",
    "        pl.col(\"discount_applied\").is_not_null().alias(\"has_discount\")\n",
    "    ])\n",
    "    print(\"Orders DataFrame with 'total_price' and 'has_discount' columns:\")\n",
    "    print(orders_enhanced_df.head())\n",
    "else:\n",
    "    print(\"orders_df not loaded.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Your turn: In customers_df, create a new column 'name_length' that contains the length of the customer's name.\n",
    "if 'customers_df' in locals():\n",
    "    customers_with_name_length_df = customers_df.with_columns(\n",
    "        # TODO: Write your expression here\n",
    "        pl.col(\"name\").str.len_chars().alias(\"name_length\")\n",
    "    )\n",
    "    print(\"\\nCustomers DataFrame with name_length:\")\n",
    "    print(customers_with_name_length_df.select([\"name\", \"name_length\"]).head())\n",
    "else:\n",
    "    print(\"customers_df not loaded.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 3. Filtering Rows with `filter`"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'orders_df' in locals():\n",
    "    electronics_orders_df = orders_df.filter(\n",
    "        pl.col(\"product_category\") == pl.lit(\"Electronics\")\n",
    "    )\n",
    "    print(\"Electronics orders:\")\n",
    "    print(electronics_orders_df.head())\n",
    "else:\n",
    "    print(\"orders_df not loaded.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Note: The 'age' column in customers.csv has an outlier (3000) and missing values.\n",
    "# For realistic filtering, this should be handled.\n",
    "# Let's create a cleaned version for this filtering example.\n",
    "if 'customers_df' in locals():\n",
    "    customers_cleaned_age_df = customers_df.with_columns(\n",
    "        pl.when(pl.col(\"age\") > 100).then(None).otherwise(pl.col(\"age\")).cast(pl.Int64, strict=False).alias(\"age_cleaned\")\n",
    "    )\n",
    "    older_customers_df = customers_cleaned_age_df.filter(\n",
    "        pl.col(\"age_cleaned\") > pl.lit(50)\n",
    "    )\n",
    "    print(\"\\nCustomers older than 50 (age cleaned):\")\n",
    "    print(older_customers_df.select([\"name\", \"age_cleaned\", \"city\"]))\n",
    "else:\n",
    "    print(\"customers_df not loaded.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Your turn: Filter orders_df for products in the 'Home Goods' category OR where 'quantity' is greater than 2.\n",
    "if 'orders_df' in locals():\n",
    "    print(\"\\nHome Goods orders OR quantity > 2:\")\n",
    "    # TODO: Write your code here\n",
    "    filtered_orders_complex = orders_df.filter(\n",
    "        (pl.col(\"product_category\") == pl.lit(\"Home Goods\")) | (pl.col(\"quantity\") > pl.lit(2))\n",
    "    )\n",
    "    print(filtered_orders_complex)\n",
    "else:\n",
    "    print(\"orders_df not loaded.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 4. Data Type Conversion (Casting)\n",
    "Using `pl.col().cast(DataType)` or specific conversion methods like `str.to_datetime()`."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Casting 'city' to Categorical in customers_df\n",
    "if 'customers_df' in locals():\n",
    "    customers_city_categorical_df = customers_df.with_columns(\n",
    "        pl.col(\"city\").cast(pl.Categorical).alias(\"city_categorical\")\n",
    "    )\n",
    "    print(\"Customers DataFrame with city as Categorical:\")\n",
    "    print(customers_city_categorical_df.select([\"name\", \"city_categorical\"]).head())\n",
    "    print(f\"Data type of 'city_categorical': {customers_city_categorical_df.get_column('city_categorical').dtype}\")\n",
    "else:\n",
    "    print(\"customers_df not loaded.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Parsing 'order_date_str' in orders_df to Datetime\n",
    "if 'orders_df' in locals():\n",
    "    orders_parsed_dates_df = orders_df.with_columns(\n",
    "        pl.col(\"order_date_str\").str.to_datetime(format=\"%Y-%m-%d %H:%M:%S\", strict=False).alias(\"order_datetime\")\n",
    "    )\n",
    "    print(\"\\nOrders DataFrame with parsed order_datetime:\")\n",
    "    print(orders_parsed_dates_df.select([\"order_id\", \"order_datetime\"]).head())\n",
    "    print(f\"Data type of 'order_datetime': {orders_parsed_dates_df.get_column('order_datetime').dtype}\")\n",
    "else:\n",
    "    print(\"orders_df not loaded.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Parsing 'registration_date_str' in customers_df (handles mixed formats and nulls)\n",
    "if 'customers_df' in locals():\n",
    "    customers_parsed_reg_dates_df = customers_df.with_columns(\n",
    "        pl.coalesce([\n",
    "            pl.col(\"registration_date_str\").str.to_date(format=\"%Y-%m-%d\", strict=False),\n",
    "            pl.col(\"registration_date_str\").str.to_date(format=\"%m/%d/%Y\", strict=False)\n",
    "        ]).alias(\"registration_date\")\n",
    "    )\n",
    "    print(\"\\nCustomers DataFrame with parsed registration_date:\")\n",
    "    print(customers_parsed_reg_dates_df.select([\"name\", \"registration_date_str\", \"registration_date\"])) \n",
    "    print(f\"Data type of 'registration_date': {customers_parsed_reg_dates_df.get_column('registration_date').dtype}\")\n",
    "else:\n",
    "    print(\"customers_df not loaded.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 5. Handling Missing Values\n",
    "Using `fill_null()` or `drop_nulls()`."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Ensure customers_cleaned_age_df from earlier is available or recreate it\n",
    "if 'customers_df' in locals():\n",
    "    if 'customers_cleaned_age_df' not in locals() or not isinstance(customers_cleaned_age_df, pl.DataFrame):\n",
    "         customers_cleaned_age_df = customers_df.with_columns(\n",
    "            pl.when(pl.col(\"age\") > 100).then(None).otherwise(pl.col(\"age\")).cast(pl.Float64, strict=False).alias(\"age_cleaned\")\n",
    "        )\n",
    "\n",
    "    # Calculate mean age for filling, excluding extreme outliers for a more sensible mean\n",
    "    # Note: .item() extracts the single value from a 1x1 DataFrame/Series\n",
    "    mean_age_val = customers_cleaned_age_df.select(pl.col(\"age_cleaned\").mean()).item() \n",
    "\n",
    "    customers_filled_age_df = customers_cleaned_age_df.with_columns(\n",
    "        pl.col(\"age_cleaned\").fill_null(mean_age_val).alias(\"age_filled_with_mean\")\n",
    "    )\n",
    "    print(f\"Customers DataFrame with 'age_cleaned' nulls filled with mean ({mean_age_val:.2f}):\")\n",
    "    print(customers_filled_age_df.filter(customers_df[\"age\"].is_null()).select([\"name\", \"age\", \"age_cleaned\", \"age_filled_with_mean\"]))    \n",
    "else:\n",
    "    print(\"customers_df not loaded.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Your turn: In orders_df, fill missing 'discount_applied' with 0.0 and missing 'quantity' with 1.\n",
    "if 'orders_df' in locals():\n",
    "    orders_filled_df = orders_df.with_columns([\n",
    "        # TODO: Fill 'discount_applied' nulls with 0.0\n",
    "        pl.col(\"discount_applied\").fill_null(0.0).alias(\"discount_applied_filled\"),\n",
    "        # TODO: Fill 'quantity' nulls with 1 (assuming a missing quantity implies at least one item)\n",
    "        pl.col(\"quantity\").fill_null(1).alias(\"quantity_filled\")\n",
    "    ])\n",
    "    print(\"\\nOrders DataFrame with nulls filled for discount and quantity:\")\n",
    "    print(orders_filled_df.filter(pl.col(\"discount_applied\").is_null() | pl.col(\"quantity\").is_null()).select([\"order_id\", \"discount_applied\", \"discount_applied_filled\", \"quantity\", \"quantity_filled\"]).head())\n",
    "    # Verify no nulls remain in these specific columns\n",
    "    print(\"\\nNull counts after filling:\")\n",
    "    print(orders_filled_df.select([\"discount_applied_filled\", \"quantity_filled\"]).is_null().sum())\n",
    "else:\n",
    "    print(\"orders_df not loaded.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Dropping rows with nulls\n",
    "if 'orders_df' in locals():\n",
    "    # Drop rows from orders_df where 'quantity' is null (using original orders_df for this example)\n",
    "    orders_dropped_null_quantity_df = orders_df.drop_nulls(subset=[\"quantity\"])\n",
    "    print(f\"Original orders count: {orders_df.height}, After dropping null quantity: {orders_dropped_null_quantity_df.height}\")\n",
    "else:\n",
    "    print(\"orders_df not loaded.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 6. Sorting Data with `sort()`"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'customers_df' in locals() and 'customers_cleaned_age_df' in locals(): # Using cleaned age for sorting\n",
    "    sorted_customers_by_age_df = customers_cleaned_age_df.sort(\"age_cleaned\", descending=True)\n",
    "    print(\"Customers sorted by cleaned age (descending):\")\n",
    "    print(sorted_customers_by_age_df.select([\"name\", \"age_cleaned\", \"city\"]).head())\n",
    "else:\n",
    "    print(\"customers_df or customers_cleaned_age_df not available.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "---",
    "\n",
    "## Module 6: Aggregating & Reshaping Data - Summarizing for Insights\n",
    "\n",
    "This module covers grouping data and applying aggregation functions."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 1. Group By and Aggregations (`.group_by().agg()`)\n",
    "The \"split-apply-combine\" strategy."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'orders_df' in locals():\n",
    "    category_summary_df = orders_df.group_by(\"product_category\").agg([\n",
    "        pl.sum(\"quantity\").alias(\"total_quantity_sold\"),\n",
    "        pl.mean(\"unit_price\").alias(\"average_unit_price\"),\n",
    "        pl.col(\"order_id\").count().alias(\"number_of_orders\"),\n",
    "        pl.n_unique(\"customer_id\").alias(\"unique_customers_in_category\")\n",
    "    ]).sort(\"total_quantity_sold\", descending=True)\n",
    "\n",
    "    print(\"Sales summary by product category:\")\n",
    "    print(category_summary_df)\n",
    "else:\n",
    "    print(\"orders_df not loaded.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Your turn: Using customers_cleaned_age_df, group by 'city' and find:\n",
    "# 1. The number of customers in each city.\n",
    "# 2. The average cleaned age of customers in each city.\n",
    "# Sort the results by the number of customers in descending order.\n",
    "if 'customers_cleaned_age_df' in locals():\n",
    "    print(\"\\nCustomer demographics by city:\")\n",
    "    # TODO: Write your code here\n",
    "    city_demographics_df = customers_cleaned_age_df.group_by(\"city\").agg([\n",
    "        pl.count().alias(\"number_of_customers\"),\n",
    "        pl.mean(\"age_cleaned\").alias(\"average_age\")\n",
    "    ]).sort(\"number_of_customers\", descending=True)\n",
    "    print(city_demographics_df)\n",
    "else:\n",
    "    print(\"customers_cleaned_age_df not available. Please ensure it was created in Module 5.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 2. (Optional/Brief) Pivoting Data with `df.pivot()`"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'orders_df' in locals():\n",
    "    # Ensure order_datetime is parsed for year extraction\n",
    "    if 'order_datetime' not in orders_df.columns:\n",
    "        current_orders_df = orders_df.with_columns(\n",
    "             pl.col(\"order_date_str\").str.to_datetime(format=\"%Y-%m-%d %H:%M:%S\", strict=False).alias(\"order_datetime\")\n",
    "        )\n",
    "    else:\n",
    "        current_orders_df = orders_df\n",
    "\n",
    "    orders_with_year_df = current_orders_df.with_columns(\n",
    "        pl.col(\"order_datetime\").dt.year().alias(\"order_year\")\n",
    "    )\n",
    "\n",
    "    yearly_category_sales = orders_with_year_df.group_by([\"order_year\", \"product_category\"]).agg(\n",
    "        pl.sum(\"quantity\").alias(\"total_quantity\")\n",
    "    ).sort([\"order_year\", \"product_category\"])\n",
    "\n",
    "    print(\"Yearly sales by category (long format):\")\n",
    "    print(yearly_category_sales.head())\n",
    "\n",
    "    pivoted_yearly_sales_df = yearly_category_sales.pivot(\n",
    "        values=\"total_quantity\",\n",
    "        index=\"order_year\",\n",
    "        columns=\"product_category\"\n",
    "    ).fill_null(0)\n",
    "\n",
    "    print(\"\\nPivoted yearly sales (product categories as columns):\")\n",
    "    print(pivoted_yearly_sales_df)\n",
    "else:\n",
    "    print(\"orders_df not loaded or order_datetime not parsed.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 3. (Mention as Advanced) Window Functions\n",
    "Window functions perform calculations across a set of table rows related to the current row. Examples include running totals, moving averages, and ranking within groups."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'orders_df' in locals():\n",
    "    # Ensure order_datetime is parsed and quantity is numeric\n",
    "    if 'order_datetime' not in orders_df.columns:\n",
    "        current_orders_df_window = orders_df.with_columns([\n",
    "             pl.col(\"order_date_str\").str.to_datetime(format=\"%Y-%m-%d %H:%M:%S\", strict=False).alias(\"order_datetime\"),\n",
    "             pl.col(\"quantity\").fill_null(0) # Ensure quantity is filled for cumsum\n",
    "        ])\n",
    "    else:\n",
    "        current_orders_df_window = orders_df.with_columns(pl.col(\"quantity\").fill_null(0))\n",
    "\n",
    "    orders_sorted_for_window = current_orders_df_window.sort([\"product_category\", \"order_datetime\"])\n",
    "\n",
    "    orders_with_running_total = orders_sorted_for_window.with_columns(\n",
    "        pl.col(\"quantity\").cumsum().over(\"product_category\").alias(\"running_total_quantity_in_category\")\n",
    "    )\n",
    "    print(\"Orders with running total quantity within category (illustrative):\")\n",
    "    print(orders_with_running_total.filter(pl.col(\"product_category\") == \"Books\").select([\n",
    "        \"order_datetime\", \"product_category\", \"quantity\", \"running_total_quantity_in_category\"\n",
    "    ]).tail(5))\n",
    "else:\n",
    "    print(\"orders_df not loaded.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "---",
    "\n",
    "## Module 7: Stitching and Saving Data\n",
    "\n",
    "This module covers joining related datasets and persisting processed data."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 1. Stitching Data: Joining DataFrames with `.join()`"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'orders_df' in locals() and 'customers_df' in locals():\n",
    "    merged_inner_df = orders_df.join(\n",
    "        customers_df, # The DataFrame to join with\n",
    "        on=\"customer_id\", # The common column to join on\n",
    "        how=\"inner\"       # The type of join\n",
    "    )\n",
    "    print(\"Inner Join - Orders with Customer Details (first 5 rows):\")\n",
    "    print(merged_inner_df.select([\n",
    "        \"order_id\", \"customer_id\", \"name\", \"city\", \"product_category\", \"unit_price\"\n",
    "    ]).head())\n",
    "    print(f\"Shape of inner joined df: {merged_inner_df.shape}\")\n",
    "\n",
    "    # Left join: All customers, with their order details if available\n",
    "    customer_orders_left_df = customers_df.join(\n",
    "        orders_df,\n",
    "        on=\"customer_id\",\n",
    "        how=\"left\"\n",
    "    )\n",
    "    print(\"\\nLeft Join - All Customers with Their Orders (if any) (first 10 rows):\")\n",
    "    print(customer_orders_left_df.select([\n",
    "        \"customer_id\", \"name\", \"city\", \"order_id\", \"product_category\"\n",
    "    ]).sort(\"customer_id\").head(10))\n",
    "    # Customers with no orders will have null for order_id and other order-specific columns\n",
    "    print(\"\\nCustomers with potentially no orders (order_id is null from left join):\")\n",
    "    print(customer_orders_left_df.filter(pl.col(\"order_id\").is_null()).select([\"customer_id\", \"name\", \"city\"]))\n",
    "else:\n",
    "    print(\"Ensure orders_df and customers_df are loaded.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 2. Saving DataFrames to Files"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "if 'merged_inner_df' in locals(): # From the join example above\n",
    "    output_df_for_csv = merged_inner_df.select([\n",
    "        \"order_id\", \"customer_id\", \"name\", \"city\", \"product_category\", \"quantity\", \"unit_price\", \"age_cleaned\", \"registration_date_str\"\n",
    "    ]).sort(\"order_id\") # Select a few more columns for richness\n",
    "\n",
    "    try:\n",
    "        output_df_for_csv.write_csv(\"enriched_orders_report.csv\")\n",
    "        print(\"\\nSuccessfully saved 'enriched_orders_report.csv'\")\n",
    "    except Exception as e:\n",
    "        print(f\"Error saving CSV: {e}\")\n",
    "\n",
    "    try:\n",
    "        output_df_for_csv.head(10).write_json(\"enriched_orders_sample.json\", row_oriented=True, pretty=True)\n",
    "        print(\"Successfully saved 'enriched_orders_sample.json' (first 10 rows, row-oriented, pretty)\")\n",
    "    except Exception as e:\n",
    "        print(f\"Error saving JSON: {e}\")\n",
    "else:\n",
    "    print(\"merged_inner_df not available. Run the join example first.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 3. Workflow and Execution Notes (Briefly)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Method Chaining Example\n",
    "if 'orders_df' in locals() and 'customers_df' in locals(): \n",
    "    # Assume customers_parsed_reg_dates_df and orders_parsed_dates_df were created in Module 5\n",
    "    # For simplicity, let's ensure our main dfs have parsed dates if we use them.\n",
    "    customers_df_final = customers_df.with_columns([\n",
    "        pl.coalesce([\n",
    "            pl.col(\"registration_date_str\").str.to_date(format=\"%Y-%m-%d\", strict=False),\n",
    "            pl.col(\"registration_date_str\").str.to_date(format=\"%m/%d/%Y\", strict=False)\n",
    "        ]).alias(\"registration_date\"),\n",
    "        # Ensure age_cleaned is present\n",
    "        pl.when(pl.col(\"age\") > 100).then(None).otherwise(pl.col(\"age\")).cast(pl.Int64, strict=False).alias(\"age_cleaned\")\n",
    "    ])\n",
    "    \n",
    "    orders_df_final = orders_df.with_columns([\n",
    "        pl.col(\"order_date_str\").str.to_datetime(format=\"%Y-%m-%d %H:%M:%S\", strict=False).alias(\"order_datetime\"),\n",
    "        pl.col(\"quantity\").fill_null(0), # Fill null quantities for calculation\n",
    "        pl.col(\"unit_price\").fill_null(0.0) # Fill null prices for calculation\n",
    "    ])\n",
    "\n",
    "    # Chained operation: Find total sales amount for customers in 'New York' for 'Electronics' orders\n",
    "    ny_electronics_sales = (\n",
    "        orders_df_final\n",
    "        .join(customers_df_final, on=\"customer_id\", how=\"inner\")\n",
    "        .filter(\n",
    "            (pl.col(\"city\") == pl.lit(\"New York\")) & \n",
    "            (pl.col(\"product_category\") == pl.lit(\"Electronics\"))\n",
    "        )\n",
    "        .with_columns(\n",
    "            (pl.col(\"quantity\") * pl.col(\"unit_price\")).alias(\"sale_amount\")\n",
    "        )\n",
    "        .group_by(\"name\") # Group by customer name\n",
    "        .agg(\n",
    "            pl.sum(\"sale_amount\").alias(\"total_spent_on_electronics\")\n",
    "        )\n",
    "        .sort(\"total_spent_on_electronics\", descending=True)\n",
    "    )\n",
    "    print(\"\\nChained operations - NY Electronics Sales by Customer:\")\n",
    "    print(ny_electronics_sales)\n",
    "else:\n",
    "    print(\"Ensure orders_df and customers_df are loaded for chaining example.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Lazy Execution and .collect() Revisited\n",
    "if 'orders_df' in locals():\n",
    "    lazy_result_plan = (\n",
    "        orders_df.lazy() # Start a lazy query\n",
    "        .filter(pl.col(\"unit_price\") > pl.lit(500))\n",
    "        .group_by(\"product_category\")\n",
    "        .agg(pl.sum(\"quantity\").alias(\"total_high_value_quantity\"))\n",
    "    )\n",
    "    print(\"\\nLazy query plan constructed (no execution yet):\")\n",
    "    print(lazy_result_plan) \n",
    "\n",
    "    print(\"\\nExecuting lazy query with .collect():\")\n",
    "    final_high_value_summary_df = lazy_result_plan.collect()\n",
    "    print(final_high_value_summary_df)\n",
    "else:\n",
    "    print(\"orders_df not loaded for lazy execution example.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "---",
    "\n",
    "## Active Learning Exercises\n",
    "\n",
    "Complete the following tasks to practice the concepts learned in this lesson. Use the `customers_df` and `orders_df` DataFrames.\n",
    "\n",
    "**Instructions:**\n",
    "1. Ensure `customers.csv` and `orders.csv` are loaded into `customers_df` and `orders_df` respectively. You may need to re-run cells from Module 2 if you've restarted your notebook.\n",
    "2.  Remember to handle data type conversions (especially for dates) and missing values where appropriate for accurate analysis.\n",
    "3.  Write your Polars code in the cells provided below each task."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Task 1: Customer Age Analysis\n",
    "\n",
    "a. From `customers_df`, identify and count how many customers have an `age` recorded as the outlier value (3000).\n",
    "b. Create a new DataFrame `customers_age_cleaned_df` where ages greater than 100 are replaced with `None` (null). Then, fill any remaining null ages (including the newly created ones) with the median age of the *cleaned* distribution.\n",
    "c. What is the average `age` (from your cleaned and filled data) of customers registered in the year 2023? (Hint: You'll need to parse `registration_date_str` first)."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Task 1a: Count outlier ages\n",
    "print(\"Task 1a:\")\n",
    "# Your code here\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Task 1b: Clean and fill ages\n",
    "print(\"\\nTask 1b:\")\n",
    "# Your code here\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Task 1c: Average age of customers registered in 2023\n",
    "print(\"\\nTask 1c:\")\n",
    "# Your code here\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Task 2: Order Insights\n",
    "\n",
    "a. In `orders_df`, calculate the actual discount amount for each order (unit_price * quantity * discount_applied). Store this in a new column called `discount_value`. Handle cases where `discount_applied` or `quantity` might be null (assume 0 for discount_applied and 1 for quantity if null for this calculation).\n",
    "b. Which `product_category` has the highest total `discount_value` given to customers?\n",
    "c. For orders placed in Q1 (January, February, March) of any year, what is the count of unique customers?"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Task 2a: Calculate discount_value\n",
    "print(\"Task 2a:\")\n",
    "# Your code here\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Task 2b: Product category with highest total discount_value\n",
    "print(\"\\nTask 2b:\")\n",
    "# Your code here\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Task 2c: Unique customers in Q1 orders\n",
    "print(\"\\nTask 2c:\")\n",
    "# Your code here\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Task 3: Combined Analysis & Output\n",
    "\n",
    "a. Join `orders_df` (after any necessary cleaning/preparation from Task 2, e.g., having `discount_value`) with `customers_df` (using cleaned age data from Task 1, `customers_age_cleaned_df`).\n",
    "b. From the joined DataFrame, find the top 3 cities by total sales revenue (quantity * unit_price - discount_value). For simplicity, if `discount_value` isn't readily available from Task 2a, you can calculate total revenue as (quantity * unit_price) and ignore discounts for this specific sub-task, but note this assumption.\n",
    "c. Save the resulting summary (city and total sales revenue) from Task 3b to a CSV file named `city_revenue_report.csv`."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Task 3a: Join cleaned orders and customers data\n",
    "print(\"Task 3a:\")\n",
    "# Your code here\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Task 3b: Top 3 cities by total sales revenue\n",
    "print(\"\\nTask 3b:\")\n",
    "# Your code here\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Task 3c: Save city revenue report\n",
    "print(\"\\nTask 3c:\")\n",
    "# Your code here\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "---",
    "\n",
    "End of Notebook. Remember to submit your completed tasks."
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3 (ipykernel)",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.10.0"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 5
}
```

-----

**Key considerations for this notebook:**

  * **File Paths:** The `pl.read_csv('customers.csv')` assumes the CSV files are in the same directory as the notebook or in Colab's root session storage after upload.
  * **Sequential Execution:** The notebook is designed to be run sequentially. Variables (DataFrames) created in earlier cells are used in later ones. Students should be reminded of this.
  * **Error Handling in `read_csv`:** Basic `try-except` blocks are included for file loading to guide students if files are missing.
  * **"Your turn" / "TODO":** These are placeholders. You might want to add more specific hints or break down these "Your turn" sections further if needed.
  * **DataFrame States:** I've tried to manage the state of `customers_df` and `orders_df` (e.g., `age_cleaned`, parsed dates) by either re-applying transformations where needed for an example or by referencing a previously created DataFrame like `customers_cleaned_age_df`. This is important for the "Active Learning Exercises" so students know which version of the DataFrame to start with for a given task. You might want to explicitly tell students, e.g., "For Task 1, use the original `customers_df` loaded in Module 2." or "Use the `customers_cleaned_age_df` you created in Module 5."
  * **JSON Output for `product_inventory.json`:** The notebook now includes a cell to create this sample JSON file so students can run the `pl.read_json()` example.
  * **Clarity of Renamed Columns:** I've maintained the `_str` suffix for date columns that are initially read as strings, and then new columns (e.g., `registration_date`, `order_datetime`) are created when they are parsed. This should help avoid confusion.

This notebook structure provides a comprehensive walkthrough of the Polars concepts with hands-on opportunities and a final set of exercises to consolidate learning. You can copy and paste this JSON structure into a new file and save it with an `.ipynb` extension, or use tools like `nbformat` in Python to create the notebook programmatically.