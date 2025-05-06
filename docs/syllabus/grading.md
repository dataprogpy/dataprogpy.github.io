---
icon: material/book-check-outline
hide:
---


# **Course Grading**

Your performance in this course will be evaluated based on your active engagement with the learning materials and a comprehensive semester-long project, contributing to a total of **85 base points**. An optional extra credit component offers the opportunity to earn an additional **15 points**, making a maximum achievable score of **100 points**.

## Grade Components
The grading breakdown is as follows:

* **Active Learning Demonstration:** 15 Points
* **Semester Long Project:** 70 Points Total (distributed across components below)
    * Component 1: Project Plan - 10 Points
    * Component 2: Data Wrangling & EDA - 30 Points
    * Component 3: Modeling - 30 Points
* **Optional Extra Credit (Advanced Data Types):** 15 Points

---
## Active Learning

**1. Active Learning Demonstration (15 Points)**

* **Objective:** To demonstrate consistent engagement with the course's learning materials throughout the semester.
* **Requirement:** You are expected to actively work through the scaffolding materials (provided as Colab/Jupyter notebooks) for each session. Submit your completed practice notebooks, showing your work and exploration of the concepts covered.
* **Deliverable:** Submitted Colab/Jupyter notebooks containing your practice work for course sessions.

---

**Grading Rubric: Active Learning Demonstration (15 Points Total)**

**Overall Goal:** To reward consistent and active engagement with the course's hands-on learning materials throughout the semester.

**Weekly Grading Method:**
* For each session where practice work (Colab/Jupyter notebook based on provided scaffolding material) is assigned, your submission will be graded on a **Complete / Incomplete** basis.
* Grading focuses on **evidence of effort and engagement**, not perfect correctness.

**Final Score Calculation:**
* Your final score out of 15 points for this component will be determined by the proportion of weekly submissions marked as 'Complete'.
* **Formula:** `Score = (Number of 'Complete' Submissions / Total Number of Required Submissions) * 15`
    * *(Example: If there are 12 sessions with required submissions and a student receives 'Complete' on 10 of them, their score is (10 / 12) * 15 = 12.5 points)*

**Criteria for Weekly Submissions:**

**✅ Complete:**
A submission will be marked as 'Complete' if it meets the following criteria:

* **Timely Submission:** The notebook is submitted by the specified deadline (or within any permitted grace period).
* **Evidence of Engagement:** The notebook clearly shows that the student has actively worked through the provided material. This includes:
    * Most code cells have been executed (outputs are visible).
    * Exercises or sections requiring student input show a genuine attempt (code written, questions answered, or analysis performed, as applicable).
    * The work directly relates to the specific session's scaffolding notebook and objectives.
* **Good-Faith Effort:** The submission demonstrates a reasonable effort to engage with the concepts and tools presented in the notebook. It's understood that solutions may not be perfect, and minor errors or incomplete sections (if the student shows they attempted them) are acceptable for a 'Complete' mark.

**❌ Incomplete:**
A submission will be marked as 'Incomplete' if it meets one or more of the following criteria:

* **Not Submitted:** No notebook is submitted for the session.
* **Minimal Effort / Blank:** The submitted notebook is essentially unchanged from the provided template, or only a negligible amount of interaction is evident (e.g., only the first one or two cells executed, no attempts at exercises).
* **Lack of Engagement:** The submission clearly indicates that a good-faith effort was not made to work through the material or complete the assigned tasks.
* **Irrelevant Content:** The submitted notebook contains work that is unrelated to the specific session's assignment.


---
## Course Project

**2. Semester Long Project (70 Points Total)**

* **Objective:** To apply the data science concepts and tools learned in the course to a real-world problem. This project requires you to demonstrate proficiency in data wrangling, exploratory data analysis (EDA), and machine learning modeling.
* **Core Tools:** Python Polars (for data wrangling), Altair (for EDA and visualization), and scikit-learn (for modeling).
* **Deliverables:** The project is broken down into three graded components, submitted sequentially throughout the semester. All submissions must be via Colab Notebooks and include clear markdown annotations explaining your analytical goals, workflow steps, and interpretation of results.

### Project Plan

* **Component 1: Project Plan (10 Points)**
    * **Goal:** Define the scope, data, objectives, and plan for your project.
    * **Tasks:**
        1.  **Data Identification:** Select the dataset you will use. Provide verifiable links or clear sourcing information for instructor review.
        2.  **Data Dictionary:** Create a detailed data dictionary in markdown format, describing the variables, data types, and meanings within your chosen dataset.
        3.  **Analytical Objectives & Techniques:** Clearly state the business or research questions you aim to answer. Provide a high-level description of the primary analytical technique you plan to employ (e.g., prediction, classification, clustering, anomaly detection).
        4.  **Execution Plan:** Outline the major phases of your project and propose a timeline for completion.


