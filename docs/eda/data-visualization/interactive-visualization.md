Okay, let's dive into **Section 4: Interactive Visualizations**.

Interactivity can significantly enhance the exploratory power of visualizations, allowing users to engage with the data more dynamically.

---
## **Section 4: Interactive Visualizations**

---
### **Material for MkDocs Site Content (Page: `04_interactive_visualizations.md`)**

```markdown
---
title: Interactive Visualizations with Altair
---

Static charts are informative, but interactive charts can transform how users explore and understand data. Altair provides a powerful way to add interactivity through **selections**. Selections allow users to highlight, filter, or otherwise interact with the data points directly on the chart.

## The Concept of Selections

A selection in Altair defines how the user can interact with the chart. There are several types, but we'll focus on two common ones:

* `alt.selection_single()`: Allows the user to select a single data point (or a group if `fields` or `encodings` are specified) by clicking.
    * `fields`: Specify data fields that define the scope of the selection. Clicking a point selects all other points with the same values in these fields.
    * `encodings`: Similar to `fields`, but uses encoding channels (e.g., 'x', 'color').
    * `on`: Event to trigger selection (e.g., 'click', 'mouseover'). Defaults to 'click'.
    * `empty`: How to treat the selection when no items are selected ('all' or 'none'). Defaults to 'all'.
* `alt.selection_interval()`: Allows the user to select a range of data points by clicking and dragging to draw a rectangular brush.
    * `encodings`: Specify encoding channels (typically 'x' and/or 'y') to define the selection interval.
    * `empty`: Defaults to 'all'.
* `alt.selection_point()` (formerly `selection_multi`): Allows selection of multiple discrete points, typically by shift-clicking.

You first define a selection and give it a name, then you can use this named selection to control aspects of your chart.

```python
# Define a single-click selection
click_selection = alt.selection_single(
    fields=['Category'], # Selects all items of the same category on click
    empty='none' # No items selected initially means nothing is "selected"
)

# Define an interval selection (brushing)
interval_brush = alt.selection_interval(
    encodings=['x'], # Brush along the x-axis
    empty='all' # Initially, all data is considered selected
)
```

## Using Selections

Once a selection is defined and added to a chart using `.add_params()`, its state (e.g., which data points are selected) can be used to:

1.  **Conditionally Encode Properties**: Change visual properties (like color, size, opacity) of marks based on whether they are part of the selection. This is done using `alt.condition()`.
2.  **Filter Data**: Display only the selected data in the current chart or in other linked charts.

### 1. Conditional Encodings

`alt.condition(selection, value_if_selected, value_if_not_selected)` is the key here.

**Example: Highlighting selected points**
Change the color and opacity of points based on a single click selection.

```python
# selection_name = alt.selection_single(empty='none')
#
# alt.Chart(data).mark_point().encode(
#     x='x_field:Q',
#     y='y_field:Q',
#     color=alt.condition(selection_name, 'highlight_color:N', alt.value('lightgray')),
#     opacity=alt.condition(selection_name, alt.value(1.0), alt.value(0.3))
# ).add_params(
#     selection_name
# )
```
In this example, points included in `selection_name` will get `highlight_color` and full opacity, while others will be light gray and semi-transparent.

### 2. Filtering Data (Linked Views - Basic)

Selections can also be used to filter data. The `.transform_filter(selection_name)` method is used for this. This is powerful for creating linked views where interacting with one chart affects another.

**Example: A scatter plot filters a bar chart (conceptual)**
Imagine a scatter plot where you select points. A nearby bar chart could then update to show data only related to those selected points.

```python
# brush = alt.selection_interval() # Define a brush selection
#
# scatter = alt.Chart(data).mark_point().encode(
#    x='X:Q', y='Y:Q'
# ).add_params(brush)
#
# bars = alt.Chart(data).mark_bar().encode(
#    x='Category:N', y='average(Value):Q'
# ).transform_filter(
#    brush # Filter bars based on the brush selection in the scatter plot
# )
#
# scatter & bars # Display side-by-side
```
For an introductory session, we'll focus primarily on conditional encodings within a single chart, as linked views can add complexity quickly.

## Focus on Simple, Meaningful Interactions

The goal of adding interactivity is to make the data exploration process more intuitive and insightful.
* Start with simple interactions like highlighting.
* Ensure the interaction has a clear purpose and helps answer a question or reveal a pattern.
* Avoid overly complex interactions that might confuse the user.

Interactive charts are particularly powerful for:
* Drilling down into specific data points.
* Comparing subsets of data dynamically.
* Exploring relationships that might not be obvious in a static view.
```

