---
icon: material/numeric-2
---

# King County House Sales: Data Dictionary

[source: GeoDa Data and Lab](https://geodacenter.github.io/data-and-lab/KingCounty-HouseSales2015/)

See the [code-samples repo](https://github.com/dataprogpy/code-samples/tree/main/data) to download data.

| #  | Variable | Description |
| -- |:---|:---|
|  1 | id | Identification |
|  2 | date | Date sold |
|  3 | price | Sale price |
|  4 | bedrooms | Number of bedrooms |
|  5 | bathrooms | Number of bathrooms |
|  6 | sqft_liv | Size of living area in square feet |
|  7 | sqft_lot | Size of the lot in square feet |
|  8 | floors | Number of floors |
|  9 | waterfront | ‘1’ if the property has a waterfront, ‘0’ if not. |
| 10 | view | An index from 0 to 4 of how good the view of the property was |
| 11 | condition | Condition of the house, ranked from 1 to 5 |
| 12 | grade | Classification by construction quality which refers to the types of materials used and the quality of workmanship. Buildings of better quality (higher grade) cost more to build per unit of measure and command higher value. Additional information in: [KingCounty](http://info.kingcounty.gov/assessor/esales/Glossary.aspx?type=r) |
| 13 | sqft_above | Square feet above ground |
| 14 | sqft_basmt | Square feet below ground |
| 15 | yr_built | Year built |
| 16 | yr_renov | Year renovated. ‘0’ if never renovated |
| 17 | zipcode | 5 digit zip code |
| 18 | lat | Latitude |
| 19 | long | Longitude |
| 20 | squft_liv15 | Average size of interior housing living space for the closest 15 houses, in square feet |
| 21 | squft_lot15 | Average size of land lots for the closest 15 houses, in square feet |
| 22 | Shape_leng | Polygon length in meters |
| 23 | Shape_Area | Polygon area in meters |

Okay, let's dive into the first phase of our workflow.

---

### 2. Phase 1: Understanding the Landscape & Defining Goals

Every data story begins with understanding the raw material: your data. But data alone doesn't speak; we need to give it a voice by asking the right questions and defining clear goals for our narrative. This phase is about laying that crucial groundwork.

#### Step 1: The Data Dictionary - Your Starting Point 🗺️

Before you can ask meaningful questions of your data, you need to understand what information you have, what each piece of information means, its format, and any potential limitations. This is where the **data dictionary** comes in. It's your map to the dataset.

For this lesson, we'll be working with a dataset containing information about house sales in King County, WA (which includes Seattle). Here's a look at its structure:

**(Imagine this is a nicely formatted table in MkDocs)**

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

> **💡 Activity: Initial Variable Brainstorm**
>
> Jot down 3-5 variables from this list that you think could be central to understanding housing prices or trends in this area. Consider why each might be important. We'll use this as a starting point for formulating questions.

---

This covers Step 1. Next, we'll move into Step 2: Articulating Analytical Goals & Research Questions, where we start thinking about what stories this data might tell. How does this look?
