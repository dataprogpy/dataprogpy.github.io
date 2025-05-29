---
icon: material/numeric-6
---

# **Part 6: Structuring Code - For Reusability and Clarity**

As your analysis tasks become more complex, you'll want ways to organize your code to make it more readable, maintainable, and reusable. Copying and pasting blocks of code is generally a bad practice – if you find a mistake or need to make an update, you have to change it in multiple places! Python offers structures like functions and leverages the concepts of objects and classes to help manage complexity.

## **6.1 Functions: Creating Reusable Commands**

**Motivation:** Imagine you need to perform the same specific calculation (like calculating a standardized score or cleaning text in a particular way) at several different points in your analysis. Instead of writing the same lines of code repeatedly, you can package that logic into a **function**.

* **DRY (Don't Repeat Yourself):** Functions allow you to define a block of code once and then **call** it whenever you need it, potentially with different inputs.
* **Modularity & Readability:** Breaking a large analysis down into smaller, well-named functions makes the overall workflow much easier to understand, test, and debug. Each function handles one specific part of the job.
* **Abstraction:** When you call a function, you focus on *what* it does (e.g., `calculate_profit(revenue, cost)`) rather than getting bogged down in the specific lines of code *how* it does it.

**Defining a Function:**
You create your own functions using the `def` keyword:

```python
def function_name(parameter1, parameter2, ...):
    """Optional: A docstring explaining what the function does.""" # Good practice!
    # --- Indented function body ---
    # Code to perform the function's task goes here.
    # This code only runs when the function is CALLED.
    # It can use the input parameters.
    print(f"Function received: {parameter1}, {parameter2}")
    calculation_result = parameter1 / parameter2 # Example action

    # Use 'return' to send a value back out of the function
    return calculation_result
```

* `def`: The keyword that starts a function definition.
* `function_name`: Give your function a descriptive name (use `snake_case`).
* `(parameters)`: Inside the parentheses are **parameters** – variables that act as placeholders for the inputs the function needs. A function can have zero, one, or many parameters.
* `:`: A colon ends the function definition line.
* **Indented Body:** All the code belonging to the function must be consistently indented (usually 4 spaces).
* `"""Docstring"""`: An optional (but highly recommended) string literal right after the `def` line explaining the function's purpose, parameters, and what it returns.
* `return value`: The `return` statement exits the function and sends a specified `value` back to the part of the code that called it. If `return` is omitted, the function implicitly returns `None`.

**Calling a Function:**
To execute the function's code, you **call** it using its name followed by parentheses `()`, providing the required input values (**arguments**) inside the parentheses.

```python
# Call the function defined above, providing arguments 100 and 5
returned_value = function_name(100, 5) # 100 is passed to parameter1, 5 to parameter2

print(f"The function returned: {returned_value}")
# Expected output based on example def:
# Function received: 100, 5
# The function returned: 20.0
```

---
## 6.2 More on Calling Functions: Understanding Signatures and Argument Passing

When you use functions from libraries (or even your own more complex functions), you'll find that there are specific rules about how you can pass arguments. The "signature" of a function defines the parameters it accepts and how arguments must be provided. Understanding these conventions is crucial for using libraries effectively.

### Function Signatures
A function's signature includes its name and the parameters it can accept. Library designers can enforce certain ways arguments are passed to ensure clarity and correctness.

### Positional and Keyword Arguments
You've already seen positional arguments, where the order matters: `function_name(value1, value2)`. You can also often pass arguments using their parameter names; these are called **keyword arguments**:

```python
def describe_pet(animal_type, pet_name):
    print(f"I have a {animal_type} named {pet_name}.")

describe_pet("hamster", "Harry") # Positional arguments
describe_pet(pet_name="Lucy", animal_type="dog") # Keyword arguments
describe_pet("cat", pet_name="Whiskers") # Mix of positional and keyword
```
Once you use a keyword argument, all subsequent arguments in that call must also be keyword arguments.

### Keyword-Only Arguments
Some functions or methods might be designed so that certain arguments *must* be passed as keyword arguments. This is often used to improve code readability when a function has many parameters, or to make it clear what a particular value represents.

* **How it's defined (for your understanding):**
    A `*` in the function definition indicates that all parameters after it are keyword-only.
    ```python
    def create_user(username, *, preferred_language, send_newsletter):
        print(f"User: {username}, Language: {preferred_language}, Newsletter: {send_newsletter}")
    ```
* **How to call it:**
    You *must* use keywords for `preferred_language` and `send_newsletter`.
    ```python
    create_user("john_doe", preferred_language="English", send_newsletter=True)
    # create_user("john_doe", "English", True) # This would cause an error
    ```
    Library providers might use this to ensure optional configuration parameters are explicitly named.

### Positional-Only Arguments
Less commonly for users to define but possible to encounter, some arguments might be required to be positional *only*.

* **How it's defined (for your understanding):**
    A `/` in the function definition indicates that all parameters before it are positional-only.
    ```python
    def item_price(item_id, quantity, / , currency="USD"):
        print(f"ID: {item_id}, Qty: {quantity}, Currency: {currency}")
    ```
* **How to call it:**
    `item_id` and `quantity` *must* be positional.
    ```python
    item_price(101, 5)
    item_price(102, 3, currency="EUR")
    # item_price(item_id=101, quantity=5) # This would cause an error for item_id and quantity
    ```
    Often, you'll see functions that have a few *required positional arguments* at the beginning, followed by optional keyword arguments.

### Arbitrary Positional Arguments (`*args`)
Sometimes, you want a function to accept an arbitrary number of positional arguments without defining each one. This is where `*args` comes in. Inside the function, `args` becomes a tuple containing all the extra positional arguments.

```python
def print_all_items(fixed_arg, *items):
    print(f"Fixed argument: {fixed_arg}")
    print("Additional items:")
    for item in items:
        print(f"- {item}")

print_all_items("Shopping List", "milk", "eggs", "bread")
# Output:
# Fixed argument: Shopping List
# Additional items:
# - milk
# - eggs
# - bread

print_all_items("Tasks") # No additional items
# Output:
# Fixed argument: Tasks
# Additional items:
```
Many library functions use `*args` to allow you to pass multiple column names, values, or objects.

### Arbitrary Keyword Arguments (`**kwargs`)
Similarly, `**kwargs` allows a function to accept any number of keyword arguments that are not explicitly defined in the function's signature. Inside the function, `kwargs` becomes a dictionary containing these additional keyword arguments.

```python
def configure_system(main_setting, **options):
    print(f"Main setting: {main_setting}")
    print("Other options:")
    for key, value in options.items():
        print(f"- {key}: {value}")

configure_system("active", debug_mode=True, log_level="info", retries=3)
# Output:
# Main setting: active
# Other options:
# - debug_mode: True
# - log_level: info
# - retries: 3

configure_system("inactive")
# Output:
# Main setting: inactive
# Other options:
```
This is extremely common in data science libraries for passing various optional parameters, configurations, or styling attributes to functions and methods (e.g., `polars.read_csv(source, **read_options)`, `altair.Chart(data, **chart_properties)`).

### The Importance of Documentation 📖
Functions in libraries can combine all these types of parameters. For example:
`def complex_function(pos1, pos2, /, pos_or_kw1, *, kw_only1, **kwargs):`

You are **not expected to memorize** the exact signature of every function or method you use. **The most crucial skill is to consult the official documentation** for the library function or method you are calling. The documentation will always specify:

* Required positional arguments.
* Optional arguments and their default values.
* Whether arguments are positional-only, keyword-only, or can be either.
* If `*args` or `**kwargs` are accepted and for what purpose.

---
**Example: Reusable Age Calculation Function**
Let's turn our earlier "Age from Date of Birth" logic into a reusable function. (Remember to `import datetime as dt` usually at the top of your notebook!)

```python
import datetime as dt # Ensure datetime is imported

def calculate_age(date_of_birth):
  """Calculates the current age in years given a date_of_birth.

  Args:
    date_of_birth: A datetime.date object representing the date of birth.
                   Can also be None, in which case None is returned.

  Returns:
    An integer representing the age, or None if date_of_birth was None.
  """
  if date_of_birth is None:
    return None # Handle missing input gracefully

  if not isinstance(date_of_birth, dt.date):
      print("Error: Input must be a datetime.date object.")
      return None # Handle incorrect input type

  today = dt.date.today()
  # Calculate basic year difference
  age = today.year - date_of_birth.year

  # Adjust if the birthday hasn't occurred yet this year
  # Check if (current month, current day) tuple is earlier than (birth month, birth day) tuple
  if (today.month, today.day) < (date_of_birth.month, date_of_birth.day):
    age -= 1 # Subtract 1 if birthday hasn't passed

  return age

# --- Now we can CALL the function easily and repeatedly ---
dob_person1 = dt.date(1988, 10, 25)
dob_person2 = dt.date(2002, 3, 15)
dob_person3 = None

age_person1 = calculate_age(dob_person1)
age_person2 = calculate_age(dob_person2)
age_person3 = calculate_age(dob_person3) # Handles None input

print(f"Person 1 (Born {dob_person1}) Age: {age_person1}")
print(f"Person 2 (Born {dob_person2}) Age: {age_person2}")
print(f"Person 3 (Born {dob_person3}) Age: {age_person3}")

# Easily reuse for another date
print(f"Someone born 1975-07-01 is Age: {calculate_age(dt.date(1975, 7, 1))}")
```
Functions are essential tools for writing cleaner, more organized, and more efficient Python code.

---
## **6.3 A Glimpse into Objects & Classes (Understanding Our Libraries)**

**Motivation Revisited:** Our `calculate_age` function works well, but notice that the function logically "belongs" with the date-of-birth data it operates on. This idea of bundling data together with the functions (actions) that are relevant to that data is the core concept behind **Object-Oriented Programming (OOP)**.

You've already been using objects extensively!

* `"some text"` is an **object** of the `str` type (or class).
* `[10, 20, 30]` is an **object** of the `list` type.
* `dt.date(2025, 5, 2)` creates an **object** of the `date` type from the `datetime` module.

### **Class vs. Object (Blueprint vs. Instance):**

* A **Class** is like a blueprint, template, or category definition. It specifies what *kind* of data (called **attributes**) objects of this class will hold, and what *kind* of actions (called **methods**) they can perform. Examples of classes we've seen: `str`, `int`, `list`, `dict`, `datetime.date`. When we use libraries like Polars, we'll encounter classes like `DataFrame` and `Series`.
* An **Object** is a specific *instance* created from a Class blueprint. Each object holds its own specific data values based on the blueprint. Examples: `my_name = "Bob"` is an object (instance) of the `str` class. `sales_figures = [100, 200]` is an object (instance) of the `list` class. `d1 = dt.date(2025, 5, 2)` is an object (instance) of the `date` class.

### **Attributes and Methods (An Object's Data and Actions):**

Objects typically encapsulate both data and actions. You interact with these using **dot notation (`.`):**

1.  **Attributes:** Data values stored *inside* the object. You access them using `object_name.attribute_name`.

    ```python
    d1 = dt.date(2025, 5, 2)
    print(f"The year attribute of d1 is: {d1.year}")  # Accessing the 'year' attribute
    print(f"The month attribute of d1 is: {d1.month}") # Accessing the 'month' attribute

    # When we use Polars later:
    # some_dataframe = pl.DataFrame(...)
    # print(some_dataframe.shape) # Accessing the 'shape' attribute (rows, cols)
    # print(some_dataframe.columns) # Accessing the 'columns' attribute (list of names)
    ```

2.  **Methods:** Functions that are "attached" to the object and typically operate on the object's data or perform an action related to the object's purpose. You **call** methods using `object_name.method_name(arguments)` – notice the crucial **parentheses `()`** at the end (even if there are no arguments).
    Methods follow the same calling conventions discussed for functions in section 6.1.1, including the use of positional, keyword, `*args`, and `**kwargs` arguments. Always check the method's documentation for specific calling requirements.

    ```python
    my_text = "  needs cleaning  "
    cleaned_text = my_text.strip() # Calling the strip() method ON the my_text string object
    print(f"Cleaned text: '{cleaned_text}'")

    my_numbers = [1, 2]
    my_numbers.append(3) # Calling the append() method ON the my_numbers list object
    print(f"List after append: {my_numbers}")

    # When we use Polars later:
    # filtered_data = some_dataframe.filter(pl.col('A') > 10) # Calling the filter() method
    # sorted_data = some_dataframe.sort('column_name')       # Calling the sort() method
    ```

---
### **The Key Takeaway for This Course (Practical OOP):**

You **do not need to become an expert** in designing or writing your own elaborate classes from scratch in this introductory course.

However, it is **absolutely essential** that you:

1.  Understand the concept that **objects bundle data (attributes) and actions (methods)**.
2.  Become comfortable with the **dot notation syntax** for accessing attributes (`object.attribute`) and calling methods (`object.method()`).
3.  Recognize the different ways arguments can be passed to functions and methods (positional, keyword, `*args`, `**kwargs`) and **prioritize checking library documentation** for their specific requirements.

Why? Because the powerful data science libraries we will rely on – **Polars, Altair, Scikit-learn** – are fundamentally object-oriented. Your entire workflow will involve:

* Creating library objects (DataFrames, Series, Charts, Models, etc.).
* Inspecting their data using attributes (e.g., `my_df.columns`, `my_model.feature_names_in_`).
* Telling them what to do by calling their methods (e.g., `my_df.select(...)`, `my_chart.save(...)`, `my_model.fit(...)`, `my_model.predict(...)`), paying close attention to how arguments need to be supplied.

Grasping this user-level interaction with objects and their methods/attributes, along with understanding function calling conventions, is the "practical OOP" you need to unlock the power of these essential data science tools.