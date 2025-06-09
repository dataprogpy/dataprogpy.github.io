---
icon: material/numeric-5
---


### 5. Phase 4: Building the Narrative - Horizontal Development

In this phase, we shift from perfecting individual charts to arranging them in a logical sequence to tell a compelling story. We're moving from individual "panels" to creating a "comic strip."

#### Step 8: Creating a Cohesive Set of Visuals (The "Comic Strip") 🖼️➡️🖼️➡️🖼️

**Horizontal visual development** is about thoughtfully selecting and sequencing multiple visualizations to guide your audience through your findings, building understanding layer by layer, much like a comic strip tells a story panel by panel.

**Key Principles of Horizontal Development:**

1.  **Logical Flow:** The sequence of visuals should follow a logical progression. This might mean:
    * **Overview first, then details:** Start with a high-level summary (e.g., overall distribution of prices) and then drill down into specifics (e.g., price drivers, comparisons).
    * **Problem/Question, then evidence/answer:** Pose a question implicitly or explicitly with one visual, then use subsequent visuals to explore and answer it.
    * **Building complexity:** Start with simpler relationships and introduce more variables or facets gradually.
2.  **Complementary Information:** Each visual in the sequence should add a new piece of information or a different perspective, building upon the previous ones. Avoid redundancy unless it's for intentional emphasis.
3.  **Clear Transitions:** While we'll discuss narrative text later (Step 9), the visual transition itself should feel natural. The audience should understand why they are seeing the next chart in the sequence.
4.  **Audience Focus (Revisit Your Persona!):** The chosen sequence should directly address the key questions and goals of your audience persona (from Step 2). What order of information would make the most sense *to them*?
5.  **Consistent Aesthetics (Where Appropriate):** Using consistent color palettes for the same categories across different charts, similar font styles, and a generally unified design language can make the "story" feel more professional and easier to follow. However, variations can be used intentionally to highlight changes in focus.

**Example: Building a Mini-Story for "Sarah the Savvy Homebuyer"**

Let's imagine Sarah wants to understand the King County housing market, focusing on price, size, and location. Here's a possible sequence of 2-3 visuals (using concepts from our cleaned data):

**Visual 1: Overall Price Landscape**
* **Chart Type:** Histogram of `price_log` (log-transformed prices).
* **Purpose:** Give Sarah an understanding of the general distribution of house prices – what's typical, the range, and the shape of the market.
* **Key Insight:** Most houses fall within a certain price band, but there's a long tail of more expensive properties.

    ```python
    # (Conceptual Altair code - assume housing_df is cleaned)
    # import altair as alt

    # price_histogram = alt.Chart(housing_df).mark_bar().encode(
    #     alt.X('price_log:Q', bin=alt.Bin(maxbins=30), title='Log of Sale Price'),
    #     alt.Y('count()', title='Number of Houses')
    # ).properties(
    _       # title='Distribution of House Prices (Log Scale)',
    #     width=500, height=300
    # )
    # price_histogram.show()
    ```
    *(Image of the price histogram would be displayed here in MkDocs)*

**Visual 2: Price vs. Key Feature (Living Area)**
* **Chart Type:** Scatter plot of `price_log` vs. `sqft_liv`, perhaps with `grade` encoded by color.
* **Purpose:** Show Sarah how a primary feature (living area) relates to price, and if construction grade plays a role.
* **Key Insight:** Generally, as living area increases, price increases. Higher grade houses tend to command higher prices for similar sizes.

    ```python
    # (Conceptual Altair code)
    # price_vs_sqft_scatter = alt.Chart(housing_df).mark_point(opacity=0.3).encode(
    #     alt.X('sqft_liv:Q', title='Living Area (sq ft)'),
    #     alt.Y('price_log:Q', title='Log of Sale Price'),
    #     alt.Color('grade:O', title='Construction Grade', scale=alt.Scale(scheme='viridis')), # :O for Ordinal
    #     tooltip=[alt.Tooltip('price:Q', title='Price', format='$,.0f'), 'sqft_liv', 'grade']
    # ).properties(
    #     title='Price vs. Living Area by Construction Grade',
    #     width=500, height=300
    # ).interactive()
    # price_vs_sqft_scatter.show()
    ```
    *(Image of the scatter plot would be displayed here)*

