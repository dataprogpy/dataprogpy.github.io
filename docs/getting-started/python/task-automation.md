# Task Automation

**Part 5: Automating Tasks - Making Code Work for You**

We've explored how to use Python's types and data structures to **model** information. Now, let's shift focus to the second anchor concept: **Automation**. How do we instruct the computer to perform tasks repeatedly or make decisions based on the data we've modeled? This is where Python's control flow statements come in.

**5.1 Conditional Logic (`if`, `elif`, `else`)**

**Automation Motivation:** Often, the steps in an analysis or process depend on certain conditions.
* If a customer spends over $100, apply free shipping.
* If a survey response is "Strongly Agree", score it 5 points; if "Agree", score it 4 points.
* If a data value is missing, handle it differently than if it's present.
Conditional statements allow your program to make these kinds of decisions automatically based on whether certain conditions are `True` or `False`.

Python uses the `if`, `elif` (short for "else if"), and `else` keywords for this.

**The Core Idea:**
These statements work by evaluating **boolean expressions** (expressions that result in `True` or `False`, often using the comparison and logical operators we saw in Section 2.2).

* The code block under `if` runs *only if* its condition is `True`.
* If the `if` condition is `False`, Python checks the condition of the first `elif` (if present). If that's `True`, its block runs.
* Python checks subsequent `elif` conditions only if all preceding `if`/`elif` conditions were `False`.
* The `else` block (if present) runs *only if* all the preceding `if` and `elif` conditions were `False`.

**Syntax (Indentation is Crucial!):**
Notice the **colons (`:`)** after each condition and the **consistent indentation** (typically 4 spaces) for the code blocks belonging to each statement. Indentation is how Python groups code blocks.

```python
if some_boolean_condition:
    # This block runs ONLY if some_boolean_condition is True
    print("The if condition was met.")
elif another_boolean_condition:
    # This block runs ONLY if the 'if' was False AND 'another_boolean_condition' is True
    print("The elif condition was met.")
else:
    # This block runs ONLY if BOTH the 'if' AND 'elif' conditions were False
    print("Neither the if nor the elif condition was met.")
```

**Examples:**

```python
# Example 1: Simple If
project_status = "Complete"
if project_status == "Complete":
    print("Archiving project files...")
    # If status wasn't "Complete", this block is just skipped.

# Example 2: If-Else
inventory_count = 35
if inventory_count > 0:
    print(f"Stock available: {inventory_count} units.")
else:
    print("Item is out of stock.")

# Example 3: If-Elif-Else
satisfaction_score = 4 # Score from 1 to 5

if satisfaction_score == 5:
    feedback_category = "Very Satisfied"
elif satisfaction_score == 4:
    feedback_category = "Satisfied"
elif satisfaction_score == 3:
    feedback_category = "Neutral"
else: # Covers scores 1 and 2
    feedback_category = "Needs Attention"

print(f"Feedback Category: {feedback_category}")
```
Conditional logic allows you to create flexible programs that respond dynamically to different data inputs and situations, automating decision-making.

**5.2 Iteration: Repeating Actions**

**Automation Motivation:** Many data tasks involve doing the same thing to multiple pieces of data.

* Applying a calculation to every sales figure in a list.
* Checking the format of every email address in a customer database.
* Processing each row in a data table.
Doing this manually is impractical. **Iteration** allows you to automate these repetitive processes using **loops**.

**`for` Loops: Iterating Over Sequences (The Workhorse)**
The `for` loop is the most common and idiomatic way to iterate in Python, especially when working with data structures like lists, tuples, strings, or dictionaries. It lets you process each item in a collection one by one.

**Syntax:**

```python
for temporary_variable in sequence_or_iterable:
    # This indented code block runs ONCE for EACH item
    # in the 'sequence_or_iterable'.
    # Inside the loop, 'temporary_variable' holds the current item being processed.
    print(f"Current item: {temporary_variable}")

print("Loop finished.")
```
*(Remember the colon `:` and the indentation!)*

**Examples:**

```python
# Iterating over a List
customer_names = ["Alice", "Bob", "Charlie"]
print("--- Sending Welcome Emails ---")
for name in customer_names:
    print(f"Sending email to: {name}")

# Iterating over a String
ticker = "GOOGL"
print("\n--- Ticker Characters ---")
for char in ticker:
    print(char)

# Using range() to loop a specific number of times
# range(n) generates numbers from 0 up to (but not including) n
print("\n--- Processing 3 Batches ---")
for batch_number in range(3): # Generates 0, 1, 2
    print(f"Starting batch number {batch_number + 1}")
    # Code to process the batch would go here

# range(start, stop) generates numbers from start up to (not including) stop
print("\n--- Calculating Interest for Years ---")
initial_balance = 1000
rate = 0.05
for year in range(1, 4): # Generates 1, 2, 3
    interest = initial_balance * rate
    print(f"Year {year} Interest: ${interest:.2f}")
    # In a real scenario, you'd update the balance too

# Iterating over Dictionary Items
product = {"id": "XYZ-123", "name": "Mouse", "price": 29.99}

print("\n--- Product Keys ---")
for key in product: # Looping directly over a dict iterates through its keys
    print(key)

print("\n--- Product Values ---")
for value in product.values(): # Use .values() to get only values
    print(value)

print("\n--- Product Key-Value Pairs ---")
for key, value in product.items(): # Use .items() to get (key, value) tuples
    print(f"{key}: {value}")
```

**`while` Loops: Repeating While a Condition is True**
A `while` loop repeatedly executes its code block *as long as* a specified boolean condition remains `True`. They are less common than `for` loops for simply iterating over data collections but are useful when you don't know in advance how many times you need to loop.

**Syntax:**

```python
while some_boolean_condition:
    # Code block runs repeatedly as long as the condition is True
    print("Condition is still True, looping...")
    # --- VERY IMPORTANT ---
    # Something inside this block MUST eventually make
    # 'some_boolean_condition' False, otherwise you have an infinite loop!
    # --- VERY IMPORTANT ---

print("Condition became False, while loop ended.")
```

**Example (Use with Care):**

```python
# Example: Simulate processing items until a queue is empty (represented by a list)
items_to_process = ["Task A", "Task B", "Task C"]
processed_count = 0

while len(items_to_process) > 0:
    current_item = items_to_process.pop(0) # .pop(0) removes and returns the first item
    print(f"Processing: {current_item}")
    processed_count += 1 # Same as processed_count = processed_count + 1

print(f"\nFinished processing. Total items: {processed_count}")
```
**Warning:** Be cautious with `while` loops. If the condition never becomes `False`, the loop will run forever (an "infinite loop"), and you'll likely need to interrupt the execution (See Section 3.4). For iterating a known number of times or over existing sequences, `for` loops are generally preferred and safer.

Loops are the engine of automation in programming, enabling you to handle large amounts of data and complex repetitive procedures with concise code.
