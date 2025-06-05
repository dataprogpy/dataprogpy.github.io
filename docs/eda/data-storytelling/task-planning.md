---
icon: material/numeric-4
---

Okay, let's craft the content for Step 3. We've defined our audience and formulated our analytical questions. Now, it's time to start thinking visually and plan how we'll answer those questions using charts. This is where the power of a structured approach to visualization, like the Grammar of Graphics, comes into play.

---

#### Step 3: Initial Data Visualization Task List (V1) - Applying the Grammar of Graphics 📝🎨

You've done the crucial work of defining *what* questions you want to answer for your persona. Now, we need to determine *how* to visually represent the data to answer these questions effectively. This isn't just about picking a "pretty chart"; it's about a deliberate process of mapping data to visual elements to create clarity and insight.

##### **Recap: The Grammar of Graphics in Altair**

In previous lessons, you were introduced to Altair, a visualization library in Python that embraces the **Grammar of Graphics**. This is a foundational concept that helps us build visualizations systematically. Let's quickly recap the core components:

1.  **Data:** The dataset you're working with (or a specific subset/transformation of it). In our case, the King County housing data.
2.  **Marks:** These are the geometric shapes that represent your data points. Common marks include:
    * `mark_point()`: For scatter plots, showing individual data points.
    * `mark_bar()`: For bar charts, representing magnitudes or counts.
    * `mark_line()`: For line charts, often showing trends over time or continuous relationships.
    * `mark_area()`: For area charts, similar to line charts but emphasizing volume.
    * `mark_rect()`: For heatmaps or 2D histograms.
    * `mark_geoshape()`: For geographic maps (if you have geographic data).
3.  **Encodings:** This is where the magic happens. Encodings define how data variables are mapped to the visual properties (or **channels**) of the marks. Key channels include:
    * `x`: Position along the x-axis.
    * `y`: Position along the y-axis.
    * `color`: The color of the marks.
    * `size`: The size of the marks.
    * `shape`: The shape of point marks.
    * `opacity`: The transparency of marks.
    * `tooltip`: Information displayed when hovering over a mark.
    * `column`/`row`: For creating faceted charts (small multiples).

By combining these components, you can construct a vast array of visualizations. The key is to think about what you want to communicate and then choose the marks and encodings that best serve that purpose.

##### **Translating Research Questions into Visual Specifications**

Now, let's take the analytical questions you drafted in Step 2 and translate each one into a specific plan for a visualization. For each question, ask yourself:

1.  **Data Variables:** Which specific columns from your dataset are needed to answer this question? (You likely identified these in the previous step).
2.  **Mark Type:** What geometric mark will best represent the entities or relationships in your question?
    * *Comparing individual values for different categories?* `mark_bar()` is often a good choice.
    * *Showing the relationship between two numerical variables?* `mark_point()` (a scatter plot) is a classic.
    * *Displaying the distribution of a single numerical variable?* `mark_bar()` (for a histogram) or `mark_area()` could work.
    * *Showing a trend over time?* `mark_line()` is standard.
3.  **Key Encodings:** How will you map your chosen data variables to the visual channels of your selected mark?
    * What goes on the `x`-axis? What goes on the `y`-axis?
    * Could `color` be used to represent an additional category or a continuous variable?
    * Would `size` or `shape` add useful information without cluttering the visual?
    * What information should appear in a `tooltip` for interactive exploration?

**Let's walk through an example using one of Sarah the Savvy Homebuyer's potential questions:**

* **Research Question:** "How does the size of the living area (`sqft_liv`) relate to the sale `price`?"

    1.  **Data Variables:**
        * `price` (numerical)
        * `sqft_liv` (numerical)
        * *(Potentially others for adding layers, e.g., `waterfront` (categorical) or `grade` (ordinal) to see if they modify the relationship)*

    2.  **Mark Type:**
        * Since we want to see the relationship between two numerical variables for individual houses, `mark_point()` is a strong candidate. Each point will represent a single house.

    3.  **Key Encodings:**
        * `x`: `alt.X('sqft_liv', type='quantitative', title='Living Area (sq ft)')`
        * `y`: `alt.Y('price', type='quantitative', title='Sale Price (USD)', axis=alt.Axis(format='$,.0f'))`
        * `tooltip`: `['price', 'sqft_liv', 'bedrooms', 'bathrooms']` (to show details on hover)
        * *Optional further encoding (to explore more nuance):*
            * `color`: `alt.Color('waterfront:N', title='Waterfront')` (to see if waterfront properties show a different pattern). The `:N` tells Altair to treat `waterfront` as a Nominal (categorical) variable.

**Connecting to Common Chart Types:**
Notice how these choices naturally lead to a familiar chart type. In the example above, using `mark_point()` with two quantitative variables encoded to the `x` and `y` axes results in a **scatter plot**. If we were answering "What is the average price per `zipcode`?" we might use `mark_bar()`, encoding `zipcode` to the `x`-axis and average `price` to the `y`-axis, resulting in a **bar chart**.

The Grammar of Graphics encourages you to think from these fundamental building blocks, giving you more flexibility than just picking a chart type from a predefined list.

##### **Creating the Initial Data Visualization Task List (V1)**

Now it's time to formalize these ideas into an initial task list. This list will be your guide as you start coding your visualizations. For each analytical question, create an entry that outlines your visual plan.

**Example Task List Entry:**