**Visual 3: Price Variation by Location (Zip Code)**
* **Chart Type:** Bar chart (or box plot) showing the distribution of `price_log` (or median `price`) for several key `zipcode`s. Alternatively, a choropleth map if geo-data for zip codes is available and joined.
* **Purpose:** Help Sarah understand how prices vary geographically.
* **Key Insight:** There are noticeable price differences across zip codes, with some areas being significantly more expensive than others.

    ```python
    # (Conceptual Altair code - bar chart of median prices for top N zipcodes by count)
    # top_zipcodes = housing_df['zipcode'].value_counts().n_largest(10).get_column('zipcode')
    # filtered_df_zipcodes = housing_df.filter(pl.col('zipcode').is_in(top_zipcodes))

    # price_by_zipcode_bar = alt.Chart(filtered_df_zipcodes).mark_bar().encode(
    #     alt.X('zipcode:N', title='Zip Code', sort='-y'), # :N for Nominal
    #     alt.Y('median(price):Q', title='Median Sale Price (USD)', axis=alt.Axis(format='$,.0f')),
    #     tooltip=[alt.Tooltip('median(price):Q', title='Median Price', format='$,.0f'), 'count()']
    # ).properties(
    #     title='Median House Prices by Zip Code (Top 10 by Volume)',
    #     width=500, height=300
    # )
    # price_by_zipcode_bar.show()
    ```
    *(Image of the bar chart would be displayed here)*

This sequence tells a story: "Here's the overall market (Visual 1), here's how a key feature like size influences price (Visual 2), and here's how location further impacts it (Visual 3)."

**Slicing, Dicing, and Faceting for Richer Narratives:**

Part of horizontal development can also involve showing different "slices" or "facets" of your data to provide deeper insights or comparisons.

* **Filtering:** You might show a general trend, then show the same chart filtered for a specific subgroup (e.g., "Here's `price` vs. `sqft_liv` for all houses, and now here it is just for `waterfront` properties").
* **Faceting (Small Multiples):** Instead of multiple separate charts, you can use faceting (e.g., `facet` or `row`/`column` encodings in Altair) to create a grid of charts that show the same relationship broken down by different categories. For example, you could facet the `price` vs. `sqft_liv` scatter plot by `bedrooms`. This allows for easy comparison across categories.

    ```python
    # (Conceptual Altair code for faceting)
    # price_vs_sqft_faceted = alt.Chart(housing_df).mark_point(opacity=0.4).encode(
    #     alt.X('sqft_liv:Q', title='Living Area (sq ft)'),
    #     alt.Y('price_log:Q', title='Log of Sale Price'),
    #     alt.Column('bedrooms:O', title='Number of Bedrooms') # Facet by bedrooms
    # ).properties(
    #     title='Price vs. Living Area, Faceted by Number of Bedrooms',
    #     width=200, height=200 # Adjust size for facets
    # ).interactive()
    # price_vs_sqft_faceted.show()
    ```
    *(Image of the faceted chart would be displayed here)*

The goal is to create a journey for your audience, leading them logically from one piece of evidence to the next, ultimately supporting your main message or answering their key questions.

---

> **💡 Activity: Outline Your "Comic Strip"**
>
> 1.  Review your **analytical questions (Step 2)** for your chosen persona and your **updated Data Visualization Task List (from Step 7)**.
> 2.  Select **2-3 refined visualizations** from your list that you believe can be sequenced to tell a mini-story or answer a more complex question for your persona.
> 3.  **Outline this sequence:**
>     * For each visual in the sequence:
>         * Briefly describe the chart (e.g., "Histogram of log prices," "Scatter of price vs. sqft_liv colored by grade").
>         * What is the key insight or piece of information this specific visual contributes to the mini-story?
>         * How does it connect to the previous visual (if any) and lead to the next?
> 4.  (Optional) Think about whether filtering or faceting could enhance part of your mini-story.
>
> You don't need to code these all perfectly right now, but sketch out the narrative flow. This plan will guide the final assembly of your data story.

