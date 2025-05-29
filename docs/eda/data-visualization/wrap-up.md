---
icon: material/numeric-7
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

The best way to solidify your understanding and build confidence is through practice. The [starter Jupyter Notebook](`https://colab.research.google.com/github/dataprogpy/code-samples/blob/main/starter_files/04_introduction_to_altair.ipynb`) contains a list of "Things to Try" exercises. These challenges encourage you to:

* Explore new datasets (See GitHub repo of [`vega-datasets`](https://github.com/vega/vega-datasets), or [Kaggle](https://www.kaggle.com/datasets) for practice datasets).
* Recreate charts you find in the wild.
* Experiment deeply with customizations and interactivity.
* Combine your Polars and Altair skills.
* Practice data storytelling.

I strongly encourage you to work through these activities. Don't hesitate to consult the Altair documentation, online resources, or leverage generative AI tools strategically to help you along the way. Remember, the goal is to become comfortable applying these powerful data programming techniques to your own professional challenges and personal projects.
```