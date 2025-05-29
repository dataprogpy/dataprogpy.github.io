---
title: Interactive Visualizations with Altair
icon: material/numeric-4
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
selection_name = alt.selection_single(empty='none')

alt.Chart(data).mark_point().encode(
    x='x_field:Q',
    y='y_field:Q',
    color=alt.condition(selection_name, 'highlight_color:N', alt.value('lightgray')),
    opacity=alt.condition(selection_name, alt.value(1.0), alt.value(0.3))
).add_params(
    selection_name
)
```
In this example, points included in `selection_name` will get `highlight_color` and full opacity, while others will be light gray and semi-transparent.

### 2. Filtering Data (Linked Views - Basic)

Selections can also be used to filter data. The `.transform_filter(selection_name)` method is used for this. This is powerful for creating linked views where interacting with one chart affects another.

**Example: A scatter plot filters a bar chart (conceptual)**
Imagine a scatter plot where you select points. A nearby bar chart could then update to show data only related to those selected points.

```python
brush = alt.selection_interval() # Define a brush selection

scatter = alt.Chart(data).mark_point().encode(
x='X:Q', y='Y:Q'
).add_params(brush)

bars = alt.Chart(data).mark_bar().encode(
x='Category:N', y='average(Value):Q'
).transform_filter(
brush # Filter bars based on the brush selection in the scatter plot
)

scatter & bars # Display side-by-side
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