---

This step encourages students to think about the narrative structure of their data story by sequencing their existing, refined charts. Next, in Step 9, we'll focus on adding the actual narrative text and annotations to tie these visuals together explicitly.

How does this look for Step 8?

That's an excellent idea! Step 8 represents a shift from refining individual components to synthesizing them into a larger narrative. It's a perfect juncture to pause, take stock, and ensure students feel confident about the foundation they've built.

Let's insert a "Checkpoint: Am I Ready to Build the Narrative?" section at the very beginning of Step 8, before we dive into the "Horizontal visual development" details.

---

### 5. Phase 4: Building the Narrative - Horizontal Development

Before we dive into arranging multiple visuals into a cohesive story, let's take a moment for a quick checkpoint. You've come a long way, and it's good to ensure your "virtual workspace" is organized and you're ready for this next stage of narrative construction.

#### **Checkpoint: Am I Ready to Build the Narrative?** 📍

You've journeyed through understanding your data, defining analytical goals, making initial visualization plans, encountering the "reality checks" of data issues, and then iteratively cleaning your data and refining individual visualizations. This is a significant amount of work!

**What Your "Virtual Workspace" Might Look Like Now:**

By this point, you should ideally have:

1.  **Cleaned(er) Data:** A version of your Polars DataFrame (`housing_df`) that reflects the cleaning and transformation tasks you deemed necessary in Step 6 and executed in Step 7 (e.g., parsed dates, transformed variables like `price_log`, newly engineered features like `price_per_sqft`).
2.  **Refined Individual Visualizations:** Python scripts or notebook cells with Altair code for several *individually developed and refined* charts. These are the "panels" you've been working on, likely improved through the iterative cycle of Step 7.
3.  **Updated Task Lists:**
    * Your **Data Cleaning Task List** should show completed tasks.
    * Your **Data Visualization Task List** is likely at V2 or V3, reflecting the refinements made to your initial ideas, and perhaps even some new visual ideas sparked during exploration.
4.  **Clear Analytical Questions:** Your guiding analytical questions (from Step 2) for your persona should still be front-of-mind, as these are what your story aims to answer.

**Self-Assessment - "Am I Doing Okay?"**

Ask yourself:

* "Do I have 2-4 refined visualizations whose code I understand and that I believe are now reasonably effective at showing a specific aspect of the data (e.g., a distribution, a relationship, a comparison)?"
* "Does the data feeding these visuals now seem appropriate for their purpose, thanks to the cleaning I've done?"
* "Can I look at my refined individual charts and clearly articulate which analytical question (from Step 2) each one helps to answer?"
* "Do I feel the 'Uh-Oh' moments from Step 5 have been largely addressed for these key visuals?"

If you can answer "yes" or "mostly yes" to these, you're in great shape to start thinking about how to weave these visuals together into a story. If some areas still feel a bit shaky, that's okay too! This workflow is iterative. You can always revisit earlier steps. For now, proceed with the understanding that your current set of refined visuals will be the building blocks for your narrative.

This checkpoint serves to ensure you're building your story on a solid foundation of well-understood, individually refined components. Now, let's explore how to arrange these into a compelling sequence.

---
*(The rest of Step 8, starting with "#### Step 8: Creating a Cohesive Set of Visuals (The 'Comic Strip') 🖼️➡️🖼️➡️🖼️", would then follow as previously outlined.)*

This addition should help students feel more grounded and prepared as they move into the narrative construction phase. It provides a moment for reflection and self-correction, which is very valuable in a learning process.

Ready to continue with the rest of Step 8 as previously drafted, or any other thoughts on this checkpoint?

You're raising a very relevant point! Altair's operator overloading (e.g., `|` for horizontal concatenation, `&` for vertical concatenation, `+` for layering) is a powerful and idiomatic way to combine charts. Given that Step 8 is all about creating a "cohesive set of visuals" or a "comic strip," introducing these operators here makes a lot of sense. It provides a direct way to implement the "horizontal development" concept within Altair itself.

