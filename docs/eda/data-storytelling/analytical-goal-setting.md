---
icon: material/numeric-2
---


# Phase 1: Understanding the Landscape & Defining Goals

Every data story begins with understanding the raw material: your data. But data alone doesn't speak; we need to give it a voice by asking the right questions and defining clear goals for our narrative. This phase is about laying that crucial groundwork.

## Step 1: The Data Dictionary - Your Starting Point 🗺️

Before you can ask meaningful questions of your data, you need to understand what information you have, what each piece of information means, its format, and any potential limitations. This is where the **data dictionary** comes in. It's your map to the dataset.

For this lesson, we'll be working with a dataset containing information about house sales in King County, WA (which includes Seattle). Here's a look at its structure:


| Variable      | Description                                                                                                                                                                                             | Data Type (Examples)        |
|:--------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------|
| `id`          | Unique identification number for each house sale.                                                                                                                                                       | Integer (e.g., 7129300520)  |
| `date`        | Date the house was sold.                                                                                                                                                                                | String/Date (e.g., "20141013T000000") |
| `price`       | Sale price of the house.                                                                                                                                                                                | Float/Integer (e.g., 221900.0) |
| `bedrooms`    | Number of bedrooms.                                                                                                                                                                                     | Integer (e.g., 3)           |
| `bathrooms`   | Number of bathrooms (can be fractional, e.g., 0.5 represents a half bath).                                                                                                                               | Float (e.g., 1.0, 2.5)      |
| `sqft_liv`    | Square footage of the interior living space.                                                                                                                                                            | Integer (e.g., 1180)        |
| `sqft_lot`    | Square footage of the land lot.                                                                                                                                                                         | Integer (e.g., 5650)        |
| `floors`      | Total number of floors (levels) in the house.                                                                                                                                                           | Float (e.g., 1.0, 1.5)      |
| `waterfront`  | Indicates if the property has a waterfront view. `1` if yes, `0` if no.                                                                                                                                     | Integer (0 or 1)            |
| `view`        | An index from 0 to 4 of how good the view from the property was (0 = no view, 4 = excellent view).                                                                                                        | Integer (0-4)               |
| `condition`   | Rates the overall condition of the house, from 1 (poor) to 5 (very good).                                                                                                                               | Integer (1-5)               |
| `grade`       | Rates the quality of construction and design, from 1-13. Higher grade indicates better construction and materials. See [King County's Glossary](http://info.kingcounty.gov/assessor/esales/Glossary.aspx?type=r) for details. | Integer (e.g., 7, 8)        |
| `sqft_above`  | Square footage of interior housing space that is above ground level.                                                                                                                                    | Integer (e.g., 1180)        |
| `sqft_basmt`  | Square footage of interior housing space that is below ground level (basement). `0` if no basement.                                                                                                       | Integer (e.g., 0, 500)      |
| `yr_built`    | Year the house was built.                                                                                                                                                                               | Integer (e.g., 1955)        |
| `yr_renov`    | Year the house was renovated. `0` if never renovated or if renovation status is unknown.                                                                                                              | Integer (e.g., 0, 1991)     |
| `zipcode`     | 5-digit zip code of the property.                                                                                                                                                                       | String/Integer (e.g., 98178)|
| `lat`         | Latitude coordinate of the property.                                                                                                                                                                    | Float (e.g., 47.5112)       |
| `long`        | Longitude coordinate of the property.                                                                                                                                                                   | Float (e.g., -122.257)      |
| `squft_liv15`  | Average interior living space (in sqft) of the nearest 15 neighbors.                                                                                                                                    | Integer (e.g., 1340)        |
| `squft_lot15`  | Average land lot size (in sqft) of the nearest 15 neighbors.                                                                                                                                            | Integer (e.g., 5080)        |
| `Shape_leng`  | Polygon length in meters (often GIS-related administrative data).                                                                                                                                      | Float                       |
| `Shape_Area`  | Polygon area in meters (often GIS-related administrative data).                                                                                                                                        | Float                       |

**Why spend time on this?**

* **Identify Key Variables:** Which variables seem most relevant to potential questions about housing? (e.g., `price`, `sqft_liv`, `bedrooms`, `zipcode`, `waterfront`).
* **Understand Data Types:** Knowing if a variable is numerical (integer, float) or categorical (like `zipcode`, even if it looks like a number) is crucial for choosing appropriate analysis and visualization methods. For instance, you might calculate an average `price` (numerical) but you wouldn't average `zipcode`s.
* **Spot Potential Issues:**
    * Are there missing values or placeholder values (like `0` for `yr_renov`) that need special handling?
    * Are there codes or indices (like `view` or `condition`) where you need to understand the scale?
    * Does the `date` field need conversion to a proper date format for time-based analysis? (Hint: Yes, it often does!)
* **Clarify Definitions:** What exactly does `grade` mean? The link provided gives more context. Understanding these nuances is key to accurate storytelling.

**First Look & Familiarization:**

Take a moment to look through this data dictionary.

* What variables immediately catch your eye as potentially interesting for a story about house prices?
* Are there any variables whose meaning isn't immediately obvious?
* What kind of audience might be interested in insights from this data? (e.g., potential homebuyers, sellers, real estate agents, urban planners).

???+ question "**💡 Activity: Initial Variable Brainstorm**"

    Jot down 3-5 variables from this list that you think could be central to understanding housing prices or trends in this area. Consider why each might be important. We'll use this as a starting point for formulating questions.

---

## Step 2: Articulating Analytical Goals & Research Questions 🎯

Now that we have a basic understanding of our dataset, the next crucial step is to define *what we want to achieve* with our data story. Simply exploring data without a clear purpose can lead to a scattered collection of charts rather than a focused narrative.

**Before you ask "What can the data tell me?", first ask "Who am I talking to and what do they need to know?"**

### **Introducing Audience Personas**

To help focus your efforts, it's incredibly useful to define an **audience persona**. A persona is a semi-fictional representation of your ideal audience member, based on their characteristics, goals, needs, and pain points. For data storytelling, this helps you:

* **Empathize:** Step into their shoes and see the data from their perspective.
* **Focus:** Prioritize questions and insights that are most relevant and valuable to *them*.
* **Tailor Communication:** Choose the right language, complexity, and visual style.
* **Select Key Variables:** Identify which parts of the data will resonate most strongly or answer their specific concerns.

**Creating a Simple Persona:**

You don't need an elaborate persona for every analysis, but even a simple one can be powerful. Consider:

* **Role/Title:** Who are they? (e.g., "First-time Homebuyer," "Real Estate Investor," "City Planner," "Marketing Manager for a Real Estate Agency").
* **Primary Goal (related to your data):** What are they trying to achieve or understand? (e.g., "Find an affordable 3-bedroom house in a good neighborhood," "Identify undervalued properties for investment," "Understand housing development trends").
* **Key Questions/Concerns:** What specific questions might they have that your data could answer? (e.g., "What's the typical price range?", "Which areas are appreciating fastest?", "How do property features like bedrooms or waterfront affect price?").
* **Data Familiarity:** How comfortable are they with data and charts? (This influences how you design your visuals and explanations).

**Example Persona for our Housing Data:**

* **Persona Name:** "The Savvy Homebuyer (Sarah)"
* **Role:** A working professional looking to buy her first home.
* **Goal:** To understand the King County housing market to find a 2-3 bedroom house that balances affordability, good condition, and reasonable commute, within the next 6-9 months.
* **Key Questions:**

    * What is the general price distribution for houses?
    * How much does `sqft_liv`, `bedrooms`, or `bathrooms` impact `price`?
    * Are there significant price differences between `zipcode`s?
    * Is a `waterfront` property completely out of reach? What's the premium?
    * How much does the `condition` or `grade` of a house influence its value?
* **Data Familiarity:** Comfortable with basic charts, but prefers clear takeaways. Not a data scientist.

By defining "Sarah," we can now look at our data dictionary (from Step 1) and select variables that are most pertinent to her needs (e.g., `price`, `bedrooms`, `bathrooms`, `sqft_liv`, `zipcode`, `condition`, `grade`, `waterfront`). Variables like `Shape_leng` or `Shape_Area` might be less immediately relevant to Sarah, so we might deprioritize them for a story aimed at her.

???+ question "**💡 Activity: Define Your Audience Persona**"

    Before we move on to brainstorming specific research questions, take 5 minutes to sketch out a simple persona for whom you might be analyzing this housing data.

    * Give them a name/role.
    * What's their primary goal related to this housing data?
    * List 2-3 key questions they might have.

    This persona will guide your choices in the subsequent steps.

Now that you have a clearer picture of *who* your data story is for and *what they care about*, let's translate those needs into concrete analytical goals and research questions. Your persona is your North Star for this process.

### **Approach 1: Revisit Your Persona's Goals & Questions**

Look back at the persona you just created.

* What are their primary goals?
* What key questions did you list for them?

These directly inform your analytical objectives. If Sarah the Savvy Homebuyer wants to "understand the general price distribution," then a primary analytical goal for you is to explore and summarize house prices. Her question, "How much does `sqft_liv` impact `price`?" points directly to an analysis of the relationship between these two variables.

### **Approach 2: Crafting Simplified User Stories**

User stories are a common tool in project management and product development, and a simplified version can be very effective for focusing data analysis. They help ensure your work is tied to a specific user need and a desired outcome.

The format is typically:

**"As a [persona/type of user], I want to [action/understand something] so that I can [achieve a goal/make a decision]."**

Let's try this with our "Sarah the Savvy Homebuyer" persona and the housing dataset:

* **Example 1 (Focus on Price Understanding):**

    * "As **Sarah the Savvy Homebuyer**, I want to **understand the typical range of house prices and how many houses fall into different price brackets** so that I can **set realistic expectations for my budget**."
    * *This points towards needing a distribution of the `price` variable.*

* **Example 2 (Focus on Key Features):**

    * "As **Sarah the Savvy Homebuyer**, I want to **see how features like the number of `bedrooms`, `bathrooms`, and `sqft_liv` affect the `price`** so that I can **evaluate trade-offs and identify properties that meet my needs and budget**."
    * *This suggests exploring relationships between `price` and `bedrooms`, `bathrooms`, `sqft_liv`.*

* **Example 3 (Focus on Location):**

    * "As **Sarah the Savvy Homebuyer**, I want to **compare average prices across different `zipcode`s** so that I can **identify potentially more affordable neighborhoods that still meet my criteria**."
    * *This indicates a need to analyze `price` grouped by `zipcode`.*

**Benefits of User Stories for Data Analysis:**

* **Keeps the "Why" Front and Center:** Connects data work directly to user needs.
* **Aids Prioritization:** Helps you decide which analyses are most critical.
* **Facilitates Communication:** Clearly articulates the purpose of your analysis to others (e.g., your manager or team).

### **Approach 3: Brainstorming Broad Themes & Specific, Answerable Questions**

Another approach is to start with broad themes relevant to your persona and dataset, and then drill down into specific, answerable questions.

**1. Identify Broad Themes:** Based on your persona and a general understanding of the dataset, what are the major areas of interest? For the housing data and a homebuyer persona, themes could include:

* **Price Drivers:** What makes houses more or less expensive?
* **Location Impact:** How does geography affect value?
* **Property Characteristics:** What features are common or desirable?
* **Market Conditions:** Are there any discernible trends (if the `date` field allows for meaningful time-series analysis)?
* **Value for Money:** How do condition and grade relate to price?

**2. Formulate Specific, Answerable Questions under each Theme:**

* **Theme: Price Drivers**
    * How is `price` distributed overall?
    * What is the relationship between `sqft_liv` and `price`?
    * Is there a significant price difference for properties with `waterfront` access?
    * How does the number of `bedrooms` or `bathrooms` correlate with `price`?
    * Does `yr_built` or `yr_renov` have a noticeable impact on `price`?

* **Theme: Location Impact**
    * What are the average/median prices per `zipcode`?
    * Are houses closer to certain amenities (if we could infer this from `lat`/`long`) more expensive? (This might be an advanced question requiring external data).
* **Theme: Property Characteristics**
    * What are the typical ranges for `bedrooms`, `bathrooms`, `sqft_liv`?
    * How common are basements (`sqft_basmt` > 0)?
* **Theme: Value for Money**
    * How does `condition` affect `price`, controlling for size?
    * Is there a clear price premium associated with higher `grade` levels?

**Characteristics of Good Analytical Questions:**

* **Specific:** Not too vague (e.g., "What about prices?" is too broad).
* **Measurable/Answerable with Data:** You should be able to answer them using the variables available in your dataset.
* **Relevant:** Directly related to the persona's needs or the project's goals.
* **Actionable (Ideally):** The answers might lead to a decision or a deeper understanding.

It's okay to brainstorm many questions initially. You'll prioritize them later based on relevance to your story and feasibility.

???+ question "**💡 Activity: Draft Your Analytical Questions**"

    Based on the persona you created and the techniques above (revisiting persona needs, user stories, or brainstorming themes/questions):

    1.  Draft **2-3 specific analytical questions** you want to explore using the King County housing dataset.
    2.  For each question, briefly note which variables from the data dictionary seem most relevant to answering it.

    *Example:*

    * *Question:* "How does the `grade` of a house relate to its `price`, and does this relationship differ significantly for houses with and without a `waterfront`?"
    * *Relevant Variables:* `grade`, `price`, `waterfront`.
    These questions will form the foundation for your data visualization task list in the next step.

### **Your Milestone: A Clear Set of Guiding Questions**

Regardless of whether you leaned more heavily on developing a detailed persona, crafting user stories, or brainstorming from broad themes, the **critical outcome** of this step is a clear, concise set of analytical questions. Think of these questions as the **foundation and compass for your entire data story**.

**Why is this set of questions so important at this stage?**

1.  **Defines Scope:** Your questions set the boundaries for your exploration. They help you (and your stakeholders, if any) agree on what's *in* and what's *out* of scope for this particular data story, preventing scope creep later on.
2.  **Directs Analysis:** Each question points towards specific variables to examine, relationships to investigate, and comparisons to make. This focused approach is far more efficient than aimless data dredging.
3.  **Informs Visualization Strategy:** As we'll see in the next step, these questions directly translate into what you need to visualize. A question like "How does price vary by zipcode?" immediately suggests a visualization involving the `price` and `zipcode` variables.
4.  **Guides Data Cleaning:** You'll soon discover that your attempts to answer these questions and visualize the data will highlight which data cleaning tasks are necessary. If a key variable for a crucial question has missing data, cleaning that variable becomes a priority.
5.  **Measures Success:** Ultimately, the success of your data story can be measured by how well it answers these initial questions in a clear, compelling, and accurate way for your intended audience.

**Before moving on, review the analytical questions you've drafted:**

* Are they clear and specific?
* Are they directly relevant to your persona and their goals?
* Can they be reasonably answered with the provided dataset?

If you can confidently say "yes" to these, you've successfully completed this crucial milestone. These questions are not set in stone—you might refine them or even add new ones as you explore the data—but having a strong initial set is key to a purposeful and effective data storytelling process. They are the terms of reference for the investigative journey ahead.

---

## Step 3: Initial Data Visualization Task List (V1) - Applying the Grammar of Graphics 📝🎨

You've done the crucial work of defining *what* questions you want to answer for your persona. Now, we need to determine *how* to visually represent the data to answer these questions effectively. This isn't just about picking a "pretty chart"; it's about a deliberate process of mapping data to visual elements to create clarity and insight.

### **Recap: The Grammar of Graphics in Altair**

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

### **Translating Research Questions into Visual Specifications**

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

### **Creating the Initial Data Visualization Task List (V1)**

Now it's time to formalize these ideas into an initial task list. This list will be your guide as you start coding your visualizations. For each analytical question, create an entry that outlines your visual plan.

**Example Task List Entry:**

| Research Question                                       | Data Variables                     | Mark Type    | Key Encodings (X, Y, Color, Size, Tooltip, etc.)                                                                        | Anticipated Chart Type | Notes / Potential Challenges                                    |
| :------------------------------------------------------ | :--------------------------------- | :----------- | :-------------------------------------------------------------------------------------------------------------------- | :--------------------- | :-------------------------------------------------------------- |
| How is `price` distributed overall?                     | `price`                            | `bar`        | `x: price` (binned), `y: count()`                                                                                     | Histogram              | Might need to handle outliers or try log scale for price.       |
| How does `sqft_liv` relate to `price`?                | `price`, `sqft_liv`, `waterfront`  | `point`      | `x: sqft_liv (Q)`, `y: price (Q)`, `color: waterfront (N)`, `tooltip: [price, sqft_liv, bedrooms]`                    | Scatter Plot           | Large number of points might cause overplotting.                |
| What's the average `price` by number of `bedrooms`?   | `price`, `bedrooms`                | `bar`        | `x: bedrooms (O)`, `y: mean(price) (Q)`, `tooltip: [mean(price), count()]`                                              | Bar Chart              | Ensure `bedrooms` is treated as ordinal/discrete. Consider outliers. |

!!! warning "**Emphasis: This is Version 1!**"

    This task list is your *starting hypothesis*. As you actually create these visualizations (Phase 3), you'll inevitably discover things:

    * The data might not be in the format you expected.
    * A chosen mark or encoding might not work as well as you thought.
    * The visualization might reveal unexpected patterns that lead to new questions or a desire to try a different visual approach.

    This is perfectly normal! Your task list is a **living document** that you will update and refine.

???+ question "**💡 Activity: Draft Your Initial Visualization Task List**"

    Take the 2-3 analytical questions you drafted in Step 2. For each question:

    1.  Identify the primary **data variables** involved.
    2.  Choose an appropriate **mark type** from Altair.
    3.  Define the **key encodings** (at least `x` and `y`, but also consider `color`, `size`, `tooltip`, etc.). Think about the `type` for each encoding (Quantitative, Nominal, Ordinal, Temporal).
    4.  (Optional but helpful) Note the anticipated common chart type that these choices would produce.
    5.  (Optional) Briefly note any initial thoughts or potential challenges.

    Create a simple table or list for your tasks, similar to the example above. This will be your first concrete plan for building your data story's visuals.



### **Your Milestone: A Visual Blueprint**

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