---
### **Starter Jupyter Notebook Content (Section 4)**

```python
# {{ Session Name: Data Visualization with Altair }}
# {{ Section 4: Interactive Visualizations }}

# Ensure libraries from previous sections are loaded
# import altair as alt
# import polars as pl
# from vega_datasets import data as vega_data
# alt.renderers.enable('colab')

# Load the 'cars' dataset again for these examples
cars_pd = vega_data.cars()
cars_pl = pl.from_pandas(cars_pd).drop_nulls(
    subset=['Horsepower', 'Miles_per_Gallon', 'Origin', 'Name', 'Cylinders']
)

# And the 'movies' dataset for a different example
movies_pd = vega_data.movies()
movies_pl = pl.from_pandas(movies_pd).drop_nulls(
    subset=['IMDB_Rating', 'Rotten_Tomatoes_Rating', 'Major_Genre', 'Release_Date']
).with_columns(
    pl.col("Release_Date").str.to_datetime().dt.year().alias("Release_Year")
)
```

```python
# ------------------------------------------------------------------------------
# 4.1 Defining Selections: `selection_single` and `selection_interval`
# ------------------------------------------------------------------------------
# Selections define how users can interact with the chart.

# Example 1: Single point selection (e.g., on click)
# We'll use this to highlight a point or a group of related points.
# `empty='none'` means if nothing is explicitly selected, the selection is empty.
# `empty='all'` means if nothing is explicitly selected, everything is considered part of the selection.
cars_origin_selection = alt.selection_point( # selection_point can select multiple points with shift-click
    fields=['Origin'], # Clicking a point will select all points with the same 'Origin'
    bind='legend', # Bind selection to legend clicks as well
    empty='all' # if nothing is selected, all are considered "selected" for initial state
)


# Example 2: Interval selection (brushing)
# This allows selecting a range of data by dragging a rectangle.
# We can specify which channels the brush applies to (e.g., 'x', 'y', or both).
rating_brush = alt.selection_interval(
    encodings=['x'], # Allow brushing along the x-axis (IMDB Rating)
    empty='all'
)
```

```python
# ------------------------------------------------------------------------------
# 4.2 Using Selections: Conditional Encodings for Highlighting
# ------------------------------------------------------------------------------
# We use `alt.condition(selection, value_if_true, value_if_false)`
# to change visual properties based on selection status.

# Activity 1: Highlight cars by 'Origin' on click (using the legend binding)
# Create a scatter plot of Horsepower vs. Miles_per_Gallon.
# Points belonging to the selected 'Origin' (via legend or if fields was bound to plot) will be fully opaque,
# others will be semi-transparent.

scatter_highlight_origin = alt.Chart(cars_pl).mark_circle(size=80).encode(
    x='Horsepower:Q',
    y='Miles_per_Gallon:Q',
    color=alt.Color('Origin:N', legend=alt.Legend(title='Click Legend to Select Origin')),
    opacity=alt.condition(cars_origin_selection, alt.value(0.9), alt.value(0.1)), # 0.9 if selected, 0.1 if not
    tooltip=['Name:N', 'Origin:N', 'Horsepower:Q', 'Miles_per_Gallon:Q']
).add_params(
    cars_origin_selection # Add the selection definition to the chart
).properties(
    title='Click Legend: Highlight Cars by Origin',
    width=500,
    height=300
)

print("\nScatter Plot with Legend-based Highlighting:")
scatter_highlight_origin
# Try clicking on 'USA', 'Europe', 'Japan' in the legend.
```

```python
# Activity 2: Using an interval selection to change color
# Scatter plot of IMDB Rating vs. Rotten Tomatoes Rating for movies.
# Use `rating_brush` to select a range of IMDB ratings.
# Selected points will be colored 'steelblue', others 'lightgray'.

movies_scatter_brush_color = alt.Chart(movies_pl.sample(n=500, seed=42)).mark_circle(size=60).encode(
    x=alt.X('IMDB_Rating:Q', scale=alt.Scale(domain=[0,10])),
    y=alt.Y('Rotten_Tomatoes_Rating:Q', scale=alt.Scale(domain=[0,100])),
    color=alt.condition(rating_brush, alt.value('steelblue'), alt.value('lightgray')),
    tooltip=['Title:N', 'IMDB_Rating:Q', 'Rotten_Tomatoes_Rating:Q', 'Major_Genre:N']
).add_params(
    rating_brush # Add the interval selection
).properties(
    title='Brush on X-axis (IMDB Rating) to Highlight Movies',
    width=500,
    height=350
)

print("\nScatter Plot with Interval Brush for Color Change:")
movies_scatter_brush_color
# Try clicking and dragging along the x-axis (IMDB Rating).
```

