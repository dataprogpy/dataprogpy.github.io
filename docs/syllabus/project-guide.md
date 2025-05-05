---
icon: octicons/project-24
---

# Project Guide


## Introduction 

Welcome to the semester project for our MBA Data Science course. This project provides a significant opportunity for you to synthesize and apply the core concepts learned throughout the semester. You'll engage in data wrangling using Polars, conduct exploratory data analysis (EDA) with Altair, and build predictive or descriptive models using scikit-learn, all centered around a topic of your choosing.

The objective extends beyond simply writing functional code. We aim for you to demonstrate a methodical and thoughtful analytical process. This involves clearly defining a question or objective, sourcing and meticulously preparing relevant data, exploring that data to uncover insights, constructing and evaluating appropriate models, and, critically, communicating your entire process, findings, limitations, and interpretations clearly. Consider this a simulation of a real-world data science task where clear communication and a documented process are paramount.

## AI for Coursework

Generative AI (GenAI) tools (like ChatGPT, Google Gemini, Microsoft Copilot, Claude) can be valuable learning aids, but their misuse undermines learning and constitutes academic dishonesty.

**Guiding Principle:** Employ GenAI as a **tutor, brainstorming partner, or debugging assistant**. It should augment your learning, not replace your critical thinking, analytical work, or original writing. The project you submit must fundamentally represent your own effort and understanding.

### Acceptable Uses

* **Brainstorming:** Generating initial ideas for project topics or analytical questions. (e.g., "Suggest data science project ideas relevant to healthcare administration.")
* **Conceptual Clarification:** Asking for explanations of terms, algorithms, or methodologies. (e.g., "Explain the concept of 'overfitting' in machine learning.")
* **Code Explanation & Debugging:** Getting explanations for specific functions or error messages using *small snippets* of your code. (e.g., "What does this Polars error mean: `[error message]`?")
* **Syntax Examples:** Requesting basic syntax for specific library functions (which you must then adapt, implement, and understand). (e.g., "Show a basic example of reading a parquet file in Polars.")
* **Exploring Alternatives:** Asking about different methods or approaches to consider for a task. (e.g., "What are common alternatives to k-means clustering?")
* **Writing Refinement:** Improving the grammar and clarity of your *own* written explanations in markdown cells.

### Unacceptable Uses

* **Generating Core Analysis Code:** Asking the AI to write substantial portions of your data cleaning, EDA, or modeling code.
* **Generating Interpretations/Conclusions:** Requesting the AI to interpret your charts, model results, or write concluding paragraphs.
* **Direct Plagiarism:** Submitting AI-generated code or text as your own without significant modification, understanding, and integration into your work.
* **Bypassing Understanding:** Relying on AI for answers without trying to grasp the underlying concepts yourself.

### Your Responsibility

* **Verify AI Output:** GenAI can be inaccurate. Always cross-reference its suggestions with course materials, documentation, or further investigation. Code provided by AI should be carefully tested and understood.
* **Understand Your Work:** Be prepared to explain the logic behind any code or analysis step you submit.
* **Own Your Insights:** All interpretations, conclusions, and discussions of limitations must stem from your own critical thinking.
* **Ask if Unsure:** Please consult the instructor if you have questions about appropriate AI use.

**Self-Check:** Could you confidently explain your project's methodology and findings without the AI tool? If so, you are likely using it appropriately.

---

## Component 1: Project Plan

*Goal: Define the project's scope, data, objectives, and initial roadmap.*

**Step 1: Identify a Topic and Research Question/Objective.** Start by choosing a domain that interests you, perhaps related to your career aspirations or other MBA coursework. Formulate an initial question you'd like to explore with data. Examples include: "Can we predict employee attrition based on HR data?" or "What customer characteristics are associated with higher lifetime value?"

***GenAI Tip:*** Use AI for brainstorming topics or refining your initial question for better focus.

