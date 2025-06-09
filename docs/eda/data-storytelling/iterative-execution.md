---
icon: material/numeric-4
---


# Iterative Cleaning and Visualization

Welcome to the heart of the iterative process! In this phase, we'll address the data issues uncovered during our initial visualization attempts. The key idea here is that data cleaning is often **responsive** – driven by the specific needs of your analysis and visualization goals. We clean with a purpose.

## Step 6: Responsive Data Cleaning Task List 🧼📝

Your experience in Step 5 – the errors, the messy charts, the skewed distributions – is now the direct input for creating a **Data Cleaning Task List**. Instead of trying to clean everything imaginable in the dataset upfront (which can be inefficient), we'll prioritize tasks that address the problems blocking us from creating clear, insightful visualizations that answer our analytical questions (which we defined in **Step 2**).

**Why "Responsive" Cleaning? Stay Focused on Your Goals!**

* **Focus and Efficiency:** You tackle problems that *actually matter* for your current goals, rather than spending time on cleaning aspects of the data you may not even use. **Refer back to your persona and analytical questions from Step 2.** If a data imperfection doesn't hinder your ability to answer those specific questions or communicate effectively to that persona, it might be a lower priority or not necessary to fix at all for *this* data story.
* **Contextual Understanding:** Understanding *why* a piece of data is problematic (e.g., "this string needs to be a date for my time-series plot *that answers Sarah's question about market trends*") makes the cleaning process more meaningful.
* **Avoiding Unnecessary Work:** The goal is not to create a "perfect" dataset in isolation, but a dataset that is *fit for the purpose* of your current data story. This helps avoid scope creep in your cleaning efforts.
* **Iterative Improvement:** As you clean and re-visualize, you might uncover deeper issues or realize that your initial cleaning approach wasn't quite right. This iterative loop is key.

**Common Cleaning Tasks Triggered by Visualization "Uh-Ohs":**

Based on the kinds of issues we discussed in Step 5, here are typical cleaning tasks you might identify for our housing dataset. We'll use Polars to perform these tasks in the next step.

1.  **Handling Missing Values (Nulls):**
    * **Observation (Step 5):** A chart fails or looks strange because a key variable used in an encoding (`x`, `y`, `color`) has missing values. Or, `describe()` showed `null_count > 0` for an important column.
    * **Potential Cleaning Tasks:**
        * **Imputation:** Fill missing values with a sensible placeholder (e.g., mean, median for numerical; mode or a special category like "Unknown" for categorical). *Example: If `bathrooms` had a few nulls, we might fill with the median.*
        * **Removal:** If only a very small percentage of rows have missing values in a critical column and imputation is problematic, you *might* consider removing those rows (use with caution).
        * **Flagging:** Create a new boolean column indicating if the original value was missing.
    * **Polars Hint:** `.fill_null()`, `.drop_nulls()`.

