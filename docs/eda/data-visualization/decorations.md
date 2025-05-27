---
icon: material/numeric-3
title: Enhancing Your Visualizations
---

Creating a basic chart is just the first step. To effectively communicate insights, your visualizations need to be clear, well-annotated, and visually appealing. Altair provides extensive options for customizing almost every aspect of your chart.

## Chart Properties: Titles, Sizing, and More

The `.properties()` method allows you to set various chart-level attributes.

* **Titles**: Every chart should have a descriptive title.
    ```python
    .properties(title='My Awesome Chart Title')
    ```
* **Sizing**: You can control the width and height of your chart.
    ```python
    .properties(width=600, height=400)
    ```
    For responsive sizing that fills the available width, you can set `width='container'`. However, be mindful that this might not work perfectly in all rendering environments or when saving static images.

## Customizing Axes: Labels, Formatting, and Scales

Clear axis labels are crucial for understanding a chart. You can customize axes by passing an `alt.Axis()` object to the `axis` argument within an encoding channel definition (e.g., `alt.X()`, `alt.Y()`).

* **Axis Titles**: Change the default column name to something more descriptive.
    ```python
    alt.X('Horsepower:Q', axis=alt.Axis(title='Vehicle Horsepower (HP)'))
    alt.Y('Miles_per_Gallon:Q', axis=alt.Axis(title='Fuel Efficiency (MPG)'))
    ```
* **Label Formatting**: Control the appearance of axis tick labels (e.g., number formats, date formats).
    ```python
    alt.Y('some_value:Q', axis=alt.Axis(format='~s')) # SI unit prefix (k, M, G)
    alt.X('date_column:T', axis=alt.Axis(format='%Y-%m-%d', title='Date')) # Date format
    ```
* **Disabling Axes/Gridlines**: Sometimes you might want to remove axes or gridlines for a cleaner look.
    ```python
    alt.X('field:Q', axis=None) # Remove X-axis completely
    alt.Y('field:Q', axis=alt.Axis(grid=False)) # Remove Y-axis gridlines
    ```
* **Scale Properties**: While axis deals with the visual representation of the scale (ticks, labels), `alt.Scale()` within an encoding channel definition configures the mapping from data values to visual values.
    ```python
    alt.Y('value:Q', scale=alt.Scale(domain=[0, 100])) # Set Y-axis domain
    alt.Color('category:N', scale=alt.Scale(scheme='tableau10')) # Set color scheme
    ```

## Working with Color: Schemes and Scales

Color is a powerful visual encoding but should be used thoughtfully.

* **Categorical Data (Nominal/Ordinal)**: Use qualitative color schemes where colors are distinct. Altair provides many Vega-Lite schemes.
    ```python
    alt.Color('Origin:N', scale=alt.Scale(scheme='category10')) # Or 'accent', 'paired', 'tableau20' etc.
    ```
* **Quantitative Data**: Use sequential (for ordered data, e.g., light to dark) or diverging (when data has a meaningful midpoint, e.g., negative to positive) color schemes.
    ```python
    alt.Color('Temperature:Q', scale=alt.Scale(scheme='viridis')) # Sequential
    alt.Color('ProfitRatio:Q', scale=alt.Scale(scheme='redblue', domainMid=0)) # Diverging
    ```
* **Specifying a Fixed Color**: If you don't want color to encode a data field but simply want all marks to be a specific color, use `alt.value()`.
    ```python
    .mark_point(color='steelblue')
    # or within encode if other encodings are present
    # .encode(..., color=alt.value('steelblue'))
    ```

## Informative Tooltips

Tooltips provide details when a user hovers over a data point. They significantly enhance interactivity and allow you to convey more information without cluttering the main chart.

```python
.encode(
    # ... other encodings
    tooltip=[
        'Name:N',
        alt.Tooltip('Horsepower:Q', title='HP'), # Custom title in tooltip
        alt.Tooltip('Miles_per_Gallon:Q', title='MPG', format='.1f') # Format number
    ]
)
```
You can specify a list of column names or use `alt.Tooltip()` for more control over titles and formatting within the tooltip.

