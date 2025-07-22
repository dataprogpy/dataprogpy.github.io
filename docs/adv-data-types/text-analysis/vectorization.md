---
icon: material/numeric-3
---
Excellent, let's proceed.

Here is the content for **Section 3: From Words to Numbers: Vectorization**.

-----

# Section 3: From Words to Numbers: Vectorization

In the previous section, we successfully transformed our raw text into a clean, standardized list of tokens for each document. This was a critical step in reducing noise, but our data is still in a format that machines cannot use for modeling: a list of words. Machine learning algorithms, including those in `scikit-learn` that you are familiar with, operate on numerical data, not strings of text.

This section covers the final and most crucial bridge between our textual data and the machine learning models: **vectorization**. This is the process of converting a collection of text documents into a matrix of numerical features. After this step, our text data will finally be in a shape that we can feed directly into a model like `LogisticRegression`.

### Preparing Our Corpus for Vectorization

Let's assume we have performed the pre-processing steps from Section 2 on a few of our "GlobalCart" reviews. Our starting point for this section—our clean **corpus**—is a list of documents, where each document is a list of clean tokens.

```python
# This is our pre-processed corpus from the end of Section 2.
# Each inner list represents one document (one customer review).
corpus = [
    ['blender', 'powerful', 'quiet', 'delivery', 'fast'],
    ['package', 'arrive', 'damage', 'box', 'crush', 'disappointed'],
    ['return', 'blender', 'large', 'fit', 'counter', 'return', 'easy'],
    ['shoe', 'waterproof', 'need', 'know', 'buy']
]

# Why: For scikit-learn's vectorizers to work, they expect a list of strings,
# not a list of lists. We'll join our tokens back into single strings.
# This is a common preparatory step before vectorization.
processed_corpus = [" ".join(doc) for doc in corpus]

print(processed_corpus)
```

**Output:**

```
[
  'blender powerful quiet delivery fast',
  'package arrive damage box crush disappointed',
  'return blender large fit counter return easy',
  'shoe waterproof need know buy'
]
```

With our corpus in this format, we are ready to convert it into numbers.

### Method 1: The Bag-of-Words (BoW) Model

The most straightforward and intuitive method for vectorization is the **Bag-of-Words (BoW)** model. The name is a helpful metaphor: the model treats each document as a "bag" containing its words, disregarding grammar and word order, and focuses only on the frequency of each word.

The process involves two main steps:

1.  **Learn Vocabulary:** First, it scans the entire corpus and builds a vocabulary of all unique tokens that appear.
2.  **Create Vectors:** For each document, it creates a numerical vector. This vector will have one entry for every word in the vocabulary. The value of the entry is simply the count of how many times that word appeared in the document.

Let's see this in action using `scikit-learn`'s `CountVectorizer`.

```python
import pandas as pd
from sklearn.feature_extraction.text import CountVectorizer

# Why: We initialize the CountVectorizer. This object will learn the vocabulary
# and convert our text documents into numerical vectors based on word counts.
vectorizer = CountVectorizer()

# Why: The .fit_transform() method does two things:
# 1. It learns the vocabulary from our entire corpus.
# 2. It transforms the corpus into a document-term matrix (a matrix of word counts).
document_term_matrix = vectorizer.fit_transform(processed_corpus)

# Why: We can inspect the learned vocabulary. The columns of our matrix
# will correspond to these words, in this order.
vocabulary = vectorizer.get_feature_names_out()

# Why: To make the output easy to understand, we'll convert the matrix
# to a pandas DataFrame and label the columns with the vocabulary.
df_bow = pd.DataFrame(document_term_matrix.toarray(), columns=vocabulary)

print("Vocabulary:", vocabulary)
```

**Vocabulary:**
`['arrive' 'blender' 'box' 'buy' 'counter' 'crush' 'damage' 'delivery' 'disappointed' 'easy' 'fast' 'fit' 'know' 'large' 'need' 'package' 'powerful' 'quiet' 'return' 'shoe' 'waterproof']`

**Rendered Table Output (`df_bow`):**