**Step 2: Find Your Data.** Search for relevant public datasets (Kaggle, data.gov, UCI ML Repository, etc.). Assess if the data is suitable (has relevant variables), accessible (downloadable), and credible. It's crucial to provide a verifiable link and note the source clearly in your plan.

***GenAI Tip:*** Ask AI about *types* of data used for certain analyses or for search keywords. *Warning:* Always verify dataset existence and links yourself, as AI suggestions can be unreliable here.

**Step 3: Define Analytical Objectives & High-Level Technique.** Refine your research question into 1-3 specific analytical objectives. Based on these objectives, identify the primary *type* of analysis you anticipate needing, such as prediction, classification, clustering, or descriptive analysis/EDA.

***GenAI Tip:*** Ask AI to help phrase objectives clearly or explain the differences between high-level techniques. Verify suggestions against course content.

**Step 4: Create Data Dictionary.** After briefly inspecting your data or its documentation, create a Markdown table. List the key variables you intend to use, their data types (e.g., integer, float, string, boolean, date), and a concise description of what each represents.

***GenAI Tip:*** Request Markdown table templates or explanations of data types. Creating the descriptions accurately usually requires manual review of the data/documentation.

**Step 5: Outline Execution Plan.** List the major phases for your project, roughly aligning with Components 2 and 3, and potentially including sub-steps like 'Data Cleaning' or 'Feature Engineering'. Propose a realistic timeline or milestones for completing these phases.

***GenAI Tip:*** Ask for typical phases in a data science project to ensure your plan is comprehensive.

**Deliverable:** A Colab notebook containing these five elements, clearly documented using Markdown.

---

## Component 2: Data Wrangling & EDA

*Goal: Prepare your data for analysis, explore it visually, and derive initial insights.*

**Step 1: Ingest & Initial Inspection.** Load your dataset into Colab using Polars commands appropriate for your file type (e.g., `pl.read_csv`). Perform initial checks like examining the `shape`, `head()`, `dtypes`, and `describe()` outputs. Document your first impressions.

***GenAI Tip:*** Get syntax examples for loading data or performing checks. Debug loading errors. Ask about interpreting initial summary statistics.

**Step 2: Data Cleaning & Transformation (Polars).** This is often the most time-consuming phase. Address missing values using an appropriate strategy (e.g., imputation, deletion) and justify your choice. Correct data types using Polars expressions (e.g., `.cast()`). Filter rows or select columns as needed (`.filter()`, `.select()`). If necessary, create new features (`.with_columns()`) or reshape data (`.pivot()`). Remember to clearly document the rationale for each significant transformation in Markdown.

***GenAI Tip:*** Explore strategies for handling missing data via AI prompts. Get Polars syntax examples for specific tasks (e.g., type conversion, conditional feature creation). Debug Polars code snippets. *Always verify the logic.*

**Step 3: Exploratory Data Analysis (Altair).** Use Altair to create visualizations that help you understand your data and address your objectives. Explore individual variables (univariate analysis, e.g., histograms) and relationships between variables (bivariate analysis, e.g., scatter plots, box plots). Ensure your charts are clear and well-labeled.

***GenAI Tip:*** Ask about appropriate chart types for different kinds of data or relationships. Get basic Altair syntax examples. Debug plotting code.

**Step 4: Document Insights & Storytelling.** For each key visualization, add a Markdown cell explaining what it reveals. What patterns or insights emerge? How do they relate to your project objectives? Structure your EDA section to tell a coherent story about your data exploration journey.

***GenAI Tip:*** Use AI to help structure your interpretation notes or refine the clarity of your writing. *The insights must be your own derivation from the analysis.*

**Deliverable:** An updated Colab notebook showing data ingestion, the cleaning/transformation process (with justifications), and EDA using Altair charts accompanied by clear interpretations.

---

## Component 3: Modeling

*Goal: Apply machine learning models using scikit-learn to address your objectives, evaluate their performance, and interpret the results.*

