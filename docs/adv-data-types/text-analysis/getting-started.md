---
icon: material/numeric-1
---

# Getting Started with Textual Data

In the previous lessons, you learned about extracting insights from structured data—the clean, predictable world of rows and columns found in spreadsheets and databases. But what about the other 80% of the world's data? The messy, context-rich, and deeply human data found in emails, reviews, reports, and social media posts. This section serves as your entry point into this world, providing the foundational concepts needed to turn raw text into a strategic business asset.

### 1.1 Why Text is a Unique Data Source

Imagine you are a manager at a growing e-commerce company, "GlobalCart." You have a dashboard showing sales figures, inventory levels, and return rates—all clear, quantifiable metrics. This is **structured data**. Each piece of information has a defined place and format, making it easy for computer systems to process. You can easily query it, aggregate it, and create visualizations.

However, this data tells you *what* is happening, but rarely *why*. Why did return rates for the "Pro-Grade Blender" spike last month? Why are sales of the "Comfort-Fit Running Shoes" declining despite positive initial forecasts? The answers are likely buried in the text customers have generated.

Consider this small sample of recent customer feedback for GlobalCart:

| Customer ID | Feedback Text | Rating |
| :---------- | :---------------------------------------------------------------------------------------------------------------- | :----- |
| CUST-001    | The Pro-Grade Blender is a beast! It's powerful and quiet. Delivery was also incredibly fast, arrived in one day. | 5      |
| CUST-002    | My package with the Comfort-Fit Running Shoes arrived damaged, and the box was completely crushed. Disappointed. | 2      |
| CUST-003    | I had to return the blender. It was much larger than expected and didn't fit on my counter. The return was easy. | 3      |
| CUST-004    | Are the Comfort-Fit shoes waterproof? Need to know before I buy. Your support chat is offline.                  | 3      |
| CUST-005    | Fast shipping is great, but the Pro-Grade Blender's lid doesn't seem to seal properly. Seems like a defect.      | 2      |

This is **unstructured data**. It lacks a predefined model or organization. It's free-form text, rich with nuances, sentiment, and specific details. Programmatically analyzing this type of data is the core challenge and opportunity of text analysis. 

To begin working with this data, we need a new vocabulary that maps to the structures we already know from working with DataFrames.

!!! info "The Hierarchy of Text: Corpus, Document, and Token"
    
     In text analysis, we use a simple hierarchy to organize our data. This hierarchy has a direct and helpful analogy to the DataFrames you are used to.
    
     * **Corpus:** The entire collection of texts we are analyzing. In our example, the five feedback entries together form our **corpus**. This is analogous to an entire `DataFrame`.
     * **Document:** A single piece of text within the corpus. Each customer feedback entry is one **document**. This is analogous to a single `row` in a DataFrame, representing one observation.
     * **Token:** An individual unit within a document, such as a word or a punctuation mark. For example, the words "package," "arrived," and "damaged" are all **tokens** from the second document.
    
     The most important difference from structured data is this: In a DataFrame, the columns (features) are already defined for you. In text analysis, we must create our features *from the text itself*. The **tokens** within each document are the raw materials we will use to build these features.


### 1.2 The Text Analysis Workflow vs. Traditional ML

You are already familiar with the standard machine learning workflow from your work with libraries like `scikit-learn`. The process is logical and methodical. However, when working with text, the workflow requires a new, specialized set of initial steps.

The primary difference is that machine learning models operate on numbers, not words. Therefore, the most critical task in text analysis is to devise a meaningful strategy to convert text into a numerical representation. This involves a much more intensive pre-processing and feature engineering phase than you might be used to with structured data.

!!! info "**A Tale of Two Workflows**"

    Here is a high-level comparison of the traditional machine learning workflow you know and the specialized workflow required for textual data.

    | Traditional ML Workflow (Structured Data)                                  | Text Analysis Workflow (Unstructured Data)                                                                                                      |
    | :------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------- |
    1. **Load Data:** Read a CSV or database table.                            | 1. **Load Data:** Read text files, emails, reviews, etc. into a **corpus**.                                                                     |
    | 2. **Pre-process Data:** Handle missing values, scale numerical features.      | 2. [**Pre-process Text:**](text-data-preparation.md) A multi-step, intensive process including **tokenization**, lemmatization, stop-word removal, and more.  |
    | 3. **Feature Engineering:** Select the most relevant columns from your table. | 3. [**Vectorize Text:**](vectorization.md) Convert the clean text into numerical features using techniques like **Bag-of-Words** or **TF-IDF**. *(Covered in Section 3)* |
    | 4. **Model Training:** Fit a model (e.g., `LogisticRegression`) to the data.  | 4. **Model Training:** Fit a model to the newly created numerical representation of the text.                                                     |
    | 5. **Evaluation:** Assess model performance.                               | 5. **Evaluation & Interpretation:** Assess performance and interpret results in the context of the original text.                             |  
The key takeaway is the addition of two major phases: intensive **pre-processing** to clean and standardize the text, and **vectorization** to convert that text into features for your model. The next two sections are dedicated entirely to these foundational steps.

### 1.3 A Map of Text Analysis Techniques

Before we dive into the "how," let's create a high-level map of the "what." What kind of business problems can we solve with text analysis? These techniques are the ultimate destination, and the pre-processing and vectorization steps are the necessary journey to get there.

#### [Text Classification and Sentiment Analysis](sentiment-analysis.md)

The most direct application of your existing supervised learning knowledge is **Text Classification**. The goal is to assign a predefined category or label to a piece of text. A common and commercially valuable form of text classification is **Sentiment Analysis**, where the labels are related to opinion, such as "positive," "negative," or "neutral."

* **Business Question:** "Is this customer review positive or negative?" or "Should this support email be routed to the 'Urgent' or 'General' queue?"
* **ML Analogy:** This is a classic **supervised classification** problem. You will use a labeled dataset to train a model to make predictions on new, unseen text. 

#### [Topic Modeling](topic-modeling.md)

Sometimes, you don't know the categories in advance. You might have thousands of customer reviews and want to discover the main themes or topics that people are discussing. This is where **Topic Modeling** comes in. It's an automated way to scan a set of documents and infer the underlying topics.

* **Business Question:** "What are the most common themes in our customer feedback? Are people talking more about 'shipping delays,' 'product quality,' or 'price'?"
* **ML Analogy:** This is an **unsupervised learning** problem, conceptually similar to the clustering you performed with K-Means. The algorithm groups words and documents together to create abstract topics. 

#### Named Entity Recognition (NER)

Your text data is often full of proper nouns—people, products, companies, locations, and dates. **Named Entity Recognition (NER)** is the technique used to locate and classify these entities within text.

* **Business Question:** "Which of our products are mentioned most often on social media?" or "Are customers in specific geographic locations complaining about shipping?"
* **ML Analogy:** This is a more advanced technique, but it can be thought of as a form of granular classification at the word level.

!!! tip "**Linking Problems to Techniques**"

    | If you want to...                                | The technique is...        | The ML analogy is...           |
    | :----------------------------------------------- | :------------------------- | :----------------------------- |
    | Assign text to known categories (e.g., spam/not spam) | **Text Classification** | Supervised Classification      |
    | Gauge the opinion within text (e.g., positive/negative)   | **Sentiment Analysis** | A type of Classification       |
    | Discover hidden themes in documents          | **Topic Modeling** | Unsupervised Clustering        |
    | Extract proper nouns like products or places     | **Named Entity Recognition** | (More advanced classification) |

With this foundational map in place, you are ready to begin the journey. The next section will equip you with the essential tools for cleaning and preparing text for analysis.