**Grading Rubric: Project Component 1 - Project Plan (10 Points Total)**

**Overall Goal:** To assess the clarity, feasibility, and completeness of your initial project proposal, focusing on the chosen data and the planned analysis. 

*Note: While explicit points for overall presentation are removed, clear documentation and organization within the Colab notebook remain crucial for demonstrating your work effectively.*

**Submission Format:** Google Colab Notebook (`.ipynb`) with clear Markdown annotations.

**Criteria for Evaluation:**

| Criterion                                                      | **Excellent** | **Good** | **Fair** | **Poor / Missing** |
| :------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1. Data Identification, Accessibility & Dictionary** (5 pts) | Dataset clearly identified, highly relevant, readily accessible via verifiable link. Comprehensive, clear, well-formatted Markdown dictionary provided for key variables (name, type, description).                                                                                     | Dataset identified & generally relevant; minor issues with accessibility or link **OR** dictionary covers most key variables but has minor omissions/formatting issues.                                                                                                                               | Dataset relevance is questionable **OR** link broken/accessibility issues **OR** dictionary is significantly incomplete, inaccurate, or poorly formatted.                                                                                                                              | Dataset poorly identified, irrelevant, inaccessible, or missing **OR** dictionary is missing or completely inadequate.                                                                                                                                                         |
| **2. Analytical Objectives, Technique & Plan** (5 pts)         | Objectives are specific, relevant (SMART-like), strongly linked to data. Appropriate high-level technique clearly stated. Execution plan has clear, logical phases and realistic timeline/milestones showing forethought.                                                                | Objectives stated adequately but could be clearer/more specific. Link to data is present. Technique appropriate. Plan identifies major phases but lacks some detail or realism in timeline.                                                                                                             | Objectives are vague or weakly linked to data. Technique unclear or questionable. Plan/phases are unclear, illogical, or lack realism.                                                                                                                                                          | Objectives missing, nonsensical, or disconnected from data. Technique missing/inappropriate. Plan is missing or completely inadequate.                                                                                                                                     |

---


### Data Wrangling & EDA

* **Component 2: Data Wrangling and Exploratory Data Analysis (EDA) (30 Points)**
    * **Goal:** Ingest, clean, transform, and explore your data to prepare it for modeling and gain initial insights.
    * **Tasks:**
        1.  **Data Ingestion & Transformation (using Polars):** Load the raw data identified in Component 1. Perform necessary data wrangling tasks, demonstrating proficiency in handling data type conversions, managing missing values, creating new features, filtering, aggregating, and reshaping data.
        2.  **Exploratory Data Analysis (using Altair):** Conduct thorough EDA using visualizations created with Altair. Your exploration should be guided by your analytical objectives (from Component 1) and should uncover patterns, trends, relationships, and potential issues in the data. Demonstrate data storytelling by explaining what your visualizations reveal.


**Grading Rubric: Project Component 2 - Data Wrangling & EDA (30 Points Total)**

**Overall Goal:** To evaluate your ability to effectively ingest, clean, transform, explore, and derive meaningful insights from your chosen dataset using Polars and Altair. 

*Note: While explicit points for overall presentation are removed, clear documentation and organization within the Colab notebook remain crucial for demonstrating your work effectively across all criteria.*

**Submission Format:** Google Colab Notebook (`.ipynb`) with clear Markdown annotations detailing workflow, rationale, and interpretation.

**Criteria for Evaluation:**