## Saving Your Charts

Once you've created and refined your visualization, you'll likely want to save it.

* **HTML**: Saves the chart as an interactive HTML file.
    ```python
    my_chart.save('my_chart.html')
    ```
* **PNG or SVG (Static Images)**: For static images, you might need an additional engine like `altair_viewer` or `vl-convert`. This setup can sometimes be tricky depending on your environment.
    ```python
    # Requires altair_viewer or vl-convert installed
    # my_chart.save('my_chart.png')
    # my_chart.save('my_chart.svg')
    ```
    For Colab, saving as HTML is straightforward. For PNG/SVG, if direct saving isn't working easily, you can often right-click the displayed chart in the output cell and save the image from there (though this might have limitations).

## Incremental Development Exercise (Recap)

As emphasized before, building a polished chart is an iterative process:
1.  **Core Plot**: Start with the basic data, mark, and essential x/y encodings.
2.  **Add Layers**: Introduce color, size, or faceting if needed.
3.  **Refine Tooltips**: Ensure hover information is useful.
4.  **Customize Aesthetics**: Adjust titles, axis labels, formatting, and color schemes.
5.  **Review and Iterate**: Check if the chart clearly communicates the intended message.

This approach makes the process manageable and helps in pinpointing issues if they arise.
```

---
### **Starter Jupyter Notebook Content (Section 3)**

```python
# {{ Session Name: Data Visualization with Altair }}
# {{ Section 3: Enhancing Your Visualizations }}

# Ensure libraries from previous sections are loaded
# import altair as alt
# import polars as pl
# from vega_datasets import data as vega_data
# alt.renderers.enable('colab')

# Load the 'cars' dataset again for these examples
cars_pd = vega_data.cars()
cars_pl = pl.from_pandas(cars_pd).drop_nulls(subset=['Horsepower', 'Miles_per_Gallon', 'Origin', 'Name', 'Year'])
```

```python
# ------------------------------------------------------------------------------
# 3.1 Chart Properties: Titles and Sizing
# ------------------------------------------------------------------------------
# Let's start with a basic scatter plot from the previous section.
base_scatter = alt.Chart(cars_pl).mark_circle(size=60, opacity=0.7).encode(
    x='Horsepower:Q',
    y='Miles_per_Gallon:Q',
    color='Origin:N',
    tooltip=['Name:N', 'Horsepower:Q', 'Miles_per_Gallon:Q']
)

# Add a title and set a specific width and height
scatter_with_properties = base_scatter.properties(
    title='Enhanced: Car Horsepower vs. MPG by Origin',
    width=500,
    height=300
)

print("\nScatter Plot with Title and Custom Size:")
scatter_with_properties
```

```python
# ------------------------------------------------------------------------------
# 3.2 Customizing Axes: Labels, Formatting
# ------------------------------------------------------------------------------
# Default axis titles are just the column names. Let's make them more descriptive.
# We use alt.X(), alt.Y() and pass alt.Axis() to the 'axis' argument.

scatter_custom_axes = alt.Chart(cars_pl).mark_circle(size=60, opacity=0.7).encode(
    x=alt.X('Horsepower:Q',
            axis=alt.Axis(title='Vehicle Horsepower (HP)', grid=True), # Custom title, ensure grid is on
            scale=alt.Scale(zero=False) # Don't necessarily start axis at zero if data is far from it
           ),
    y=alt.Y('Miles_per_Gallon:Q',
            axis=alt.Axis(title='Fuel Efficiency (Miles per Gallon)', format='~s'), # SI unit format
            scale=alt.Scale(zero=False)
           ),
    color='Origin:N',
    tooltip=['Name:N', 'Horsepower:Q', 'Miles_per_Gallon:Q']
).properties(
    title='Horsepower vs. MPG with Custom Axes',
    width=500,
    height=300
)

