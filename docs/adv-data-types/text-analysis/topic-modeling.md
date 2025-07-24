---
icon: material/numeric-5
---
# Uncovering Hidden Themes with Topic Modeling

In the last section, we focused on supervised learning: we had predefined labels ("positive," "negative") and trained a model to classify our documents. But what if you don't know the categories in advance? What if you have thousands of customer reviews and simply want to discover, "What are people talking about?"

This is where **Topic Modeling** comes in. It is an **unsupervised learning** technique used to scan a corpus of documents, detect word and phrase patterns within them, and automatically discover abstract "topics" that are present in the collection.

## The "What" and "Why" of Topic Modeling

The goal of topic modeling is discovery. It helps answer broad questions like:

  * What are the main themes in our customer support tickets?
  * What features do customers mention most often in product reviews?
  * Are there emerging issues or trends in our feedback data?

Think of it as a powerful form of clustering, an unsupervised task you are already familiar with. With K-Means, you grouped data points based on their proximity in a feature space. With topic modeling, you group documents based on the words they contain. Documents that use similar words will be grouped under the same topic.

## Implementing Topic Modeling

There are several algorithms for topic modeling, but we will use **Non-Negative Matrix Factorization (NMF)**, a popular and highly interpretable method available in `scikit-learn`.

At a high level, NMF works by taking our document-term matrix (like the TF-IDF matrix we built in Section 3) and decomposing it into two smaller matrices: one that maps documents to topics, and another that maps topics to the words in the vocabulary. It is this second matrix that we will inspect to understand what each topic is about.

### **Discovering Topics with NMF**

We will use the `tfidf_matrix` we created in Section 3. This demonstrates the power of vectorization—the same numerical representation of our text can be used for both supervised (sentiment analysis) and unsupervised (topic modeling) tasks.

**Step 1: Choose the Number of Topics**

Like with K-Means, we must decide in advance how many topics we want the algorithm to find. This is a hyperparameter you would often tune. For our small "GlobalCart" corpus, let's look for two distinct topics.

**Step 2: Build and Train the NMF Model**

 ```python
 from sklearn.decomposition import NMF

 tfidf_vectorizer = TfidfVectorizer()
 tfidf_matrix = tfidf_vectorizer.fit_transform(processed_corpus)
 
 # hyperparameter
 num_topics = 2
 nmf_model = NMF(n_components=num_topics, random_state=42)
 
 nmf_model.fit(tfidf_matrix)
 ```

### Interpreting the Results

Now for the most important part: interpretation. The NMF model does not give us labels like "Shipping Problems." Instead, it tells us which words are most important for each topic. It is our job as analysts to look at these lists of words and infer the meaning of the topic.

We can inspect the `components_` attribute of our fitted model to see the word-to-topic mapping.

```python
# Why: This loop iterates through each topic learned by the model.
# For each topic, it identifies the words with the highest weights. These
# words are the most representative of the topic.
for index, topic in enumerate(nmf_model.components_):
    # We get the top 5 words for each topic
    top_words_indices = topic.argsort()[-5:]
    top_words = [tfidf_vectorizer.get_feature_names_out()[i] for i in top_words_indices]
    print(f"Topic #{index}:")
    print(" ".join(top_words))
    print("-" * 20)
```

**Output:**

```
Topic #0:
large fit easy return counter
--------------------
Topic #1:
crush disappointed damage box package
--------------------
```

Looking at this output, we can now perform our human interpretation:

  * **Topic \#0** contains words like "return," "large," "fit," and "counter." 
    This topic seems to be about **Product Characteristics and Returns**, 
    likely related to the blender review.
  * **Topic \#1** contains words like "package," "damage," "box," and "crush." 
    This topic is clearly about **Shipping and Delivery Problems**.

!!! tip "**Topic Modeling is an Art and a Science**"

    The quality of your topics is highly dependent on a few factors:

      * **Good Pre-processing:** If you don't clean your text well, your topics might be filled with noise.
      * **Number of Topics:** Choosing the right number of topics (`n_components`) is key. You often need to experiment with different numbers to see which yields the most coherent and useful topics.
      * **Interpretation:** The final step always requires human judgment to translate the model's output into a meaningful business insight.

In just a few lines of code, we have gone from a collection of unstructured reviews to a clear, high-level summary of the main themes our customers are discussing. This allows a business to quickly identify areas of concern or praise without manually reading thousands of documents.