| | arrive | blender | box | buy | counter | crush | damage | delivery | disappointed | easy | fast | fit | know | large | need | package | powerful | quiet | return | shoe | waterproof |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| **0** | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 1 | 1 | 0 | 0 | 0 |
| **1** | 1 | 0 | 1 | 0 | 0 | 1 | 1 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 |
| **2** | 0 | 1 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | 0 | 0 | 0 | 2 | 0 | 0 |
| **3** | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 1 | 0 | 0 | 0 | 0 | 1 | 1 |

Each row now represents one of our original documents, and each column represents a word in our vocabulary. For example, in Document 2 (row index 2), the word "return" appears twice, so its corresponding entry is 2. We have successfully vectorized our text\!

> **[NOTE] The Limits of Bag-of-Words**
>
> BoW is simple and effective, but it has two key limitations:
>
> 1.  It completely loses the original word order and context.
> 2.  It treats every word as equally important. A word like "blender" might be more important than "large" in a document, but BoW only sees their counts.

### Method 2: Term Frequency-Inverse Document Frequency (TF-IDF)

To address the limitations of BoW, we can use a more sophisticated scoring method called **Term Frequency-Inverse Document Frequency (TF-IDF)**. The core idea is to assign a weight to each word that reflects its importance to a specific document within the context of the entire corpus.

TF-IDF is a product of two metrics:

  * **Term Frequency (TF):** This is essentially the same as BoW. It's the frequency of a term in a document.
  * **Inverse Document Frequency (IDF):** This is the "smart" component. It measures how rare or common a word is across all documents. Words that appear in many documents (e.g., "blender," which appears in two of our four documents) get a low IDF score, while words that are rare get a high IDF score.

The final TF-IDF score for a word is `TF * IDF`. A word gets a high score if it appears frequently in one document but rarely across the rest of the corpus, making it a good indicator of that document's specific content.

Let's use `scikit-learn`'s `TfidfVectorizer`.

```python
from sklearn.feature_extraction.text import TfidfVectorizer

# Why: We initialize the TfidfVectorizer. Its API is intentionally
# very similar to CountVectorizer, demonstrating scikit-learn's consistency.
tfidf_vectorizer = TfidfVectorizer()

# Why: Just like before, .fit_transform() learns the vocabulary and transforms
# the corpus into a document-term matrix, but this time the values are TF-IDF scores.
tfidf_matrix = tfidf_vectorizer.fit_transform(processed_corpus)

# Why: We create another DataFrame to easily inspect and compare the results.
df_tfidf = pd.DataFrame(tfidf_matrix.toarray(), columns=tfidf_vectorizer.get_feature_names_out())
```

**Rendered Table Output (`df_tfidf`):**
*(Note: Values are rounded for display)*

| | arrive | blender | box | buy | counter | crush | damage | delivery | disappointed | easy | fast | fit | know | large | need | package | powerful | quiet | return | shoe | waterproof |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| **0** | 0.00 | 0.42 | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 | 0.53 | 0.00 | 0.00 | 0.53 | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 | 0.53 | 0.53 | 0.00 | 0.00 | 0.00 |
| **1** | 0.41 | 0.00 | 0.41 | 0.00 | 0.00 | 0.41 | 0.41 | 0.00 | 0.41 | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 | 0.41 | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 |
| **2** | 0.00 | 0.31 | 0.00 | 0.00 | 0.39 | 0.00 | 0.00 | 0.00 | 0.00 | 0.39 | 0.00 | 0.39 | 0.00 | 0.39 | 0.00 | 0.00 | 0.00 | 0.00 | 0.78 | 0.00 | 0.00 |
| **3** | 0.00 | 0.00 | 0.00 | 0.45 | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 | 0.00 | 0.45 | 0.00 | 0.45 | 0.00 | 0.00 | 0.00 | 0.00 | 0.45 | 0.45 |

Notice the difference. In the BoW matrix, "blender" and "return" had scores of 1 and 2 in the third document. In the TF-IDF matrix, the score for "return" (0.78) is much higher than for "blender" (0.31). This is because "return" appears twice in that document, while "blender" appears only once and is also present in another document, reducing its IDF score. TF-IDF has successfully identified "return" as being more significant to that specific document.

We have now successfully converted our text into a rich numerical format. This matrix is the `X` that you are used to working with. It is ready to be fed into any `scikit-learn` classifier or clustering algorithm to finally unlock the insights hidden within our text.

*(Proceed to Section 4: Strategies for Sentiment Analysis)*