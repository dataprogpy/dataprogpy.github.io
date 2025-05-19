--- 
icon: material/numeric-3
---

# **Kernel State**

Understanding this section is crucial for working effectively with Colab (and any Jupyter-style notebook). It addresses the most common source of confusion for newcomers: the difference between the order of cells on your screen and the order in which the computer actually executes your commands.

## **Execution Order**
 **It's Not Just Top-to-Bottom: Understanding Execution Flow**

Unlike a simple text document or a traditional computer script that typically runs line-by-line from start to finish, a Colab notebook is more interactive and **stateful**. This means the notebook's 'memory' or 'state' (like the values of variables you create) depends entirely on the **sequence in which you have executed the cells**, not just their top-to-bottom arrangement on the page.

### **The Common Pitfall**
Imagine you write some code, run it, then scroll back up, change an earlier cell, and run *only that changed cell*. If you then continue running cells further down *without* re-running the intermediate steps, those later cells might use *outdated information* from before you made your change! This leads to results that don't seem to make sense based on the code you *see*.

**Let's See it in Action (Example):**

Consider these three simple code cells in your notebook:

*(Code Cell 1)*
```python
# Define an initial price for an item
price = 100
print(f"Cell 1 Executed: Initial price is currently {price}")
```

*(Code Cell 2)*
```python
# Calculate a 10% tax based on the current price
tax = price * 0.10
print(f"Cell 2 Executed: Tax calculated is currently {tax}")
```

*(Code Cell 3)*
```python
# Calculate the final cost
final_cost = price + tax
print(f"Cell 3 Executed: Final cost is currently {final_cost}")
```

**Scenario 1: Running in Order (The Normal Flow)**

1.  Run Cell 1 -> Output includes `price is currently 100`
2.  Run Cell 2 -> Output includes `Tax calculated is currently 10.0`
3.  Run Cell 3 -> Output includes `Final cost is currently 110.0`
    *This works exactly as you'd expect.*

**Scenario 2: The Pitfall (Running Out of Order)**

1.  Let's say you ran Cells 1, 2, and 3 just like above. The final cost is 110.0.
2.  Now, you decide the price should have been 200. You go back to **Cell 1**, change `price = 100` to `price = 200`, and **run *only* Cell 1**.
    *Output: `Cell 1 Executed: Initial price is currently 200`*
3.  You **skip running Cell 2**.
4.  You jump down and **run Cell 3 again**.
    * **What you might *expect*: 220.0** (because price is now 200, so 200 + 20 tax).
    * **What you actually *get*: `Cell 3 Executed: Final cost is currently 210.0`** (Huh?!)

**Why the difference (210 vs 220)?**
When you re-ran Cell 3, the computer used the *current* value of `price` (which *was* updated to 200 when you re-ran Cell 1). However, it used the value of `tax` that was calculated the *last time Cell 2 was run* (which was 10.0, based on the old price of 100). You never re-ran Cell 2 to update the `tax` variable based on the new price! So Cell 3 calculated `200 + 10.0 = 210.0`.

**Key Takeaway:** The notebook doesn't automatically recalculate everything below a change. You must explicitly re-run all dependent cells to update the notebook's state correctly.

## **The Kernel**

What actually keeps track of variables like `price` and `tax`? It's a background process called the **Kernel**.

* Think of the Kernel as the dedicated Python engine running behind your specific notebook session.
* When you run a code cell, the code is sent to the Kernel.
* The Kernel executes the code and, crucially, **remembers the state** – any variables created (`price`, `tax`), functions defined, libraries imported (`import polars as pl`), etc.
* This memory (state) persists throughout your session based on the *sequence of cell executions*.

## **Hidden State**

The Kernel's memory can sometimes lead to confusing situations, often called **hidden state**. This happens because the Kernel can remember things even if the cell that defined them is no longer visible in your notebook.

**Example:**

1.  In Cell 1, type and run: `temporary_variable = "Important Info"`
2.  In Cell 2, type and run: `print(temporary_variable)` -> Output: `Important Info`
3.  Now, **delete Cell 1** completely from your notebook.
4.  Run Cell 2 again.

**Result:** Surprisingly, Cell 2 might *still work* and print `Important Info`! The Kernel remembers the `temporary_variable` from when Cell 1 *was* executed, even though the defining cell is gone.

This hidden state can make your notebook behave unexpectedly and makes it hard to guarantee that someone else running your notebook (or you, coming back later) will get the exact same results just by reading the visible cells.

## **Managing the Kernel**

Thankfully, Colab provides essential tools to manage the Kernel and reset its state, located in the **Runtime** menu. Mastering these is key to avoiding frustration!

**Key Runtime Options:**

* **`Restart runtime`** (`Runtime` -> `Restart runtime` or shortcut `Ctrl+M .`)
    * **What it does:** Stops the current Kernel and starts a completely fresh one.
    * **Effect:** Clears *all* variables, function definitions, and imported libraries from the Kernel's memory. Locally uploaded files (session storage) might also be cleared. Your *code cells remain*, but the memory is wiped clean.
    * **When to use:** This is your **go-to fix** when the notebook feels stuck, is behaving unpredictably, or you suspect hidden state issues. After restarting, you'll need to re-run necessary cells (like imports, data loading, variable definitions) from the top.
* **`Restart and run all`** (`Runtime` -> `Restart and run all`)
    * **What it does:** First, it restarts the runtime (clearing the state, as above). Then, it automatically starts executing *every single cell* in your notebook sequentially, from the very first cell to the very last.
    * **Why it's crucial:** This perfectly simulates running your notebook from a completely clean slate. It's the **gold standard for checking reproducibility**. Does your notebook run correctly from top to bottom without errors and produce the expected final results?
    * **Recommendation:** **Make it a habit to use `Restart and run all` before submitting any assignment or finalizing an analysis.** It ensures your work is reliable and free from state-related bugs.
* **`Interrupt execution`** (`Runtime` -> `Interrupt execution` or shortcut `Ctrl+M I`)
    * **What it does:** Attempts to stop the code currently being executed by the Kernel (e.g., if a cell is taking unexpectedly long or got stuck in a loop).
    * **When to use:** When a cell seems frozen. It doesn't clear the memory, just stops the current task. You might still need to restart the runtime afterwards if things remain unstable.

**Your Reproducibility Mantra:**

> *"When things act weird, `Restart runtime`. To check my final work, `Restart and run all`."*
