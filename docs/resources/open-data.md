---
icon: material/database-eye-outline
---

-----

## Course Project Data Sources

Below is a list of data sources that you may consider for your semester-long project. When choosing a dataset, remember to consider the project requirements: you'll need to be able to wrangle it with **Polars**, visualize it with **Altair**, and apply **scikit-learn** models. Also, think about whether your chosen dataset could support the **optional extra credit** in time-series or text analysis. 

!!! note "Not an Exhaustive/Authoritative List"

    This list is intended to help you get started; it is neither exhaustive nor authoritative. Please feel free to explore sources beyond those listed here. Choosing data that you find personally meaningful can often lead to a more engaging and well-executed project

For each data source, you'll find a brief introduction and some tips to help you get started. Ensure your chosen dataset is **readily accessible** and that you can create a comprehensive **data dictionary** for it.

-----

### General Dataset Search Engines

These are great starting points if you have a topic in mind but don't know where to find relevant data.

#### 1\. Google Dataset Search

  
  **Link:** [https://datasetsearch.research.google.com/](https://datasetsearch.research.google.com/)
  
  **Short Description:** A search engine specifically for datasets. It indexes datasets from various sources across the web.
  
  **Tips:**

  * Use keywords related to your interests (e.g., "climate change," "mental health," "housing prices").
  * Check the source of the dataset to ensure reliability and accessibility.
  * Look for common file formats like CSV, JSON, or Excel.
  * **Project Fit:** Can lead to virtually any type of data, so evaluate carefully against project requirements.

#### 2\. Data.gov

  
  **Link:** [https://catalog.data.gov/dataset](https://catalog.data.gov/dataset)
  
  **Short Description:** The central repository for U.S. government open data. Provides access to a vast collection of datasets from federal agencies.
  
  **Tips:**

  * The sheer volume can be overwhelming; use filters for topics, formats (CSV, Excel, XML, JSON), and agencies.
  * Look for datasets with good metadata or accompanying documentation to help create your data dictionary.
  * Data quality and cleanliness can vary, offering good wrangling practice.
  * **Project Fit:** Suitable for a wide array of topics including health, environment, public safety, and economics. Many datasets are suitable for regression/classification and some for time-series analysis.

#### 3\. Awesome Public Datasets (GitHub)

  
  **Link:** [https://github.com/awesomedata/awesome-public-datasets](https://github.com/awesomedata/awesome-public-datasets)
  
  **Short Description:** A community-curated list of high-quality public datasets, often categorized by topic (e.g., agriculture, education, finance).
  
  **Tips:**

  * Browse by category to find datasets matching your interests.
  * Links usually go directly to the dataset or a page describing it.
  * Great for finding datasets that are already somewhat vetted by the community.
  * **Project Fit:** Diverse topics; check individual datasets for suitability with Polars, Altair, and scikit-learn.

#### 4\. DataIsPlural

  
  **Link:** [https://www.data-is-plural.com/](https://www.data-is-plural.com/)
  
  **Short Description:** A weekly newsletter (with a searchable archive) that highlights interesting and often unique datasets.
  
  **Tips:**

  * Explore the archive for past datasets.
  * Good for finding timely or niche datasets you might not discover elsewhere.
  * Pay attention to the original source and data format.
  * **Project Fit:** Excellent for unique project ideas. Data will vary widely.

-----

### Machine Learning & General Purpose Repositories

These sites host datasets often used in machine learning and data science.

#### 5\. Kaggle

  
  **Link:** [https://www.kaggle.com/datasets](https://www.kaggle.com/datasets)
  
  **Short Description:** A popular platform for data science competitions, datasets, and notebooks. Offers a vast range of datasets on diverse topics.
  
  **Tips:**

  * Many datasets are in CSV format and are relatively clean, but always perform your own EDA.
  * Explore existing notebooks for inspiration on data wrangling and analysis (but ensure your work is original).
  * Check dataset licenses for any usage restrictions.
  * Good source for text data (e.g., product reviews, tweets) and time-series data.
  * **Project Fit:** Excellent for all project components. Many datasets are well-suited for classification, regression, and the extra credit components.

#### 6\. OpenML

  
  **Link:** [https://www.openml.org/search?type=data](https://www.openml.org/search?type=data)
  
  **Short Description:** An open science platform for machine learning that allows sharing of datasets, algorithms, and experimental results.
  
  **Tips:**

  * Datasets are often well-documented and structured for machine learning tasks.
  * You can filter datasets by task type (e.g., classification, regression).
  * Good for finding benchmark datasets to test your modeling skills.
  * **Project Fit:** Strong for the modeling component. Provides clear features and targets.

#### 7\. UCI Machine Learning Repository

  
  **Link:** [https://archive.ics.uci.edu/ml/index.php](https://archive.ics.uci.edu/ml/index.php)
  
  **Short Description:** One of the oldest and most well-known repositories of datasets used for machine learning research.
  
  **Tips:**

  * Contains a wide variety of datasets, often classic ones used in many studies.
  * Data formats can sometimes be older or require more parsing (e.g., .data files without headers). Check for accompanying `.names` files or descriptions.
  * Many datasets have clear descriptions of variables and potential ML tasks.
  * **Project Fit:** Excellent for practicing data wrangling and modeling. Good for both supervised and unsupervised learning tasks.

-----

### Government & Public Sector Data

These sources provide official data from governmental and international organizations.

#### 8\. U.S. Census Bureau

  
  **Link:** [https://data.census.gov/](https://data.census.gov/)
  
  **Short Description:** Provides a wealth of data about the U.S. population and economy.
  
  **Tips:**

  * `data.census.gov` is the primary data portal. It can be complex to navigate; use the advanced search and filtering options.
  * American Community Survey (ACS) data is very detailed.
  * Data often comes in large tables; you'll likely need to select specific variables and geographies.
  * Excellent for demographic analysis and creating rich features for other projects.
  * **Project Fit:** Great for projects involving demographics, economics, and social trends. Data often requires significant wrangling.

#### 9\. CDC Data (Centers for Disease Control and Prevention)

  
  **Link:** [https://open.cdc.gov/data.html](https://open.cdc.gov/data.html)
  
  **Short Description:** Offers a wide range of public health data, including surveillance data, statistics, and information on various health topics.
  
  **Tips:**

  * The "CDC WONDER" (Wide-ranging Online Data for Epidemiologic Research) system is a key resource.
  * Data is often available in user-friendly formats (CSV, Excel).
  * Explore topics like chronic diseases, infectious diseases, behavioral risk factors, and environmental health.
  * **Project Fit:** Ideal for public health projects. Datasets can support regression, classification, and time-series analysis (e.g., disease trends).

#### 10\. USAFacts

  
  **Link:** [https://usafacts.org/](https://usafacts.org/)
  
  **Short Description:** A non-profit organization that compiles and visualizes data from U.S. government sources. Aims to provide a data-driven portrait of the nation.
  
  **Tips:**

  * Presents data in an accessible way, often with charts and reports. Look for links to download the underlying data (usually CSV).
  * Covers topics like population, economy, health, and education.
  * Useful for projects focusing on U.S. trends and government metrics.
  * **Project Fit:** Good for projects requiring aggregated US data. Data is typically clean.

#### 11\. WHO Data (World Health Organization)

  
  **Link:** [https://www.who.int/data/collections](https://www.who.int/data/collections)
  
  **Short Description:** The primary source for global health statistics and information.
  
  **Tips:**

  * Explore their "Data Collections" and the Global Health Observatory (GHO) data repository.
  * Data covers global health priorities, diseases, health systems, and mortality.
  * Pay attention to indicator definitions and data collection methodologies.
  * **Project Fit:** Suitable for projects with a global health focus. Many datasets lend themselves to time-series analysis and cross-country comparisons.

#### 12\. NHTSA Datasets (National Highway Traffic Safety Administration)

  
  **Link:** [https://www.nhtsa.gov/nhtsa-datasets-and-apis](https://www.nhtsa.gov/nhtsa-datasets-and-apis)
  
  **Short Description:** Provides data related to traffic safety, including crash statistics, vehicle safety ratings, and recall information.
  
  **Tips:**

  * The Fatality Analysis Reporting System (FARS) is a rich dataset for detailed crash analysis.
  * Data can be very detailed and may require careful study of documentation and coding manuals.
  * Files can be large, offering good practice with Polars for efficient data handling.
  * **Project Fit:** Excellent for projects on transportation safety, risk analysis. Good for classification/regression (e.g., predicting crash severity).

#### 13\. World Bank Open Data

  
  **Link:** [https://databank.worldbank.org/source/world-development-indicators](https://databank.worldbank.org/source/world-development-indicators)
  
  **Short Description:** Provides free and open access to global development data.
  
  **Tips:**

  * Includes a vast collection of time-series data on indicators like GDP, poverty, education, and environment from countries around the world.
  * The World Development Indicators (WDI) is a flagship collection.
  * Data can be downloaded in various formats, including CSV and Excel.
  * **Project Fit:** Excellent for projects on international development, economics, and socio-economic trends. Very suitable for time-series analysis and cross-sectional studies.

-----

### City-Specific Open Data Portals

Many cities now have open data portals, which are fantastic for local or urban-focused projects.

#### 14\. NYC OpenData

  
  **Link:** [https://opendata.cityofnewyork.us/data/](https://opendata.cityofnewyork.us/data/)
  
  **Short Description:** Comprehensive portal for data from New York City agencies.
  
  **Tips:**

  * Huge variety of datasets: public safety, transportation (taxi trips are famous), environment, health, education, etc.
  * Often have APIs and various download formats (CSV, JSON).
  * Many datasets have temporal components, good for time-series.
  * **Project Fit:** Great for urban analytics projects. Data quality varies.

#### 15\. Data WPRDC (Western Pennsylvania Regional Data Center)

  
  **Link:** [https://data.wprdc.org/dataset/?organization=city-of-pittsburgh](https://data.wprdc.org/dataset/?organization=city-of-pittsburgh)
  
  **Short Description:** A regional open data portal with a strong focus on the City of Pittsburgh and Allegheny County.
  
  **Tips:**

  * Covers topics like public safety, housing, transportation, and environment.
  * Good for projects with local relevance (if you're in the area or interested in Pittsburgh).
  * Check dataset update frequencies and metadata.
  * **Project Fit:** Similar to NYC OpenData, good for urban analytics.

#### 16\. City of Chicago Data Portal

  
  **Link:** [https://data.cityofchicago.org/](https://data.cityofchicago.org/)
  
  **Short Description:** One of the most extensive city data portals, with a wide array of datasets about Chicago.
  
  **Tips:**

  * Topics include crime, transportation (Divvy bike share, taxi trips), health, permits, etc.
  * Look for datasets with historical data for time-series analysis.
  * Many datasets are updated regularly.
  * **Project Fit:** Excellent for diverse urban projects, from analyzing crime patterns to public transit usage.

-----

### Other Interesting Sources

#### 17\. FiveThirtyEight

  
  **Link:** [https://data.fivethirtyeight.com/](https://data.fivethirtyeight.com/)
  
  **Short Description:** A site known for data-driven journalism (politics, sports, science, economics). They often share the data and code behind their articles.
  
  **Tips:**

  * Datasets are usually well-cleaned, in CSV format, and topical.
  * Great for projects that aim to replicate or extend existing analyses, or to explore current events.
  * Often comes with context and methodology explanations.
  * **Project Fit:** Excellent for projects where storytelling with data is key. Data can be suitable for various modeling tasks and EDA.

#### 18\. Our World in Data

  
  **Link:** [https://ourworldindata.org/](https://ourworldindata.org/)
  
  **Short Description:** An online publication that presents data and research on global living conditions and changes (e.g., health, poverty, environment, war).
  
  **Tips:**

  * Provides clean, well-documented datasets, often in CSV or Excel format (look for "Download" buttons on charts or at the bottom of articles).
  * Visualizations on the site can inspire your own EDA with Altair.
  * Focuses on long-term trends, making many datasets suitable for time-series analysis.
  * **Project Fit:** Excellent for projects addressing global issues and trends. Strong on data suitable for EDA and time-series.

#### 19\. Spotify Charts

  
  **Link:** [https://charts.spotify.com/](https://www.google.com/search?q=https://charts.spotify.com/)
  
  **Short Description:** Provides data on top and viral songs globally and by country.
  
  **Tips:**

  * Data is typically available as CSVs for current and past charts (e.g., daily, weekly) via a download button on the chart pages.
  * Can be used for analyzing music trends, popularity, and potentially for time-series forecasting.
  * You might need to combine this with other music metadata sources (e.g., Spotify API for audio features, if you're ambitious and it fits project scope).
  * **Project Fit:** Good for projects on cultural trends, time-series analysis.

#### 20\. IMDb Datasets (Internet Movie Database)

  
  **Link:** [https://developer.imdb.com/non-commercial-datasets/](https://developer.imdb.com/non-commercial-datasets/)
  
  **Short Description:** IMDb provides subsets of their data for non-commercial use.
  
  **Tips:**

  * Data includes movie titles, ratings, genres, cast/crew, etc., in TSV (tab-separated values) format.
  * Requires some wrangling to link different files (e.g., titles to ratings).
  * If you can find associated review text (e.g., from other public academic datasets of IMDb reviews, being mindful of terms of service), it could be used for text analysis.
  * **Project Fit:** Good for projects on the film industry. Structured data is good for general analysis; combining with review text (if sourced appropriately) can enable the text analysis extra credit.

-----

Remember to thoroughly explore any dataset you choose. Read its documentation, understand its variables, and consider its limitations before committing to it for your project. Good luck\!