Let's refine Step 8 to include a discussion and example of these operators. This will give students a concrete tool to arrange their charts programmatically if they wish, which is especially useful for dashboards or more complex layouts.

---

*(Following the "Checkpoint: Am I Ready to Build the Narrative?" section)*

#### Step 8: Creating a Cohesive Set of Visuals (The "Comic Strip") 🖼️➡️🖼️➡️🖼️

**Horizontal visual development** is about thoughtfully selecting and sequencing multiple visualizations to guide your audience through your findings, building understanding layer by layer, much like a comic strip tells a story panel by panel.

*(Key Principles of Horizontal Development: Logical Flow, Complementary Information, Clear Transitions, Audience Focus, Consistent Aesthetics - these remain as previously outlined)*

**Arranging Charts with Altair: Concatenation Operators**

Altair makes it easy to combine multiple charts into a single visualization object using intuitive operators:

* `chart1 | chart2`: **Horizontal Concatenation (hconcat)**. Places `chart2` to the right of `chart1`. Perfect for a "comic strip" sequence.
* `chart1 & chart2`: **Vertical Concatenation (vconcat)**. Places `chart2` below `chart1`. Useful for stacking related views.
* `chart1 + chart2`: **Layering**. Places `chart2` on top of `chart1`. Both charts must share the same x and y scales. This is primarily for "vertical development" within a single conceptual chart (e.g., adding a regression line to a scatter plot), which we touched upon in Step 7, but it's part of the same family of operators.

These operators allow you to build complex, multi-view displays from simpler components.

**Example: Building a Mini-Story for "Sarah the Savvy Homebuyer" (with Concatenation)**

Let's revisit Sarah's journey to understand the King County housing market. We'll use the same conceptual charts as before but imagine combining two of them side-by-side using horizontal concatenation (`|`).

**Visual 1: Overall Price Landscape** (Histogram of `price_log`)
*(Conceptual Altair code for `price_histogram` as previously shown)*

**Visual 2: Price vs. Key Feature (Living Area)** (Scatter plot of `price_log` vs. `sqft_liv`)
*(Conceptual Altair code for `price_vs_sqft_scatter` as previously shown)*

Now, let's combine them:

```python
# (Conceptual Altair code - assume price_histogram and price_vs_sqft_scatter are defined Altair chart objects)
# import altair as alt

# Combined_story_part1 = price_histogram | price_vs_sqft_scatter

# # To resolve scales across concatenated charts if needed, you might use:
# # Combined_story_part1 = alt.hconcat(price_histogram, price_vs_sqft_scatter).resolve_scale(
# #     # color='independent', # Example if they used different color scales
# #     # legend='independent'
# # )


# Combined_story_part1.show()
# print("Horizontally concatenated charts generated.")
```
*(Image of the two charts displayed side-by-side would be shown here in MkDocs)*

This `Combined_story_part1` object now holds both charts, arranged horizontally. You could then follow this with `Visual 3: Price Variation by Location (Zip Code)` displayed separately or even concatenated vertically below the first two if the layout made sense.

The sequence still tells a story: "Here's the overall market (Visual 1, left panel), and here's how a key feature like size influences price (Visual 2, right panel)."

**Slicing, Dicing, and Faceting for Richer Narratives:**

*(This section remains as previously outlined, discussing filtering and faceting as ways to enrich the narrative. Faceting itself is a form of small multiple display that Altair handles elegantly within a single chart specification, but filtering can be used to create distinct charts that are then concatenated.)*

The goal is to create a journey for your audience, leading them logically from one piece of evidence to the next, ultimately supporting your main message or answering their key questions. Using Altair's concatenation operators can be a neat way to package parts of this journey.

---