**Step 1: Feature Preparation for Modeling.** Select the features you'll use based on your EDA and objectives. Prepare them for modeling by encoding categorical features (e.g., One-Hot Encoding) and scaling numerical features if necessary (e.g., StandardScaler). Split your data into training and testing sets (`train_test_split`). Using a scikit-learn `Pipeline` to combine preprocessing and model estimation is highly recommended for efficiency and rigor.

***GenAI Tip:*** Ask about encoding/scaling strategies or the rationale for train/test splits. Get example `Pipeline` structures. Debug preprocessing code.

**Step 2: Model Selection and Training.** Choose appropriate scikit-learn models based on your task (regression, classification, clustering) and justify your selection. Instantiate your chosen model(s) or pipeline(s). Train the model(s) using the `.fit()` method exclusively on your **training data**.

***GenAI Tip:*** Ask for explanations of algorithms suitable for your task. Get basic syntax examples for fitting models. *Rely on course knowledge and your analysis, not just AI suggestions, for model selection.*

**Step 3: Model Evaluation.** Use the trained model to make predictions on the **testing data**. Select appropriate evaluation metrics for your task (e.g., accuracy, precision, recall, F1, confusion matrix for classification; R2, MAE, RMSE for regression; silhouette score for clustering) and justify your choices. Calculate these metrics using `sklearn.metrics`. Analyze the results, checking for signs of overfitting (much better performance on training vs. test data) and comparing models if multiple were trained.

***GenAI Tip:*** Ask about appropriate metrics for your goal. Get syntax examples for metric calculation. Ask how to interpret evaluation outputs like confusion matrices.

**Step 4: Interpretation, Conclusion & Limitations.** Interpret what your model's performance means in the context of your project goals. Draw clear conclusions based on the evidence. Did you answer your original question? Critically discuss the limitations of your analysis – consider data limitations, model assumptions, potential biases, and practical implications. Suggest potential next steps or areas for improvement.

***GenAI Tip:*** Ask for help structuring your conclusion or brainstorming potential limitations common to your chosen model type. Use AI to refine the clarity of your writing. *The core interpretation and critical assessment must be your own.*

**Deliverable:** The final Colab notebook incorporating feature preparation, model training/evaluation (sklearn), and detailed interpretation, conclusions, and discussion of limitations, all clearly documented.

---

## Optional Extra Credit Component

*Goal: Explore time-series or text analysis techniques.*

If you chose a suitable dataset, you can pursue this option. This involves specific **preprocessing** relevant to time-series (e.g., time indexing, stationarity checks) or text (e.g., tokenization, vectorization). You'll then **apply appropriate analysis techniques** using relevant libraries (e.g., `statsmodels`, `prophet` for time-series; `nltk`, `spaCy`, `sklearn.feature_extraction` for text). Finally, **interpret and integrate** these findings with your main project. Clearly mark this section in your notebook and document your process.

***GenAI Tip:*** Use AI similarly to other steps – for conceptual explanations, basic syntax examples specific to these advanced libraries, debugging, and writing refinement.

**Deliverable:** A clearly marked section within your final submitted Colab notebook.

---

**7. General Tips for Success**

* **Start Early:** Allocate sufficient time for each project phase.
* **Iterate:** Data analysis is often cyclical. Be prepared to revisit earlier steps.
* **Document Thoroughly:** Use Markdown cells extensively to explain your choices and interpretations. Your notebook should be a self-contained report.
* **Justify Choices:** The reasoning behind your decisions is a key part of the evaluation.
* **Seek Help When Stuck:** Leverage office hours, TA sessions, and course forums.
* **Scope Realistically:** A well-executed analysis on a focused problem is better than a superficial one on an overly broad topic.

**8. Submission**

Ensure your Colab notebook (`.ipynb` file) is well-organized, all code cells have been run with visible outputs, and submitted by the specified deadlines for each component. The final submission should clearly delineate all project components.
