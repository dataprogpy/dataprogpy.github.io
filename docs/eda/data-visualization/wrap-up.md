Okay, let's finalize the lesson plan with **Section 7: Wrap-up & Next Steps**.

This section will consolidate the learning and provide clear paths for students to continue practicing and applying their new skills.

---
## **Section 7: Wrap-up & Next Steps**

---
### **Material for MkDocs Site Content (Page: `07_wrap_up_next_steps.md`)**

```markdown
---
title: Wrap-up & Next Steps
---

Congratulations on completing this introduction to data visualization with Altair! You've learned the fundamental concepts and practical skills to start turning data into insightful visuals.

## Key Concepts Recap

Throughout this session, we've covered:

* **The "Why" of Visualization**: Understanding the importance of visual exploration, as exemplified by Anscombe's Quartet.
* **Altair's Declarative Grammar**:
    * The core structure: `alt.Chart(data).mark_type().encode(...)`.
    * Understanding **marks** (geometric representations) and **encodings** (mapping data to visual properties like `x`, `y`, `color`, `size`, `tooltip`).
    * The importance of data type hinting (`:Q`, `:N`, `:O`, `:T`).
* **Basic Chart Types**: Creating scatter plots, bar charts, line charts, and histograms to answer different types of questions.
* **Enhancing Visualizations**: Customizing charts with titles, axis labels, color schemes, and informative tooltips to improve clarity and impact.
* **Interactive Visualizations**: Adding basic interactivity using selections (`selection_single`, `selection_interval`) and conditional encodings to allow for dynamic data exploration.
* **Data Transformations**:
    * Primarily using **Polars** for robust pre-processing (filtering, deriving columns, aggregating) before visualization.
    * Awareness of Altair's built-in transformations for simpler, chart-specific tasks.
* **Best Practices**: Choosing appropriate charts, aiming for clarity and simplicity, knowing your audience, iterating on your designs, and ethical considerations.

## Connection to Future Topics: Machine Learning

The visualization skills you've developed are foundational for upcoming topics in the course, particularly Machine Learning. You'll find that visualizing your data is crucial for:

* **Exploratory Data Analysis (EDA)** before model building: Understanding feature distributions, identifying correlations, and detecting potential issues like outliers.
* **Feature Engineering**: Visualizing the impact of newly created features.
* **Model Evaluation**: Plotting results from common supervised learning models for regression and classification tasks, such as visualizing prediction errors (residuals), confusion matrices, or ROC curves.
* **Unsupervised Learning**: Visualizing clusters or the results of dimensionality reduction techniques.

Being able to "see" your data and model outputs will greatly enhance your understanding and ability to build effective machine learning models.

## Things to Try: Continue Your Learning Journey!

The best way to solidify your understanding and build confidence is through practice. The starter Jupyter Notebook (`xx_altair_visualization_notebook.ipynb`) contains a list of "Things to Try" exercises. These challenges encourage you to:

* Explore new datasets.
* Recreate charts you find in the wild.
* Experiment deeply with customizations and interactivity.
* Combine your Polars and Altair skills.
* Practice data storytelling.

We strongly encourage you to work through these activities. Don't hesitate to consult the Altair documentation, online resources, or leverage generative AI tools strategically to help you along the way. Remember, the goal is to become comfortable applying these powerful data programming techniques to your own professional challenges and personal projects.
```

---
### **Starter Jupyter Notebook Content (Section 7)**

```python
# {{ Session Name: Data Visualization with Altair }}
# {{ Section 7: Wrap-up & Next Steps }}
```

```python
# ------------------------------------------------------------------------------
# 7.1 Key Concepts Recap
# ------------------------------------------------------------------------------

# Congratulations on making it through this introduction to Altair!
# Let's quickly recap what we've learned:

# - **Why Visualize?**: Anscombe's Quartet showed us that numbers alone aren't enough.
# - **Altair's Core**:
#   - `alt.Chart(data)`: Start with your data.
#   - `.mark_type()`: Choose your geometry (e.g., `mark_point`, `mark_bar`, `mark_line`).
#   - `.encode(...)`: Map data columns to visual channels (`x`, `y`, `color`, `size`, `tooltip`, etc.),
#     remembering data type hints (`:Q`, `:N`, `:O`, `:T`).
# - **Common Charts**: We built scatter plots, bar charts, line charts, and histograms.
# - **Enhancements**: We learned to add titles, customize axes, apply color schemes,
#   and create detailed tooltips.
# - **Interactivity**: We introduced `alt.selection_single()`, `alt.selection_interval()`,
#   and `alt.condition()` to make charts responsive.
# - **Data Transformations**:
#   - **Polars First**: For most filtering, aggregation, and feature creation before plotting.
#   - **Altair's Transforms**: For simpler, on-the-fly chart-specific changes.
# - **Best Practices**: Choosing the right chart, clarity, audience, iteration, and ethical considerations.

# These skills are crucial for turning raw data into actionable insights!
```

```python
# ------------------------------------------------------------------------------
# 7.2 Connection to Future Topics (Machine Learning)
# ------------------------------------------------------------------------------

# The visualization skills you've gained are directly applicable to the Machine Learning
# part of this course. You'll use them for:
#   - Exploratory Data Analysis (EDA) before model training.
#   - Visualizing feature distributions and relationships.
#   - Evaluating model performance (e.g., plotting actual vs. predicted values, residuals for regression;
#     visualizing confusion matrices for classification).
#   - Understanding results from unsupervised techniques like clustering.
```