> **💡 Activity: Outline Your "Comic Strip" (and Consider Concatenation)**
>
> 1.  Review your **analytical questions (Step 2)** for your chosen persona and your **updated Data Visualization Task List (from Step 7)**.
> 2.  Select **2-3 refined visualizations** from your list that you believe can be sequenced to tell a mini-story or answer a more complex question for your persona.
> 3.  **Outline this sequence:**
>     * For each visual in the sequence:
>         * Briefly describe the chart (e.g., "Histogram of log prices," "Scatter of price vs. sqft_liv colored by grade").
>         * What is the key insight or piece of information this specific visual contributes to the mini-story?
>         * How does it connect to the previous visual (if any) and lead to the next?
> 4.  **(New!) Consider Altair Concatenation:** If you were to present these 2-3 visuals together as a single compound figure, would you use horizontal (`|`) or vertical (`&`) concatenation? Sketch out how that might look or even try coding it if you have your individual chart objects ready.
> 5.  (Optional) Think about whether filtering or faceting could enhance part of your mini-story.
>
> You don't need to code these all perfectly right now, but sketch out the narrative flow and consider how Altair's operators could help you present it. This plan will guide the final assembly of your data story.

---
This revision introduces Altair's concatenation operators, providing students with a powerful tool directly relevant to arranging multiple visuals. It also updates the activity to encourage them to think about using these operators.

This should enhance the practical Altair skills students take away from this section, connecting the conceptual "horizontal development" to a concrete implementation technique.

How does this revised Step 8 look?
Okay, excellent. You've now outlined a sequence of visuals – your "comic strip" – designed to guide your audience through a mini-story embedded in the data. You've even considered how Altair's concatenation operators could help present these visuals together.

But visuals, even when sequenced well, rarely tell the *entire* story on their own. They need context, explanation, and a guiding voice. That's where narrative text and annotations come in.

---

#### Step 9: Adding Narrative Text and Annotations 🗣️✍️

This step is about weaving your visuals together with words to create a complete and understandable data story. Well-chosen text and carefully placed annotations bridge the gap between what the data *shows* and what the audience *understands*.

**The Role of Narrative Text: Why Words Matter**

* **Contextualization:** Text provides background, explains what the audience is looking at, and sets the stage for the insights revealed in the visuals.
* **Explanation of Insights:** While a chart might show a pattern, text explicitly calls out the key takeaways, answers the "so what?" question, and clarifies complex points.
* **Guidance and Flow:** Words act as signposts, guiding the audience's attention from one visual to the next, ensuring a smooth narrative flow.
* **Reinforcement:** Key messages can be reinforced by stating them in text alongside their visual representation.
* **Addressing Nuance:** Text can address subtleties, limitations of the data, or alternative interpretations that might not be obvious from the visuals alone.

**Types of Narrative Text:**

1.  **Overall Story Title:** A compelling title for your entire data story that grabs attention and summarizes the main theme.
    * *Example:* "Navigating King County's Housing Market: Insights for First-Time Buyers"

2.  **Chart Titles:** Every chart needs a clear, descriptive title that immediately tells the audience what the chart is about.
    * *Bad Title:* "Price Data"
    * *Good Title (for Visual 1 in our Step 8 example):* "Most Homes Cluster in Mid-Range Prices, With a Tail of Luxury Properties" or "Distribution of King County House Prices (Log Scale)"

3.  **Captions or Introductory/Explanatory Text:** Short paragraphs or sentences accompanying each visual or a sequence of visuals.
    * **What to include:**
        * Briefly state the main insight the visual is intended to convey.
        * Explain any important features or encodings if they aren't immediately obvious.
        * Point out what the audience should be looking for.
    * *Example (for Visual 2: Price vs. Living Area by Grade):*
        "As expected, larger homes generally command higher prices. The color-coding by construction grade further reveals that higher-grade homes (shown in brighter yellows) tend to be priced above lower-grade homes of similar size, indicating a premium for quality."

4.  **Transitional Text:** Words or phrases that create a smooth link from one visual or point to the next.
    * *Examples:* "Having seen the overall price landscape, let's now explore what drives these prices..."
        "Beyond size and grade, location also plays a significant role..."
        "This leads us to another important factor: ..."

**Annotations: Highlighting Directly on Your Charts**

Annotations are textual or graphical elements placed *directly onto a chart* to highlight specific data points, trends, or areas of interest. They are powerful tools for guiding audience attention precisely where you want it.