```python
# Student Task: Conditional Sizing
# 1. Create a new single point selection called `click_select_point`. This time, don't specify `fields` or `encodings`.
#    This means it will select individual data points on click. Set `empty='none'`.
# 2. Create a scatter plot of 'Acceleration' vs. 'Weight_in_lbs' from the `cars_pl` dataset.
# 3. Use `alt.condition()` with `click_select_point` to make the clicked point `size=200` and other points `size=50`.
# 4. Color points by 'Cylinders:O' (Ordinal, as it has an order).
# 5. Add tooltips for 'Name', 'Acceleration', 'Weight_in_lbs', 'Cylinders'.
# 6. Add the selection to the chart and give it an appropriate title.

# YOUR CODE HERE for Student Task (Conditional Sizing)
# Example Solution:
# click_select_point = alt.selection_point(empty='none') # Note: selection_point instead of selection_single for multi-select with shift

# scatter_conditional_size = alt.Chart(cars_pl).mark_circle(opacity=0.7).encode(
#     x='Acceleration:Q',
#     y='Weight_in_lbs:Q',
#     color='Cylinders:O', # Ordinal for color scale if desired, or Nominal
#     size=alt.condition(click_select_point, alt.value(200), alt.value(50)),
#     tooltip=['Name:N', 'Acceleration:Q', 'Weight_in_lbs:Q', 'Cylinders:O']
# ).add_params(
#     click_select_point
# ).properties(
#     title='Click a Point to Enlarge It',
#     width=500,
#     height=350
# )
# scatter_conditional_size
```

```python
# ------------------------------------------------------------------------------
# 4.3 Using Selections: Filtering Data (Simple Linked View - Optional/Advanced)
# ------------------------------------------------------------------------------
# Selections can filter data for the same chart or other charts.
# `.transform_filter(selection_name)`

# Example: A bar chart showing average MPG for car origins,
# linked to a scatter plot where you can select origins via the legend.
# We will reuse `cars_origin_selection` which is bound to the legend.

# Scatter plot (acts as the controller via legend clicks)
source_data = cars_pl.filter(pl.col('Miles_per_Gallon').is_not_null())

# Ensure cars_origin_selection is defined (from earlier cell)
# cars_origin_selection = alt.selection_point(fields=['Origin'], bind='legend', empty='all')

scatter_controller = alt.Chart(source_data).mark_circle(size=80).encode(
    x='Horsepower:Q',
    y='Miles_per_Gallon:Q',
    color=alt.Color('Origin:N', legend=alt.Legend(title='Click Legend to Filter Bar Chart')),
    opacity=alt.condition(cars_origin_selection, alt.value(0.9), alt.value(0.1)),
    tooltip=['Name:N', 'Origin:N']
).add_params(
    cars_origin_selection # This selection will drive the filter
).properties(
    width=350,
    height=250,
    title='Controller: Click Legend'
)

# Bar chart (will be filtered by the selection from the scatter plot's legend)
bar_filtered = alt.Chart(source_data).mark_bar().encode(
    x='Origin:N',
    y='average(Miles_per_Gallon):Q',
    color='Origin:N',
    tooltip=['Origin:N', 'average(Miles_per_Gallon):Q']
).transform_filter(
    cars_origin_selection # Filter this chart based on the selection
).properties(
    width=250,
    height=250,
    title='Filtered: Avg MPG by Origin'
)

linked_chart_example = scatter_controller | bar_filtered # Display side-by-side

print("\nLinked View Example (Legend Click Filters Bar Chart):")
linked_chart_example
# Click items in the legend of the scatter plot. Observe how the bar chart updates.
# If nothing selected in legend (and selection empty='all'), bar chart shows all.
# If empty='none', bar chart would be empty initially.
```
```python
# Discussion Point for Linked View:
# - The `cars_origin_selection` is defined once and added to the scatter_controller.
# - The `bar_filtered` chart uses `.transform_filter(cars_origin_selection)` to react to changes
#   in that selection state.
# - This is a basic example. More complex dashboards can be built by linking multiple charts
#   and selections. For this course, understanding conditional encoding and simple filtering
#   is the main goal for interactivity.
```

This section introduces interactivity, starting with defining selections and then using them for conditional encoding, which is a very common and useful technique. The linked view example is kept simple and uses a legend binding to make the interaction mechanism clear. The "Discussion Point" in the notebook can be used by the instructor to elaborate.

Ready for Section 5: "Data Transformations for Visualization"?