print("\nScatter Plot with Custom Axis Titles and Formatting:")
scatter_custom_axes
```

```python
# Student Task: Axis Customization
# 1. Take the `avg_hp_by_origin_bar` chart from Section 2 (Average Horsepower by Origin).
#    (Recreate it here if needed based on `cars_pl`)
# 2. Customize the Y-axis title to "Average Horsepower (HP)".
# 3. Customize the X-axis title to "Country of Origin".
# 4. Add a main title: "Vehicle Power Comparison by Origin".

# Recreate the base chart for the task:
avg_hp_by_origin_base = alt.Chart(cars_pl).mark_bar().encode(
    x='Origin:N',
    y='average(Horsepower):Q',
    color='Origin:N',
    tooltip=['Origin:N', 'average(Horsepower):Q']
)

# YOUR CODE HERE for Student Task (Axis Customization)
# Example Solution:
# avg_hp_customized = avg_hp_by_origin_base.encode(
#     x=alt.X('Origin:N', axis=alt.Axis(title='Country of Origin')),
#     y=alt.Y('average(Horsepower):Q', axis=alt.Axis(title='Average Horsepower (HP)')),
#     color='Origin:N', # Keep color encoding
#     tooltip=['Origin:N', 'average(Horsepower):Q']
# ).properties(
#     title='Vehicle Power Comparison by Origin'
# )
# avg_hp_customized
```

```python
# ------------------------------------------------------------------------------
# 3.3 Working with Color: Schemes and Scales
# ------------------------------------------------------------------------------
# Using a different color scheme for the 'Origin' nominal variable.
# The `scale` property within an encoding channel (like alt.Color) allows this.

scatter_custom_color_scheme = alt.Chart(cars_pl).mark_circle(size=60, opacity=0.7).encode(
    x=alt.X('Horsepower:Q', axis=alt.Axis(title='Vehicle Horsepower (HP)')),
    y=alt.Y('Miles_per_Gallon:Q', axis=alt.Axis(title='Fuel Efficiency (MPG)')),
    color=alt.Color('Origin:N', scale=alt.Scale(scheme='tableau10')), # Using 'tableau10' scheme
    tooltip=['Name:N', 'Horsepower:Q', 'Miles_per_Gallon:Q']
).properties(
    title='Scatter Plot with "tableau10" Color Scheme',
    width=500,
    height=300
)

print("\nScatter Plot with Custom Color Scheme:")
scatter_custom_color_scheme

# Example with a quantitative color scale (e.g., color by 'Acceleration')
scatter_quantitative_color = alt.Chart(cars_pl).mark_circle(size=60, opacity=0.8).encode(
    x=alt.X('Horsepower:Q', axis=alt.Axis(title='Vehicle Horsepower (HP)')),
    y=alt.Y('Miles_per_Gallon:Q', axis=alt.Axis(title='Fuel Efficiency (MPG)')),
    color=alt.Color('Acceleration:Q', scale=alt.Scale(scheme='viridis')), # Sequential scheme for quantitative
    tooltip=['Name:N', 'Horsepower:Q', 'Miles_per_Gallon:Q', 'Acceleration:Q']
).properties(
    title='Color Encoded by "Acceleration" (Quantitative)',
    width=500,
    height=300
)
print("\nScatter Plot with Quantitative Color Scheme:")
scatter_quantitative_color
```

```python
# ------------------------------------------------------------------------------
# 3.4 Informative Tooltips
# ------------------------------------------------------------------------------
# Tooltips can be customized for better readability and more information.

scatter_enhanced_tooltips = alt.Chart(cars_pl).mark_circle(size=60, opacity=0.7).encode(
    x=alt.X('Horsepower:Q', axis=alt.Axis(title='Vehicle Horsepower (HP)')),
    y=alt.Y('Miles_per_Gallon:Q', axis=alt.Axis(title='Fuel Efficiency (MPG)')),
    color=alt.Color('Origin:N', scale=alt.Scale(scheme='category10')),
    tooltip=[
        alt.Tooltip('Name:N', title='Car Model'), # Custom title for 'Name'
        alt.Tooltip('Origin:N', title='Origin Country'),
        alt.Tooltip('Horsepower:Q', title='HP', format='.0f'), # Format as integer
        alt.Tooltip('Miles_per_Gallon:Q', title='MPG', format='.1f'), # Format to one decimal place
        alt.Tooltip('Year:T', title='Manufacture Year', format='%Y') # Format year
    ]
).properties(
    title='Scatter Plot with Enhanced Tooltips',
    width=500,
    height=300
)

