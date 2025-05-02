# Modeling Information

**Part 2: Modeling Information - The Basic Building Blocks**

To analyze data, we first need a way to represent that data in our code. We start with the simplest forms of information – basic facts and values – and learn how Python understands them.

**2.1 Literals & Core Data Types: Raw Information**

The most basic way to represent information is by using **literals** – fixed values written directly into your code. Think of these as the raw facts you might start with:

* `"Midtown Office"` (The name of a location - text)
* `255` (The number of employees - a whole number)
* `15750.25` (Monthly rent - a number with decimals)
* `True` (Whether the lease is active - a yes/no status)
* `None` (Perhaps the previous tenant is unknown - representing missing info)

Each of these literals belongs to a fundamental **data type**. The data type tells Python what kind of information it is and what operations are allowed. Here are the core types we'll use constantly:

* **Strings (`str`):** Used for textual data. Enclose text in *either* single quotes (`'...'`) or double quotes (`"..."`).
    ```python
    print("This is a string.")
    print('So is this.')
    print("Employee Name: Alice")
    ```
* **Integers (`int`):** Used for whole numbers (positive, negative, or zero).
    ```python
    print(42)
    print(-100)
    print(0)
    ```
* **Floats (`float`):** Used for numbers that have a decimal component (floating-point numbers).
    ```python
    print(3.14159)
    print(-0.5)
    print(2.0) # Note: Even a .0 makes it a float, not an int
    ```
* **Booleans (`bool`):** Represent logical `True` or `False` values. These are essential for making decisions and comparisons later (Automation!). Note the capitalization.
    ```python
    print(True)
    print(False)
    ```
* **The `None` Type (`NoneType`):** A special type representing the intentional absence of a value. It's often used as a placeholder or to indicate that data is missing or not applicable. There's only one `None` value.
    ```python
    print(None)
    ```