| Research Question                                       | Data Variables                     | Mark Type    | Key Encodings (X, Y, Color, Size, Tooltip, etc.)                                                                        | Anticipated Chart Type | Notes / Potential Challenges                                    |
| :------------------------------------------------------ | :--------------------------------- | :----------- | :-------------------------------------------------------------------------------------------------------------------- | :--------------------- | :-------------------------------------------------------------- |
| How is `price` distributed overall?                     | `price`                            | `bar`        | `x: price` (binned), `y: count()`                                                                                     | Histogram              | Might need to handle outliers or try log scale for price.       |
| How does `sqft_liv` relate to `price`?                | `price`, `sqft_liv`, `waterfront`  | `point`      | `x: sqft_liv (Q)`, `y: price (Q)`, `color: waterfront (N)`, `tooltip: [price, sqft_liv, bedrooms]`                    | Scatter Plot           | Large number of points might cause overplotting.                |
| What's the average `price` by number of `bedrooms`?   | `price`, `bedrooms`                | `bar`        | `x: bedrooms (O)`, `y: mean(price) (Q)`, `tooltip: [mean(price), count()]`                                              | Bar Chart              | Ensure `bedrooms` is treated as ordinal/discrete. Consider outliers. |

**Emphasis: This is Version 1!**
This task list is your *starting hypothesis*. As you actually create these visualizations (Phase 3), you'll inevitably discover things:
* The data might not be in the format you expected.
* A chosen mark or encoding might not work as well as you thought.
* The visualization might reveal unexpected patterns that lead to new questions or a desire to try a different visual approach.
This is perfectly normal! Your task list is a **living document** that you will update and refine.

---

> **💡 Activity: Draft Your Initial Visualization Task List**
>
> Take the 2-3 analytical questions you drafted in Step 2. For each question:
>
> 1.  Identify the primary **data variables** involved.
> 2.  Choose an appropriate **mark type** from Altair.
> 3.  Define the **key encodings** (at least `x` and `y`, but also consider `color`, `size`, `tooltip`, etc.). Think about the `type` for each encoding (Quantitative, Nominal, Ordinal, Temporal).
> 4.  (Optional but helpful) Note the anticipated common chart type that these choices would produce.
> 5.  (Optional) Briefly note any initial thoughts or potential challenges.
>
> Create a simple table or list for your tasks, similar to the example above. This will be your first concrete plan for building your data story's visuals.

---

This completes Step 3. We've laid out a structured way to plan visualizations based on analytical questions and the Grammar of Graphics. The students should now have a tangible list of charts they intend to create.

Next, we'll move into "Phase 2: Data Ingestion and Initial Exploration," where students will start working with the data in Polars and attempt their first visualizations, which often leads to the "Uh-Oh" moment of discovering data issues.

How does this content for Step 3 look?

You're absolutely right. Just as the set of analytical questions was a key milestone, this initial Data Visualization Task List is another crucial checkpoint in our workflow. It translates abstract questions into a concrete visual plan.

Let's add a concluding section to Step 3 to highlight this.

---

#### Step 3: Initial Data Visualization Task List (V1) - Applying the Grammar of Graphics 📝🎨 (Continued)

*(This follows the "Activity: Draft Your Initial Visualization Task List" box)*

##### **Your Milestone: A Visual Blueprint**

Congratulations! By translating your analytical questions into a list of visual specifications—defining data, marks, and encodings—you've created the **initial blueprint for your data story's visuals**. This Data Visualization Task List (V1) is more than just a to-do list; it's a critical output that bridges your analytical goals with the practical steps of visual creation.

**Why is this "Visual Blueprint" so important at this stage?**

1.  **Tangible Plan for Action:** It moves you from abstract questions to a concrete plan for what charts to build. You now have specific starting points for when you begin coding with Altair.
2.  **Reinforces Purposeful Design:** By thinking through marks and encodings for each question, you're practicing deliberate visual design, ensuring each chart element has a purpose related to answering your question and guiding audience attention.
3.  **Anticipates Data Needs (and a Sneak Peek into Cleaning):** Listing the specific variables for each chart often highlights which data will be heavily used. As you attempt to create these visuals (in the upcoming phases), any issues with these key variables (missing values, incorrect types, outliers) will become immediately apparent, directly informing your data cleaning priorities.
4.  **Facilitates Iteration:** While this is V1, having it documented makes it easier to track changes, make decisions about which visuals to refine or discard, and explain your design choices as you iterate.
5.  **Foundation for the "Story":** This list is the first glimpse of how individual pieces of analysis (the charts) will eventually come together to form a narrative. You can start to see how different visualizations might connect to tell a larger story.

**Before moving on, take a quick look at your Data Visualization Task List:**
* Does each entry clearly link back to one of your analytical questions?
* Have you considered the data types (Quantitative, Nominal, Ordinal) for your encodings?
* Does your choice of marks and encodings seem appropriate for the type of question you're trying to answer (e.g., comparison, relationship, distribution)?

This blueprint isn't rigid; it's meant to evolve. But having this thoughtful initial plan is essential for an efficient and effective journey into the data. It prepares you for the hands-on work of data ingestion, exploration, and the iterative process of cleaning and visualizing that we're about to embark on.

---

This addition emphasizes the deliverable of Step 3 and sets the stage for the practical application in the subsequent phases.

Now we are truly ready to move into "Phase 2: Data Ingestion and Initial Exploration," starting with Step 4. Shall we proceed?