print("\nScatter Plot with Enhanced Tooltips:")
scatter_enhanced_tooltips
```

```python
# ------------------------------------------------------------------------------
# 3.5 Saving Your Charts
# ------------------------------------------------------------------------------
# You can save your Altair charts to HTML, PNG, SVG, etc.
# HTML is interactive and usually works without extra setup.
# PNG/SVG might require `altair_viewer` or `vl-convert`.

# Save the chart with enhanced tooltips to an HTML file
# This file will be saved in your Colab environment's file system.
# You can then download it from the file pane on the left.
# scatter_enhanced_tooltips.save('cars_scatter_plot.html')
# print("\nChart saved to 'cars_scatter_plot.html'. Check your Colab files.")

# Saving as PNG (may require additional setup like !pip install altair_viewer)
# try:
#    scatter_enhanced_tooltips.save('cars_scatter_plot.png')
#    print("Chart saved to 'cars_scatter_plot.png'.")
# except Exception as e:
#    print(f"Could not save as PNG directly. Error: {e}")
#    print("You might need to install a driver like 'altair_viewer' or 'vl-convert'.")
#    print("Alternatively, in Colab, you can often right-click the displayed chart and 'Save image as...'")
```

```python
# ------------------------------------------------------------------------------
# 3.6 Incremental Development Exercise
# ------------------------------------------------------------------------------
# Task: Create a polished histogram of 'Acceleration' from the `cars_pl` dataset.
# Follow these incremental steps:

# 1. Base Histogram:
#    - Data: `cars_pl`
#    - Mark: `mark_bar()`
#    - Encodings:
#        - X-axis: 'Acceleration:Q', binned (e.g., `maxbins=15`)
#        - Y-axis: 'count():Q'
#    Display this base chart.

# YOUR CODE for Step 1
# base_accel_hist = alt.Chart(cars_pl).mark_bar().encode(
#     alt.X('Acceleration:Q', bin=alt.Bin(maxbins=15)),
#     y='count():Q'
# )
# base_accel_hist

# 2. Add Properties:
#    - Add a title: "Distribution of Vehicle Acceleration".
#    - Set width to 400.
#    Display this chart.

# YOUR CODE for Step 2
# accel_hist_props = base_accel_hist.properties(
#     title="Distribution of Vehicle Acceleration",
#     width=400
# )
# accel_hist_props

# 3. Customize Axes and Tooltips:
#    - X-axis: Title "Acceleration (0-60 mph time)", format values with one decimal place (e.g., `format='.1f'`).
#    - Y-axis: Title "Number of Car Models".
#    - Tooltips: Show the binned 'Acceleration' range and the count.
#    Display this chart.

# YOUR CODE for Step 3
# accel_hist_final = alt.Chart(cars_pl).mark_bar().encode(
#     alt.X('Acceleration:Q',
#           bin=alt.Bin(maxbins=15),
#           axis=alt.Axis(title='Acceleration (e.g., 0-60 mph time in sec)', format='.1f')
#          ),
#     y=alt.Y('count():Q', axis=alt.Axis(title='Number of Car Models')),
#     tooltip=[alt.Tooltip('Acceleration:Q', bin=True, title='Acceleration Range', format='.1f'), 'count():Q']
# ).properties(
#     title="Distribution of Vehicle Acceleration",
#     width=400
# )
# accel_hist_final

# 4. (Optional) Save your final polished histogram as an HTML file.
# accel_hist_final.save('acceleration_histogram.html')
```

This covers Section 3, providing students with the tools to make their visualizations much more professional and communicative. The notebook includes tasks for them to apply these concepts directly.

Ready for Section 4: "Interactive Visualizations"?