**Checking the Type:**
Python lets you ask what type a specific value (or variable, as we'll see soon) is using the built-in `type()` function. This is useful for understanding your data.

```python
# Try running this cell in Colab!
print( type("Hello") )
print( type(123) )
print( type(98.6) )
print( type(False) )
print( type(None) )
```

*Expected Output:*
```
<class 'str'>
<class 'int'>
<class 'float'>
<class 'bool'>
<class 'NoneType'>
```
Knowing these basic types is the first step toward modeling information effectively in Python.

**2.2 Expressions & Operators: Combining and Comparing Information**

Rarely do we work with just single literal values. We usually need to combine, compare, or perform calculations. We do this using **expressions** and **operators**.

* An **expression** is a piece of code that Python evaluates to produce a value (e.g., `5 + 3` is an expression that evaluates to `8`).
* **Operators** are the special symbols that perform the actions (like `+`, `-`, `*`, `/`).

**Common Operator Types:**

* **Arithmetic Operators:** For math (primarily used with `int` and `float`):
    * `+` Addition (`10 + 5` -> `15`)
    * `-` Subtraction (`10 - 5` -> `5`)
    * `*` Multiplication (`10 * 5` -> `50`)
    * `/` True Division (`10 / 4` -> `2.5`) - *Result is always a float!*
    * `//` Floor Division (`10 // 4` -> `2`) - *Discards the decimal/remainder.*
    * `%` Modulo (`10 % 4` -> `2`) - *Gives the remainder.*
    * `**` Exponentiation (`10 ** 2` -> `100`) - *10 raised to the power of 2.*
* **Comparison Operators:** For comparing two values. These expressions *always* result in a Boolean (`True` or `False`):
    * `==` Equal to (`5 == 5` -> `True`)
    * `!=` Not equal to (`5 != 6` -> `True`)
    * `<` Less than (`5 < 6` -> `True`)
    * `>` Greater than (`5 > 6` -> `False`)
    * `<=` Less than or equal to (`5 <= 5` -> `True`)
    * `>=` Greater than or equal to (`5 >= 6` -> `False`)
    *(These also work for comparing strings alphabetically)*
* **Logical Operators:** For combining `True`/`False` values:
    * `and`: `True and True` -> `True` (Both sides must be True)
    * `or`: `True or False` -> `True` (At least one side must be True)
    * `not`: `not True` -> `False` (Reverses the boolean value)

**Example Combining Operators:**

```python
# Example scenario
revenue = 50000
cost = 35000
target_profit = 10000
is_new_quarter = True

# Calculate profit
profit = revenue - cost

# Check conditions
is_profitable = profit > 0
meets_target = profit >= target_profit
should_review = is_profitable and (not meets_target) and is_new_quarter # Example logic

print(f"Profit: {profit}")
print(f"Is profitable? {is_profitable}")
print(f"Meets profit target? {meets_target}")
print(f"Needs quarterly review? {should_review}")
print(f"Type of 'meets_target': {type(meets_target)}") # Output shows <class 'bool'>
```
Expressions let you derive new information and check conditions based on your basic modeled data.

**2.3 Variables: Naming Information for Reuse**

Hardcoding literal values everywhere makes code hard to read and maintain. If a value needs to change (like a tax rate), you'd have to find and replace it everywhere! More importantly for our "Modeling" approach, we want to give meaningful names to the different pieces of our information model. We achieve this using **variables**.

* **Assignment (`=`):** You create a variable and assign it a value using the single equals sign (`=`).
    `variable_name = value`
* **Analogy:** Think of a variable as a label pointing to a value stored in the computer's memory. You use the label (the variable name) to refer to the value.

**Examples:**

```python
# Modeling details for an employee
employee_id = "EMP102"         # Assigning a string
employee_name = "Bob Johnson"    # Assigning another string
years_with_company = 3          # Assigning an int
hourly_rate = 22.50           # Assigning a float
is_full_time = True           # Assigning a bool

# Using the variables
print(employee_name)
print(hourly_rate)

# Variables can be used in expressions
weekly_pay_estimate = hourly_rate * 40
print(f"Estimated weekly pay for {employee_name}: ${weekly_pay_estimate}")

# Variable values can be updated
years_with_company = years_with_company + 1 # Anniversary!
print(f"{employee_name} now has {years_with_company} years with the company.")
```

**Variable Naming Conventions (Python Best Practice):**

* **Use `snake_case`:** All lowercase letters, with words separated by underscores (e.g., `hourly_rate`, `customer_first_name`). This is the standard convention in the Python community.
* **Be Descriptive:** Choose names that clearly indicate the variable's purpose (`average_score` is better than `avg` or `s`).
* **Rules:** Names must start with a letter or underscore (`_`), followed by letters, numbers, or underscores. They cannot be Python keywords (like `if`, `for`, `True`, `None`, `def`, `class`). Names are case-sensitive (`age` is different from `Age`).

**Comments (`#`): Explaining Your Code**
As you assign variables and write logic, add **comments** using the hash symbol (`#`). Python ignores everything on a line after the `#`. Comments explain the *why* behind your code, making it understandable later.

```python
# Standard hourly rate for entry-level analysts
base_rate = 20.00

# Bonus multiplier for employees with > 2 years experience
experience_bonus_multiplier = 1.1

# Calculate Bob's actual rate (assuming Bob has > 2 years)
actual_rate = base_rate * experience_bonus_multiplier # Apply experience bonus
```
Use comments frequently!

**2.4 Working with Text (`str`): A Closer Look**

Text data (`str`) is fundamental in business (names, addresses, descriptions, logs, etc.) and often needs cleaning or formatting. Python's strings have powerful capabilities.

* **Combining Strings (Concatenation):** Use the `+` operator. Remember to add spaces if needed!
    ```python
    greeting = "Hello"
    name = "Maria"
    message = greeting + ", " + name + "!"
    print(message) # Output: Hello, Maria!
    ```
* **Common String Methods:** Strings are objects with built-in functions called **methods**. You call them using `dot notation`: `your_string_variable.method_name()`. Here are some essentials for data cleaning:
    * `.lower()`: Returns a *new* string with all characters converted to lowercase.
    * `.upper()`: Returns a *new* string with all characters converted to uppercase.
    * `.strip()`: Returns a *new* string with leading and trailing whitespace (spaces, tabs, newlines) removed. Invaluable!
    * `.replace(old, new)`: Returns a *new* string where all occurrences of the `old` substring are replaced with the `new` substring.
    * *(Note: Strings are immutable - these methods return *new* strings; they don't change the original one unless you reassign the variable.)*

**Example Usage:**

```python
raw_city_data = "  New York City \n"
print(f"Original: '{raw_city_data}'")

# Clean up for consistency
cleaned_city = raw_city_data.strip().lower()
print(f"Cleaned: '{cleaned_city}'") # Output: Cleaned: 'new york city'

status = "Status: PENDING"
updated_status = status.replace("PENDING", "COMPLETE")
print(f"Updated Status: '{updated_status}'") # Output: Updated Status: 'Status: COMPLETE'
```

**F-Strings (Formatted String Literals): The Best Way to Combine Strings and Variables**
We've used these in examples already. F-strings provide the clearest and most convenient way to create strings that include the values of variables.

* Start the string with the letter `f` immediately before the opening quote (`f"..."` or `f'...'`).
* Inside the string, place variable names or even simple expressions directly within curly braces `{}`.

```python
product_id = "XYZ789"
price = 19.95
discount_pct = 10 # As percentage points

# Using f-string for clear output
output_message = f"Product {product_id} costs ${price:.2f}. Current discount: {discount_pct}%"
print(output_message)
# Output: Product XYZ789 costs $19.95. Current discount: 10%

# You can include calculations inside the braces
discounted_price = price * (1 - discount_pct / 100)
print(f"Discounted price for {product_id}: ${discounted_price:.2f}")
# Output: Discounted price for XYZ789: $17.95
```
*(Note: The `:.2f` inside the braces is special formatting to show the float with exactly two decimal places - useful for currency!)*

Get comfortable with basic string operations and f-strings; you'll use them constantly when preparing and presenting data.