2.  **Correcting Data Types:**
    * **Observation (Step 5):** A plot behaves unexpectedly because a numerical variable is treated as a string, or a date variable isn't recognized as such. Your `.schema` check might have hinted at this.
    * **Potential Cleaning Tasks:**
        * **Convert to Numerical:** Change string representations of numbers to `integer` or `float`. *Example: If `price` was accidentally read as a string.*
        * **Parse Dates/Times:** Convert string representations of dates/times into proper datetime objects. This is crucial for time-series analysis or correct sorting.
            * *Example:* Our `date` column (e.g., "20141013T000000") definitely needs parsing from string to a datetime type if we want to analyze trends over time.
        * **Convert to Categorical:** Ensure variables intended as categorical are treated as such (e.g., `zipcode` should often be `pl.Categorical` or `pl.Utf8`, not a number you'd average).
    * **Polars Hint:** `.cast()`, `pl.to_datetime()` (often with a `format` string).

3.  **Feature Engineering (Creating New Variables from Existing Ones):**
    * **Observation (Step 5):** An existing variable isn't quite in the form you need, or a new derived metric could be more insightful.
    * **Potential Cleaning Tasks:**
        * **Extraction:** Pull out parts of a variable. *Example: Extract `year` and `month` from our parsed `date` column to analyze seasonality or yearly trends.*
        * **Calculation:** Create new features from existing ones. *Example: Calculate `price_per_sqft` (`price` / `sqft_liv`) to normalize for size.*
        * **Binning/Discretization:** Convert a continuous numerical variable into a categorical one. *Example: Bin `yr_built` into decades like "1950-1959", "1960-1969", etc., to see price trends by age category.*
        * **Creating Flags:** Make boolean indicators. *Example: Create an `is_renovated` column (`1` if `yr_renov > 0`, else `0`).*
    * **Polars Hint:** `.with_columns()`, string functions (`.str.extract()`, `.str.strptime()`), date functions (`.dt.year()`, `.dt.month()`), mathematical operations, `pl.cut()` for binning.

4.  **Handling Outliers:**
    * **Observation (Step 5):** A few extreme values in a numerical column (e.g., `price`, `sqft_liv`) are "squishing" your plot, making it hard to see patterns in the majority of the data.
    * **Potential Cleaning Tasks (use with caution and transparency):**
        * **Identification:** Use statistical methods (e.g., Z-score, IQR) or visualization (box plots) to identify outliers.
        * **Capping/Winsorizing:** Limit extreme values to a certain percentile (e.g., cap all values above the 99th percentile at the 99th percentile value).
        * **Filtering (for visualization):** Temporarily exclude extreme outliers when generating a specific plot to get a better view of the bulk of the data (ensure you note this exclusion).
        * **Transformation:** Apply mathematical transformations like a log transform (`pl.log()`) to variables like `price`, which can compress the range and make skewed distributions more symmetrical for visualization.
    * **Polars Hint:** `.quantile()`, filtering expressions, mathematical functions.

5.  **String Manipulation / Categorical Variable Cleanup:**
    * **Observation (Step 5):** Categorical labels are inconsistent (e.g., "King County", "king county"), have leading/trailing whitespace, or need simplification.
    * **Potential Cleaning Tasks:**
        * Standardize case (e.g., all lowercase).
        * Trim whitespace.
        * Replace or map values to create consistent categories.
    * **Polars Hint:** `.str.to_lowercase()`, `.str.strip_chars()`, `.replace()`, `.map_dict()`.

**Creating Your Data Cleaning Task List:**

Similar to your visualization task list, document the cleaning tasks you identify. This helps you stay organized and track your progress.

**Example Data Cleaning Task List Entry (with goal alignment in mind):**

| "Uh-Oh" / Observation (from Step 5)                      | Data Variable(s) | Proposed Cleaning Task                                     | Rationale / Why is this needed? (**Link to Step 2 Goal**)                                     | Polars Function(s) (Hint)    | Priority |
| :------------------------------------------------------- | :--------------- | :--------------------------------------------------------- | :-------------------------------------------------------------------------------------------- | :--------------------------- | :------- |
| `date` column is string ("20141013T000000"), cannot plot trend | `date`           | Parse to datetime objects. Extract `year` and `month`.     | Enable time-series analysis for Sarah's question about price changes over time.                 | `pl.to_datetime()`, `.dt.year()` | High     |
| Scatter plot of `price` vs `sqft_liv` is a blob.         | `price`, `sqft_liv`| Try log transformation on `price` and possibly `sqft_liv`. | Reduce skew for better visibility, helping Sarah understand the `price`/`sqft_liv` relationship. | `pl.col().log()`             | High     |
| `yr_renov` has `0` for no renovation. Want a clear flag. | `yr_renov`       | Create `is_renovated` (1 if `yr_renov` > 0, else 0).     | Easier for Sarah to see if renovations impact value when grouping or coloring.              | `.is_greater()`, `.cast()`   | Medium   |
| `bedrooms` has some high outliers (e.g. 33).             | `bedrooms`       | Investigate these outliers. For initial viz, filter to < N. | Prevent extreme outliers from skewing plots or summary stats for typical house comparisons. | `.filter()`                  | Medium   |
| `notes_column` (hypothetical) has messy text.            | `notes_column`   | *No action for now.* | This column is not related to any of Sarah's current questions or our planned visuals.      | N/A                          | Low/None |

???+ activity "**💡 Activity: Draft Your Data Cleaning Task List (Goal-Oriented)**"

    Based on your experiences and observations from **Step 5 (Your First Visualization Attempt & Reality Check)**:

    1.  **Revisit your Step 2 artifacts:** Your audience persona and your list of analytical questions.
    2.  Identify **2-4 specific data cleaning or transformation tasks** that are *directly necessary* to improve your visualizations or enable you to answer your analytical questions more effectively.
    3.  For each task, note:
        * The "Uh-Oh" moment or observation that triggered it.
        * The data variable(s) involved.
        * A brief description of the proposed cleaning task.
        * **How this task aligns with your Step 2 goals or helps your target persona.**
        * (Optional, if you know it) Any Polars functions you think might be useful.
    4.  Organize these into a list or table similar to the example above, paying attention to priority based on your analytical goals.
    This focused list will guide your hands-on data manipulation work in the very next step, ensuring you're investing effort where it counts most for your data story.

## Step 7: Executing Cleaning and Updating Visualizations (Iterative Cycle) 🔄✨

This step is where the "iterative" part of the "Iterative Cleaning and Visualization" phase truly comes alive. You'll move through a cycle:

1.  **Clean:** Implement a cleaning task from your list using Polars.
2.  **Visualize:** Re-generate or create a new visualization with the cleaned data using Altair.
3.  **Assess:** Evaluate the impact of the cleaning and the effectiveness of the visual. Does it answer your question better? Is it clearer? Has it revealed new insights or issues?
4.  **Refine:** Update your data cleaning task list (you might identify new cleaning needs) and your data visualization task list (your visual idea might evolve).

You might go through this cycle multiple times for a single visualization or a set of related ones.

### **1. Executing Cleaning Tasks with Polars**

Let's take some common tasks from the "Example Data Cleaning Task List" in Step 6 and see how we might implement them in Polars. You should adapt these to the specific tasks on *your* list.

**Example Cleaning Task 1: Parse `date` and Extract `year` and `month`**
* **Rationale:** Needed for time-series analysis or grouping by year/month. Our `date` column is a string like "20141013T000000".

```python
import polars as pl
# Assuming housing_df is your Polars DataFrame

# Make sure the 'date' column exists
if 'date' in housing_df.columns:
    try:
        housing_df = housing_df.with_columns(
            # Parse the date string. The "T000000" part is effectively ignored by %Y%m%d
            # if it's not included in the format string.
            # Polars' strptime is robust.
            pl.col('date').str.strptime(pl.Date, format="%Y%m%dT%H%M%S").alias('parsed_date')
        )
        # Now extract year and month from the new 'parsed_date' column
        housing_df = housing_df.with_columns([
            pl.col('parsed_date').dt.year().alias('sale_year'),
            pl.col('parsed_date').dt.month().alias('sale_month')
        ])
        print("Date parsing and feature extraction successful.")
        # print(housing_df.select(['date', 'parsed_date', 'sale_year', 'sale_month']).head())
    except Exception as e:
        print(f"Error during date parsing: {e}")
else:
    print("Column 'date' not found. Skipping date parsing.")
```

**Example Cleaning Task 2: Log Transform `price`**
* **Rationale:** The `price` distribution is likely skewed, making visualizations hard to interpret. A log transform can help normalize it for visual purposes.

```python
# Make sure the 'price' column exists and is numeric
if 'price' in housing_df.columns and housing_df['price'].dtype in [pl.Float32, pl.Float64, pl.Int32, pl.Int64]:
    try:
        housing_df = housing_df.with_columns(
            pl.col('price').log().alias('price_log')
        )
        print("Log transformation of 'price' successful.")
        # print(housing_df.select(['price', 'price_log']).head())
    except Exception as e:
        print(f"Error during log transformation: {e}") # e.g. if price contains 0 or negative values
else:
    print("Column 'price' not found or not numeric. Skipping log transformation.")

```
*Self-correction during cleaning:* If `price` contains 0 or negative values, `log()` will produce nulls or errors. You might need to handle those first (e.g., `pl.when(pl.col('price') > 0).then(pl.col('price').log()).otherwise(None).alias('price_log')`) or filter them out if appropriate for your analysis. This is part of the iterative process!

**Example Cleaning Task 3: Create `price_per_sqft`**

* **Rationale:** A useful metric for comparing value, normalizing for house size.

```python
# Ensure 'price' and 'sqft_liv' exist and are numeric
if 'price' in housing_df.columns and 'sqft_liv' in housing_df.columns and \
   housing_df['price'].dtype in pl.NUMERIC_DTYPES and \
   housing_df['sqft_liv'].dtype in pl.NUMERIC_DTYPES:
    try:
        housing_df = housing_df.with_columns(
            (pl.col('price') / pl.col('sqft_liv')).alias('price_per_sqft')
        )
        # Handle cases where sqft_liv might be 0 to avoid division by zero
        housing_df = housing_df.with_columns(
            pl.when(pl.col('sqft_liv') > 0)
            .then(pl.col('price') / pl.col('sqft_liv'))
            .otherwise(None) # Or some other appropriate fill value
            .alias('price_per_sqft')
        )
        print("Creation of 'price_per_sqft' successful.")
        # print(housing_df.select(['price', 'sqft_liv', 'price_per_sqft']).head())
    except Exception as e:
        print(f"Error creating 'price_per_sqft': {e}")
else:
    print("Columns 'price' or 'sqft_liv' not found or not numeric. Skipping 'price_per_sqft'.")
```

### **2. Vertical Visual Development with Altair (Post-Cleaning)**

Once you've performed a relevant cleaning or transformation task, revisit the visualization that prompted it. Now, you can also focus on **vertical visual development**: building up a single chart layer by layer to enhance its clarity and communicative power.

Let's refine our `price` vs. `sqft_liv` scatter plot from Step 5, assuming we've now created `price_log`.

```python
import altair as alt

# Assuming housing_df now contains 'price_log' and other cleaned columns
if 'price_log' in housing_df.columns and 'sqft_liv' in housing_df.columns:
    # Layer 1: Basic scatter plot with transformed data
    base_scatter = alt.Chart(housing_df).mark_point(opacity=0.3).encode( # Added opacity for overplotting
        x=alt.X('sqft_liv:Q', title='Living Area (sq ft)'),
        y=alt.Y('price_log:Q', title='Log of Sale Price (USD)') # Using log-transformed price
    )

    # Layer 2: Add tooltips for interactivity
    base_scatter_with_tooltips = base_scatter.encode(
        tooltip=[
            alt.Tooltip('price:Q', title='Price', format='$,.0f'), # Show original price in tooltip
            'sqft_liv:Q',
            'bedrooms:O', # :O for Ordinal or discrete numeric
            'bathrooms:Q'
        ]
    )

    # Layer 3: Add a title and adjust properties
    final_scatter_plot = base_scatter_with_tooltips.properties(
        title='Log Price vs. Living Area',
        width=600,
        height=400
    ).interactive() # Enable panning and zooming

    # Layer 4 (Optional): Add a regression line to see the trend
    # Note: Polars DataFrames need to be converted to pandas for transform_regression
    # or you'd pre-calculate regression line data.
    # For simplicity in this example, we'll skip adding a live regression line
    # but you could add a pre-calculated one or use a loess line.
    # Example: final_scatter_plot + final_scatter_plot.transform_loess('sqft_liv', 'price_log').mark_line(color='red')


    # Display the chart
    final_scatter_plot.show()
    print("Refined scatter plot generated.")

else:
    print("Required columns for refined scatter plot are missing.")

```

**Elements of Vertical Development to Consider:**

* **Base Marks and Encodings:** Start with your core data mapping.
* **Transformations:** Apply transformations directly in Altair (e.g., `bin`, `aggregate`, `log` scales on axes) or use pre-transformed data from Polars.
* **Interactivity:** Add `tooltip`s, make charts `.interactive()`.
* **Aesthetics & Clarity:** Adjust `opacity` for overplotting, choose clear `color` schemes, refine `size` and `shape` encodings.
* **Labels and Titles:** Ensure axes have informative titles (`title=`), charts have main titles (`.properties(title=)`), and units are clear (e.g., `axis=alt.Axis(format='$,.0f')`).
* **Annotations:** Add text or line marks to highlight specific points or trends (e.g., `mark_text()`, `mark_rule()`).
* **Legends:** Customize legend titles and appearance for clarity.

### **3. Updating Your Data Visualization Task List (V2, V3, ...)**

As you clean your data and refine your visuals, your understanding deepens. This often leads to changes in your visualization plan:

* **Refine Existing Ideas:** The scatter plot example above was a refinement. You might update your task list to note "use log scale for price," "add opacity."
* **Add New Ideas:** Cleaner data or an insightful plot might spark ideas for new visualizations you hadn't considered. Add them to your list!
* **Discard Unfruitful Ideas:** Sometimes, even after cleaning, a planned visualization doesn't yield useful insights or effectively answer your question. It's okay to remove it from your active list (perhaps with a note why).

Your Data Visualization Task List is a dynamic document. Keep it updated to reflect your evolving strategy.

---

???+ activity "**💡 Activity: Clean, Visualize, Assess, Refine!**"

    1.  **Select 1-2 tasks** from your Data Cleaning Task List (from Step 6).
    2.  **Execute the Cleaning:** Write and run the Polars code to perform these cleaning/transformation tasks on your `housing_df`. Verify the changes (e.g., using `.head()`, `.schema`, or plotting a quick histogram of a transformed column).
    3.  **Re-Visualize / Develop Vertically:**
        * Revisit the Altair visualization that was affected by the cleaning you just performed.
        * Re-generate it using the cleaned data.
        * **Practice vertical development:** Try adding or refining at least two layers to your chart (e.g., improve tooltips, add a title, adjust opacity, change axis formatting, add a color encoding based on another variable).
    4.  **Assess:**
        * How did the cleaning impact the visual? Is it clearer? More insightful?
        * Does the vertically developed chart better guide attention to the key information?
    5.  **Refine Your Task Lists:**
        * Update your Data Cleaning Task List: Mark tasks as complete, or add new ones if your latest visualization attempt revealed further data issues.
        * Update your Data Visualization Task List (V2): Note the refinements made to your chart. Did this spark any new visualization ideas? Should any old ones be modified or removed?
    This cycle is the engine of practical data analysis and storytelling.

---
This step provides concrete examples and guides students through the core iterative loop. It emphasizes both the data manipulation (Polars) and visualization refinement (Altair) aspects.

Next is Step 8: "Creating a Cohesive Set of Visuals (The 'Comic Strip')," which moves into the "horizontal" development of the story.
Okay, we've spent considerable time in the iterative loop of cleaning data and refining individual visualizations (vertical development). You should now have a few well-crafted charts that begin to answer your analytical questions.

Now, let's zoom out a bit. A single chart, no matter how well designed, rarely tells the whole story. The next step is to weave these individual visual pieces into a coherent narrative. This is where "horizontal visual development" comes in.

---