```python
# ------------------------------------------------------------------------------
# 7.3 Things to Try (Independent Learning Activities)
# ------------------------------------------------------------------------------
# Practice is key to mastering these concepts. Here are some challenges to explore:

# ---
# **Challenge 1: Explore a New Dataset**
# ---
# - Pick a dataset from `vega_datasets` that we haven't used extensively (e.g., `iris`, `seattle_temps`, `barley`, `flights-2k`, `disasters`).
#   You can list available datasets with `dir(vega_data)`.
# - Load it into a Polars DataFrame.
# - Perform some initial EDA with Polars (`.head()`, `.describe()`, `.null_count()`).
# - Try to answer 2-3 interesting questions about the dataset by creating different Altair charts
#   (e.g., "What is the distribution of petal widths in the iris dataset?", "How have disaster counts changed over time?").
# - Apply appropriate customizations (titles, labels, colors).

# Example (loading iris):
# iris_df = pl.from_pandas(vega_data.iris())
# print(iris_df.head())
# YOUR EXPLORATION CODE HERE ...
```

```python
# ---
# **Challenge 2: Recreate a Chart**
# ---
# - Find a simple chart online, in a business report, or a news article.
# - Try to recreate its basic structure and message using Altair and a suitable dataset
#   (either from `vega_datasets` or one you find/create).
# - Focus on matching the chart type, the main encodings, and the overall message.
#   Don't worry about exact cosmetic matches, but aim for functional equivalence.

# YOUR RECREATION CODE HERE ...
```

```python
# ---
# **Challenge 3: Deep Dive into Customization**
# ---
# - Take one of the more complex charts you created during this session (or from Challenge 1).
# - Experiment with advanced customization:
#   - Try different **color schemes** from the Vega-Lite documentation (search "Vega-Lite color schemes").
#     Apply them using `scale=alt.Scale(scheme='...')`.
#   - Customize **axis properties**:
#     - Change tick counts (`tickCount` inside `alt.Axis`).
#     - Rotate labels (`labelAngle` inside `alt.Axis`).
#     - Remove grid lines (`grid=False`).
#   - Customize **legend properties**:
#     - Change legend title (`legend=alt.Legend(title='New Legend Title')`).
#     - Change legend position (`orient='bottom'` or `'top-left'`, etc. inside `alt.Legend`).
#     (Refer to Altair documentation for `alt.Legend` options).

# YOUR CUSTOMIZATION CODE HERE ...
```

```python
# ---
# **Challenge 4: Interactive Exploration with Layering or Concatenation**
# ---
# - Using the `movies_pl` dataset:
#   a. Create an interval selection that allows brushing over 'Rotten_Tomatoes_Rating' (x-axis)
#      AND 'IMDB_Rating' (y-axis) on a scatter plot.
#   b. Create two additional charts:
#      1. A histogram of 'IMDB_Rating'.
#      2. A bar chart showing the count of movies per 'Major_Genre'.
#   c. Link all three charts: The brush on the scatter plot should filter the histogram AND the bar chart.
#      (Hint: Use `.add_params()` on the scatter plot, and `.transform_filter()` on the other two.
#      Then concatenate them using `|` or `&` or `alt.vconcat`/`alt.hconcat`).
#   d. Experiment with how the `empty` parameter of `alt.selection_interval()` (e.g., 'all' vs 'none')
#      changes the initial appearance of the linked charts.

# YOUR INTERACTIVE EXPLORATION CODE HERE ...
```

```python
# ---
# **Challenge 5: Polars + Altair Workflow - Advanced Aggregation**
# ---
# - Using the `cars_pl_reloaded` DataFrame:
#   a. With Polars, calculate the **median** 'Horsepower' and the **average** 'Miles_per_Gallon'
#      for each combination of 'Origin' and 'Cylinders'.
#   b. Filter out groups where the count of cars is less than 5 to ensure robust statistics.
#   c. Create a grouped bar chart (or a heatmap, or faceted scatter plots – be creative!) in Altair
#      to visualize these two metrics. For example, you could have 'Origin' on one axis,
#      'Cylinders' as different colored bars (or facets), and then represent median HP or avg MPG.
#      Think about how to best represent these multiple dimensions.

# YOUR POLARS + ALTAIR WORKFLOW CODE HERE ...
```

```python
# ---
# **Challenge 6: Mini Data Storytelling Snippet**
# ---
# - Choose any dataset and create 1-2 related Altair charts that reveal an interesting insight.
# - Write a short paragraph (3-5 sentences) below your charts in a markdown cell.
#   This paragraph should:
#     - Briefly introduce the data/charts.
#     - Clearly state the main insight or finding your charts illustrate.
#     - Explain how the visual elements in your charts support this insight.
# - This practices the skill of communicating findings from data analysis clearly.

# YOUR CHARTS AND STORYTELLING SNIPPET HERE ...
```

```python
# Remember to consult the Altair documentation: https://altair-viz.github.io/
# And don't hesitate to experiment! The goal is to build practical confidence.

print("\nSession complete! Happy visualizing!")
```

This completes the content for Section 7. The MkDocs page provides a summary and points to the notebook for practice. The notebook offers a detailed recap and a set of "Things to Try" that are designed to reinforce all the key skills from the session, from basic chart creation and customization to interactivity and combining Polars with Altair for more complex tasks, and finally, data storytelling. The references to the course description () are included where appropriate.