| Criterion                                                     | **Excellent** | **Good** | **Fair** | **Poor / Missing** |
| :------------------------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1. Data Ingestion, Wrangling & Cleaning (Polars)** (10 pts) | Data correctly ingested with thorough initial checks. Demonstrates strong Polars proficiency. Comprehensively & effectively addresses data quality issues (types, missing, outliers) using logical transformations (joins, groupbys, feature eng.). Clean dataset ready for EDA/modeling. | Data correctly ingested with basic checks. Adequately performs necessary cleaning/transformations using Polars, handling common issues correctly. Polars usage is functional. Dataset is reasonably prepared.                                                                                 | Data ingestion may have minor errors or checks are minimal. Basic cleaning/transformations attempted, but significant issues may remain or handled suboptimally/inefficiently. Polars usage shows minor inaccuracies. Dataset may not be fully prepared.                                                   | Unable to load data correctly OR minimal/ineffective cleaning/transformation. Major data quality issues ignored or handled incorrectly. Little evidence of Polars proficiency. Dataset not ready for analysis.                                                               |
| **2. Exploratory Data Analysis (Altair)** (10 pts)            | Creates a wide variety of relevant, well-chosen, clear, and correctly labeled Altair visualizations (uni/bivariate, maybe multivariate) appropriate for data types & goals. Effective use of Altair features. Thorough exploration demonstrated.                                                | Creates relevant Altair charts covering key data aspects. Generally appropriate chart types & labeling. Covers essential exploration adequately.                                                                                                                                                  | Limited variety or relevance of visualizations. Chart types sometimes inappropriate, lack clarity/labels. Basic exploration only. Some reliance on non-Altair plots.                                                                                                                              | Very few, irrelevant, or poorly executed visualizations. Missing charts for key aspects. Little evidence of Altair usage. Exploration is insufficient.                                                                                                                     |
| **3. Insight Generation & Data Storytelling** (10 pts)        | Interprets EDA findings deeply and critically. Clearly and explicitly links insights back to project objectives, strongly supporting interpretations with specific chart references. Tells a compelling and coherent story with the data, effectively motivating the modeling phase.         | Provides valid interpretation for most visualizations. Makes clear connections to project objectives. A logical data narrative is present.                                                                                                                                                      | Interpretation is minimal, superficial, or occasionally incorrect. Weak or unclear link between EDA & objectives. Narrative is fragmented or difficult to follow.                                                                                                                            | No meaningful interpretation provided. No connection to objectives. Just code/charts without explanation.                                                                                                                                                                 |

---

### Modeling

* **Component 3: Modeling (30 Points)**
    * **Goal:** Apply appropriate machine learning models to address your analytical objectives.
    * **Tasks:**
        1.  **Model Selection & Implementation (using scikit-learn):** Based on your objectives and EDA findings, select and implement relevant supervised (e.g., regression, classification) and/or unsupervised (e.g., clustering) learning algorithms using scikit-learn.
        2.  **Model Evaluation & Interpretation:** Evaluate the performance of your models using appropriate metrics. Interpret the results in the context of your original objectives. Discuss the limitations and implications of your findings.


**Grading Rubric: Project Component 3 - Modeling (30 Points Total)**

**Overall Goal:** To evaluate your ability to select, implement, evaluate, and interpret appropriate machine learning models using scikit-learn to address your project objectives defined in Component 1 and informed by Component 2. 

*Note: Clear documentation and organization within the Colab notebook remain crucial for demonstrating your work effectively.*

**Submission Format:** Google Colab Notebook (`.ipynb`) with clear Markdown annotations explaining the rationale, process, and findings.

**Criteria for Evaluation:**

| Criterion                                            | **Excellent** | **Good** | **Fair** | **Poor / Missing** |
| :--------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1. Model Selection & Implementation (sklearn)** (10 pts) | Choice of algorithm(s) is highly relevant to objectives & data type. Strong justification provided. Correct & effective use of scikit-learn (e.g., pipelines, `fit`/`predict`/`transform`, train/test split). Appropriate feature handling (scaling, encoding) implemented logically. | Algorithm choice is relevant to objectives/data. Implementation uses scikit-learn correctly for basic steps. Necessary feature handling and train/test split performed adequately.                                                                                             | Algorithm choice may be suboptimal or justification weak. Minor errors in scikit-learn usage or pipeline setup. Feature handling or data splitting might be incomplete or slightly incorrect.                                                                                          | Algorithm choice is inappropriate or missing. Major errors in scikit-learn implementation or logic. Fundamental steps like train/test split or essential feature handling missing or incorrect.                                                                               |
| **2. Model Evaluation** (10 pts)                    | Selects highly appropriate evaluation metric(s) with clear justification relevant to project objectives. Correctly calculates/reports metrics on the test set. Compares models effectively (if applicable). Demonstrates clear understanding & checks for overfitting/underfitting.     | Selects appropriate metric(s) for the task. Correctly calculates/reports metrics on test set. Basic comparison performed if needed. Addresses overfitting/underfitting at a basic level.                                                                                         | Metric choice may be less appropriate or lack clear justification. Minor errors in calculation or reporting. Evaluation might be incomplete (e.g., only on training data, weak comparison). Overfitting/underfitting poorly addressed or identified.                                | Inappropriate or missing metrics. Incorrect calculation/reporting. No comparison or testing on appropriate data split. No consideration or check for overfitting/underfitting.                                                                                                |
| **3. Interpretation, Conclusion & Limitations** (10 pts) | Provides deep, insightful interpretation of model results, explicitly linking them back to project objectives. Draws clear, well-supported conclusions answering the original question(s). Thoroughly discusses model limitations, assumptions, potential biases, and implications.     | Provides correct interpretation of results and relates them back to objectives. Draws reasonable conclusions based on the evaluation. Mentions some relevant limitations of the model or analysis.                                                                               | Interpretation is superficial, partially incorrect, or weakly linked to objectives. Conclusions are unclear or weakly supported by the evaluation results. Discussion of limitations is minimal, generic, or missing key aspects.                                                     | Interpretation is missing, incorrect, or irrelevant. No connection made to objectives. Conclusions are missing or unsupported by evidence. No discussion of limitations or implications.                                                                                          |


