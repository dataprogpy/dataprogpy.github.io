# Modeling Structures

**Part 3: Modeling Structures - Organizing Related Information**

We've seen how to represent basic pieces of information (`str`, `int`, `float`, `bool`, `None`) and name them using variables. But data rarely exists in isolation. Information is usually related, comes in collections, or requires specialized tools (like handling dates). This section introduces Python's fundamental **data structures** for organizing related information and how we access external code tools using `import`.

**3.1 Importing Modules: Accessing More Tools**

Python itself provides the basic building blocks, but much of its power, especially for data analysis, comes from **modules** and **packages** (often called **libraries**). These are collections of pre-written code created by other developers that provide specialized functions and data types.

Think of Python's built-in features as your standard toolbox. Modules are like specialized toolsets you can bring into your workshop when you need them – perhaps a set for advanced math, another for plotting, another for web access, or, importantly for us, tools for handling dates and times or performing large-scale data manipulation.

To use the tools (functions, classes, etc.) from a module or library, you must first **`import`** it into your current Colab session.

**How to Import:**

1.  **`import library_name`**: This brings the entire library into your session's memory. To use something *from* the library, you typically prefix it with the library's name followed by a dot (`.`).

    ```python
    import math # Import Python's built-in math module

    # Use the sqrt function *from* the math module
    number = 25
    square_root = math.sqrt(number)
    print(f"The square root of {number} is {square_root}")
    ```

2.  **`import library_name as alias`**: This is extremely common, especially for libraries with long names or standard abbreviations. It imports the library but assigns it a shorter, convenient nickname (**alias**) that you use instead of the full name. This makes your code less verbose and often follows community conventions.

    ```python
    # Import Python's built-in datetime module, using the standard alias 'dt'
    import datetime as dt

    # Use the 'date' object type and the 'today()' method from the datetime module, via the alias
    todays_date = dt.date.today()
    print(f"Today's date is: {todays_date}")

    # Access attributes of the date object
    print(f"The current year is: {todays_date.year}")
    print(f"The current month is: {todays_date.month}")
    print(f"The current day is: {todays_date.day}")
    ```

**Where Imports Go:** By convention, all `import` statements are usually placed at the **very top** of a notebook or script. This clearly declares all the external tools your code relies on. We'll use `import library as alias` frequently for `polars`, `altair`, `sklearn`, and `datetime`.

**3.2 Lists (`list`): Ordered, Changeable Sequences**

**Modeling Motivation:** How do you represent a collection of related items where the order is important?

* A sequence of steps in a process.
* Monthly sales figures recorded in chronological order.
* A list of customer names, perhaps in the order they were acquired.
Python's solution is the **list**.