* **Why Annotate?**
    * Draw attention to key outliers, specific values, or important thresholds.
    * Explain an anomaly or a significant event within the data.
    * Add context directly where it's needed.

* **Simple Altair Annotation Examples:**
    Altair allows for annotations, often by layering additional marks like `mark_text()` or `mark_rule()`.

    * **Highlighting a specific point with `mark_text()`:**
        Imagine you wanted to label a particularly interesting house in your `price_log` vs. `sqft_liv` scatter plot. You might create a small DataFrame with the details of that point and layer a `mark_text` chart.

        ```python
        # (Conceptual - assume 'final_scatter_plot' from Step 7 and housing_df exists)
        # highlight_data = pl.DataFrame({
        #     'sqft_liv': [3000],  # Sqft of the house to highlight
        #     'price_log': [housing_df.filter(pl.col('sqft_liv') == 3000)['price_log'].mean()], # Example Y
        #     'text': ['Example High Value Home']
        # })
        #
        # annotation_text = alt.Chart(highlight_data).mark_text(
        #     align='left',
        #     baseline='middle',
        #     dx=7  # Offset text slightly to the right
        # ).encode(
        #     x='sqft_liv:Q',
        #     y='price_log:Q',
        #     text='text:N'
        # )
        #
        # annotated_scatter_plot = final_scatter_plot + annotation_text
        # annotated_scatter_plot.show()
        ```

    * **Adding a reference line with `mark_rule()`:**
        If you wanted to show the median `price_log` on your histogram.

        ```python
        # (Conceptual - assume 'price_histogram' and housing_df with 'price_log' exists)
        # median_price_log = housing_df['price_log'].median()
        #
        # median_rule = alt.Chart(pl.DataFrame({'median_val': [median_price_log]})).mark_rule(color='red', strokeDash=[3,3]).encode(
        #     y='median_val:Q'
        # )
        #
        # histogram_with_median_line = price_histogram + median_rule # Layering the rule
        # histogram_with_median_line.show()
        ```
    *Keep annotations sparse and purposeful. Too many can clutter the visual.*

**Key Considerations for Text and Annotations:**

* **Audience and Purpose (Again!):** Use language your persona understands. Focus on what's relevant to their goals and your analytical questions (Step 2).
* **Conciseness:** Be brief and to the point. Avoid jargon where possible. Every word should add value.
* **Clarity:** Ensure your explanations are unambiguous.
* **Integration:** Text and visuals should work together seamlessly. The text should refer to what's in the visual, and the visual should support the text.
* **Placement:** Position text and annotations thoughtfully so they are easy to read and don't obscure important data.

---

> **💡 Activity: Draft Your Narrative and Annotations**
>
> 1.  Take the "Comic Strip" outline (sequence of 2-3 visuals) you created in Step 8.
> 2.  **For your sequence, draft the following narrative text:**
>     * A compelling **overall title** for this mini-story.
>     * A clear, descriptive **title for each visual** in your sequence.
>     * A short **caption or introductory sentence** for each visual, explaining its main takeaway or what the audience should focus on.
>     * Brief **transitional phrases or sentences** to link one visual to the next.
> 3.  **Identify Annotation Opportunities:**
>     * For at least one of your visuals, identify **1-2 specific annotations** that would help guide attention or clarify an important point (e.g., highlighting an outlier, marking an average, labeling a specific segment).
>     * Briefly describe what the annotation would say or show. You don't need to code it in Altair for this activity unless you're comfortable doing so, but think about its purpose and placement.
>
> Write this down. This brings you one step closer to a fully formed data story!

---

This step helps students understand the importance of words in data storytelling and gives them practice in crafting the narrative elements that will accompany their visuals. The final step in the workflow (Step 10) will be about reviewing the complete package.

How does this look for Step 9?

Okay, excellent. You've now assembled a sequence of visuals and drafted the narrative text and potential annotations to guide your audience. You essentially have a first draft of your data story!

Before we call it complete, there's one more crucial phase: Review and Validation. This is where you step back, look at what you've created with a critical eye, and ensure it truly achieves the goals you set out at the beginning.

---