---
### Extra Credit

**3. Optional Extra Credit Component: Advanced Data Types (15 Points)**

* **Objective:** To demonstrate proficiency in analyzing specialized data types beyond the core project requirements.
* **Requirement:** Students choosing this option must select *either* time-series analysis *or* text analysis. This requires selecting a dataset suitable for the chosen advanced technique during the Project Plan phase (Component 1).
* **Tasks:** Apply relevant libraries and techniques (e.g., libraries like `statsmodels`, `prophet` for time-series; `nltk`, `spaCy`, or `scikit-learn`'s text features for text analysis) to your chosen dataset. Perform analysis appropriate to the data type and your project goals.
* **Deliverable:** Integrated analysis within your final project submission (Colab Notebook), clearly marked as the extra credit component, with relevant markdown explanations.


---

**Grading Rubric: Optional Extra Credit - Advanced Data Types (15 Points Total)**

**Overall Goal:** To evaluate your ability to apply techniques appropriate for either time-series or text data, interpret the results meaningfully, and integrate them with your main project analysis. Success requires choosing an appropriate dataset early in the project.

**Submission Format:** Clearly marked section within the final project Colab Notebook submission, including Markdown annotations explaining the process, rationale, and findings specific to this extra credit work.

**Criteria for Evaluation:**

| Criterion                                       | **Excellent** | **Good** | **Fair** | **Poor / Missing** |
| :---------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1. Data Prep & Technique Selection** (5 pts) | Clear choice of relevant advanced track (time-series/text). Data appropriately preprocessed using techniques specific to the chosen type (e.g., time indexing/resampling; tokenization/vectorization). Appropriate libraries used effectively. Setup is clear within the notebook.                                                        | Choice of track is clear & appropriate. Basic necessary preprocessing performed correctly using relevant libraries. Setup is adequate.                                                                                                                                                    | Choice of track appropriate, but preprocessing is incomplete, contains minor errors, or uses less suitable techniques/libraries. Setup might be unclear.                                                                                                                               | Track choice unclear or inappropriate for data **OR** preprocessing is missing or contains major errors **OR** relevant libraries not used correctly. Setup inadequate or missing.                                                      |
| **2. Analysis & Implementation** (5 pts)        | Correctly applies relevant & reasonably sophisticated analysis techniques (e.g., forecasting model; topic model, sentiment analysis). Implementation uses library functions accurately and effectively. Analysis directly addresses relevant goal (either main project goal or specific extra credit goal).                            | Correctly applies relevant basic analysis techniques. Implementation uses library functions correctly for the chosen method. Analysis is relevant.                                                                                                                                       | Attempts relevant techniques but contains minor errors in application or implementation. Analysis may be only tangentially relevant or incomplete.                                                                                                                                            | Inappropriate techniques applied **OR** major errors in implementation **OR** analysis is missing or irrelevant.                                                                                                                        |
| **3. Interpretation & Integration** (5 pts)     | Provides insightful interpretation of advanced analysis results. Clearly explains findings and their significance. Effectively integrates/connects these findings with the main project's objectives or conclusions. Communication (markdown) within the extra credit section is clear and comprehensive.                                | Provides correct interpretation of results. Explains findings adequately. Makes a reasonable attempt to connect findings to the main project. Communication is clear.                                                                                                                    | Interpretation is superficial or partially incorrect. Explanation is unclear. Connection to main project is weak or missing. Communication lacks detail.                                                                                                                                   | Interpretation missing or incorrect. No explanation of findings. No connection made to the main project. Poor or missing communication within the section.                                                              |

## Grading Scheme

***Pitt's Default Canvas Grading Scheme:***

| Letter Grade	| Range |
|:---:|:---:|
| **A**	 | 100% to 94%  |
| **A-** | < 94% to 90% |
| **B+** | < 90% to 87% |
| **B**	 | < 87% to 84% |
| **B-** | < 84% to 80% |
| **C+** | < 80% to 77% |
| **C**	 | < 77% to 74% |
| **C-** | < 74% to 70% |
| **D+** | < 70% to 67% |
| **D**	 | < 67% to 64% |
| **D-** | < 64% to 61% |
| **F**	 | < 61% to 0%  | 