A list is an **ordered sequence** of items. The items can be of any data type (though often they're homogeneous, meaning all the same type), and the order in which you add them is preserved.

**Syntax:** Create lists using square brackets `[]`, with items separated by commas.

```python
# A list of region names (strings)
regions = ["North", "South", "East", "West"]

# A list of quarterly sales figures (floats)
quarterly_sales = [15000.50, 18200.00, 14100.75, 19500.20]

# A list can technically mix types (but often less useful for data analysis)
mixed_info = ["Product A", 100, 49.99, True]

# An empty list, ready to be filled later
tasks = []

print(regions)
print(quarterly_sales)
```

**Accessing Items by Index:** Because lists are ordered, you retrieve items using their numerical position, called an **index**. **Crucially, Python indexing starts from 0!**

```python
first_region = regions[0]         # Index 0 is the FIRST item
sales_q2 = quarterly_sales[1]     # Index 1 is the SECOND item
last_region = regions[-1]         # Index -1 is the LAST item
second_last = regions[-2]     # Index -2 is the second-to-last

print(f"First region: {first_region}")
print(f"Q2 Sales: {sales_q2}")
print(f"Last region: {last_region}")
```
*(Trying to access an index outside the list's range will cause an `IndexError`.)*

**Slicing (Getting Sub-Lists):** Extract a portion using `[start:stop]`. The `start` index is included, but the `stop` index is *excluded*.

```python
# Get the 'South' and 'East' regions (index 1 up to, but not including, index 3)
middle_regions = regions[1:3]
print(f"Middle regions: {middle_regions}") # Output: ['South', 'East']

# Get from the beginning up to index 2 (exclusive)
first_two = regions[:2]
print(f"First two: {first_two}") # Output: ['North', 'South']

# Get from index 2 to the end
last_two = regions[2:]
print(f"Last two: {last_two}") # Output: ['East', 'West']
```

**Mutability (Changeable):** Lists are **mutable**, meaning you can change their contents after creation.

* **Change an item:** Use index assignment.
    ```python
    print(f"Original Q1 Sales: {quarterly_sales[0]}")
    quarterly_sales[0] = 15500.00 # Corrected Q1 sales
    print(f"Updated Q1 Sales: {quarterly_sales[0]}")
    ```
* **Add an item to the end:** Use the `.append()` method.
    ```python
    regions.append("Central")
    print(f"Regions after adding Central: {regions}")
    ```

**Getting the Length:** Use the built-in `len()` function.

```python
num_regions = len(regions)
print(f"Number of regions: {num_regions}")
```
Lists are fundamental for holding ordered collections of data that might need modification.

**3.3 Tuples (`tuple`): Ordered, Unchangeable Records**

**Modeling Motivation:** What if you want to group related pieces of information about a *single thing*, where the order matters, but the structure itself shouldn't change?
* An (X, Y) coordinate point.
* An RGB color value `(red, green, blue)`.
* A basic record for a person: `(name, age, registration_date)`.
Python provides **tuples** for this.

A tuple is also an **ordered sequence**, much like a list.

**Syntax:** Usually created using parentheses `()`, with items separated by commas. (Parentheses are often optional but recommended for clarity).

```python
# Tuple representing (x, y) coordinates
point = (150, 75)

# Tuple representing a database record (ID, status, timestamp)
# We need datetime for this example! Make sure 'import datetime as dt' ran earlier.
import datetime as dt # Place imports at the top usually, but here for example context
record_status = (101, "Processed", dt.datetime.now())

# A tuple requires a comma even for one item!
single_value_tuple = ("UniqueValue",)

# Empty tuple
empty_tuple = ()

print(point)
print(record_status)
print(single_value_tuple)
```

**Accessing Items by Index:** Same zero-based indexing as lists.

```python
x_coordinate = point[0]
status = record_status[1]
print(f"X Coordinate: {x_coordinate}")
print(f"Record Status: {status}")
```

**Immutability (Unchangeable):** This is the **defining characteristic and key difference** from lists. Tuples are **immutable**. Once created, you *cannot* change, add, or remove elements.

```python
# These lines will cause an ERROR if you try to run them!
# point[0] = 160  # Cannot change item assignment
# record_status.append(True) # Tuples have no .append() method
```

**Why Use Tuples?**

* **Data Integrity:** Signals that this group of items represents a fixed record or structure that shouldn't be altered.
* **Performance:** Can be slightly more memory-efficient and sometimes faster to process than lists (though usually not a major factor for basic usage).
* **Dictionary Keys:** Tuples can be used as keys in dictionaries (see next section) because they are immutable; lists cannot.

**Common Use Case: List of Tuples**
A very frequent pattern for representing simple tables of data is a **list of tuples**, where each tuple represents one row or record.

```python
# List of tuples: (Product ID, Price, Quantity)
inventory = [
    ("P1001", 49.99, 50),
    ("P1002", 19.50, 120),
    ("P1003", 175.00, 15)
]

# Access the second product record (tuple)
product2_record = inventory[1]
print(f"Product 2 Record: {product2_record}")

# Access the price of the second product
product2_price = inventory[1][1] # Index 1 for the tuple, Index 1 for the price within the tuple
print(f"Product 2 Price: {product2_price}")

# Iterate through the inventory (we'll cover loops soon!)
# for item in inventory:
#   print(f"Processing Product ID: {item[0]}")
```
Use lists when the order matters and the contents might change; use tuples when the order matters but the contents represent a fixed record.

**3.4 Dictionaries (`dict`): Labeled Information (Key-Value Pairs)**

**Modeling Motivation:** Accessing items by numerical position (index) like in lists and tuples isn't always intuitive. How would you model a configuration setting where you want to look up the 'username' or 'timeout_seconds'? Or represent a product where you want to directly access its 'price' or 'brand' using those names? Python's **dictionaries** are perfect for this.

A dictionary stores a collection of **key-value pairs**. Each unique **key** is associated with a specific **value**. Think of it like a real-world dictionary where the word (key) points to its definition (value).

**Syntax:** Dictionaries are created using curly braces `{}`, containing `key: value` pairs separated by commas.
* Keys must be unique and immutable (strings and numbers are common keys; tuples can be keys, but lists cannot).
* Values can be any data type.

```python
# Dictionary modeling configuration settings
config = {
    "server_ip": "192.168.1.100",
    "port": 8080,
    "username": "admin",
    "timeout_seconds": 60,
    "feature_flags": ["feature_a", "feature_c"] # Value can be a list!
}

# Dictionary modeling a student record
student = {
    "student_id": "S5001",
    "name": "Charlie Day",
    "major": "Business Analytics",
    "gpa": 3.75,
    "is_active": True
}

# Empty dictionary
empty_dictionary = {}

print(config)
print(student)
```

**Accessing Values by Key:** You don't use numerical indices. Instead, you use the **key** inside square brackets `[]` to get its associated value.

```python
server = config["server_ip"]
student_name = student["name"]

print(f"Connect to server: {server}")
print(f"Student Name: {student_name}")
```
*(Attempting to access a key that doesn't exist will raise a `KeyError`.)*

**Mutability (Changeable):** Dictionaries are **mutable**. You can add new key-value pairs or change the value associated with an existing key after creation.

```python
# Add a new setting to the config
config["log_level"] = "INFO"
print(f"Config after adding log_level: {config}")

# Update the student's GPA
student["gpa"] = 3.80
print(f"Student record after GPA update: {student}")
```

**Key vs. Index:** This is the fundamental difference in how you retrieve data:
* **Lists/Tuples:** Ordered, access by numerical position (index) -> `my_list[0]`
* **Dictionaries:** Conceptually unordered (though order-preserving in modern Python), access by unique label (key) -> `my_dict['label']`

Dictionaries are incredibly flexible for modeling objects or records where accessing information by a specific name or identifier is important.

