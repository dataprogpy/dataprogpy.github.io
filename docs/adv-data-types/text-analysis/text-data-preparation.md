---
icon: material/numeric-2
---

# Text Preparation with NLTK

Before we can perform any analysis, we must first translate the messy, human-centric world of text into the clean, structured format that a machine can understand. This crucial phase, often called **text pre-processing** or **normalization**, involves cleaning and standardizing our text data. The goal is to reduce noise and variability, ensuring that the meaningful signals in the text are not obscured.

In this section, we will use the **Natural Language Toolkit (NLTK)**, a powerful and foundational Python library, to perform these essential steps. Think of NLTK as a workshop full of specialized tools; we will learn how to pick the right tool for each job to take our raw text and forge it into something ready for analysis.

## The Goal: From Raw Document to Clean Tokens

As we established in the previous section, our text data is organized in a `corpus -> document -> token` hierarchy. Our objective in this pre-processing stage is to take each **document** (a single customer review) and convert it into a list of clean, meaningful **tokens**.

Let's take one of the reviews from our fictional "GlobalCart" dataset:
`"My package with the Comfort-Fit Running Shoes arrived damaged, and the box was completely crushed. Disappointed."`

Our goal is to transform this raw sentence into a standardized list of tokens, perhaps something like: `['package', 'comfort-fit', 'running', 'shoe', 'arrive', 'damage', 'box', 'completely', 'crush', 'disappointed']`. Notice how punctuation is gone, words are in lowercase, and some words are shortened to their root form. Let's explore the steps to get there.

## The Pre-processing Toolkit

We will now walk through the most common pre-processing steps. We'll treat each as a distinct tool we are adding to our collection.

!!! activity "**Setting up your Toolkit**"

    To use NLTK's pre-processing tools, you may first need to download some of its data packages. You can do this by running the following Python code. It only needs to be done once.

    ```python
    import nltk
    
    # Downloads the 'punkt' tokenizer model, which is used to split text into words.
    nltk.download('punkt_tab')
    
    # Downloads the 'stopwords' corpus, a list of common words to ignore.
    nltk.download('stopwords')
    
    # Downloads the 'averaged_perceptron_tagger', used for Part-of-Speech tagging.
    nltk.download('averaged_perceptron_tagger')
    
    # Downloads 'wordnet', a large lexical database of English used by the lemmatizer.
    nltk.download('wordnet')
    ```


### Tokenization: Breaking Down Sentences

The first step is always **tokenization**. This is the process of breaking down a string of text into its individual components, or tokens. These are typically words, numbers, and punctuation marks.

```python
from nltk.tokenize import word_tokenize

raw_text = "My package with the Comfort-Fit Running Shoes arrived damaged."

tokens = word_tokenize(raw_text)

print(tokens)
```

**Output:**

```
['My', 'package', 'with', 'the', 'Comfort-Fit', 'Running', 'Shoes', 'arrived', 'damaged', '.']
```

After this step, we are no longer working with a simple string but with a list of tokens that we can programmatically inspect and manipulate. We will also convert all tokens to lowercase to ensure that words like "Package" and "package" are treated as the same token.

### Stop Word Removal: Filtering Out the Noise

Many words in the English language, such as "the," "a," "in," and "with," are essential for grammar but add little semantic value for analysis. These common words are called **stop words**. Removing them helps our analysis focus on the words that carry the most meaning.

```python
from nltk.corpus import stopwords

# Why: We create a set of English stop words for fast lookup.
# Using a set is much faster than using a list for checking if a word is present.
stop_words = set(stopwords.words('english'))

# Let's use a new list of tokens from our previous step, after lowercasing them
raw_tokens = ['my', 'package', 'with', 'the', 'comfort-fit', 'running', 'shoes', 'arrived', 'damaged', '.']

# Why: We use a list comprehension to build a new list containing only the tokens
# that are NOT in our stop_words set.
filtered_tokens = [word for word in raw_tokens if word.lower() not in stop_words]

print(filtered_tokens)
```

**Output:**

```
['package', 'comfort-fit', 'running', 'shoes', 'arrived', 'damaged', '.']
```

Notice how the list is now shorter and more focused on the core concepts of the sentence.

### Stemming and Lemmatization: Finding Word Roots

Our text contains different forms of the same word, like "run," "ran," and "running." To a computer, these are three distinct tokens. **Stemming** and **Lemmatization** are two techniques for reducing words to their root form.

**Stemming** is a crude, rule-based process of chopping off the ends of words. It's fast but can sometimes result in non-dictionary words.

```python
from nltk.stem import PorterStemmer

stemmer = PorterStemmer()

# Why: We apply the stemmer to each word to reduce it to its 'stem'.
# This is a blunt but fast way to group word variations together.
stemmed_tokens = [stemmer.stem(token) for token in filtered_tokens]

print(stemmed_tokens)
```

**Output:**

```
['packag', 'comfort-fit', 'run', 'shoe', 'arriv', 'damag', '.']
```

!!! warning "Stemming can be Aggressive"

    Notice that "package" became "packag" and "arrived" became "arriv." Stemmers apply simple suffix-stripping rules and don't care about the context or dictionary validity of the resulting stem.

**Lemmatization**, on the other hand, is a more sophisticated process that uses a dictionary and parts of speech to bring a word back to its root form, known as its **lemma**. This approach is slower but more accurate.

To perform lemmatization effectively, we first need to know the part of speech of each word. **Part-of-Speech (POS) tagging** is the process of labeling each word in a sentence with its corresponding grammatical category (e.g., noun, verb, adjective).

```python
from nltk.stem import WordNetLemmatizer
from nltk.tag import pos_tag

# Let's use our filtered tokens from before
tokens_to_process = ['package', 'comfort-fit', 'running', 'shoes', 'arrived', 'damaged']

# Why: pos_tag analyzes the list of words and assigns a part-of-speech tag to each one.
# For example, NN for singular noun, VBD for past tense verb.
tagged_tokens = pos_tag(tokens_to_process)
```

**Output (formatted for reading and presentation):**

| Token | POS Tag | Description |
| :--- | :--- | :--- |
| package | `NN` | Noun, singular |
| comfort-fit | `JJ` | Adjective |
| running | `VBG`| Verb, gerund |
| shoes | `NNS`| Noun, plural |
| arrived | `VBD`| Verb, past tense |
| damaged | `VBN`| Verb, past participle |

Now, we can use these tags to get more accurate lemmas.

```python
lemmatizer = WordNetLemmatizer()

# Why: By providing the POS tag, we give the lemmatizer crucial context.
# It now knows 'running' is a verb and its lemma is 'run'. Without the tag, it might be treated as a noun.
# (Note: A helper function is often needed to map NLTK's POS tags to the format the lemmatizer expects).
# For simplicity, we'll show the concept directly:
lemma_run = lemmatizer.lemmatize('running', pos='v') # 'v' for verb
lemma_shoes = lemmatizer.lemmatize('shoes', pos='n')   # 'n' for noun

print(f"The lemma for 'running' as a verb is: {lemma_run}")
print(f"The lemma for 'shoes' as a noun is: {lemma_shoes}")
```

**Output:**

```
The lemma for 'running' as a verb is: run
The lemma for 'shoes' as a noun is: shoe
```


**Mapping POS Tags for Accurate Lemmatization**

You may have noticed a practical challenge when trying to use the output of `nltk.pos_tag()` with the `WordNetLemmatizer`. The `pos_tag()` function returns detailed tags from the **Penn Treebank tagset** (like `VBD` for a past-tense verb or `NNS` for a plural noun), but the lemmatizer's `pos` parameter expects a much simpler format from the **WordNet** tagset (`n` for noun, `v` for verb, `a` for adjective, `r` for adverb, or `s` for satellite adjective).

 How do we bridge this gap? The solution is to create a simple helper function that maps the tags from one set to the other.

#### The Mapping Logic

 The key insight is that most Penn Treebank tags for a given part of speech start with the same letter.

   * All adjective tags start with **'J'** (e.g., `JJ`, `JJR`, `JJS`).
   * All verb tags start with **'V'** (e.g., `VB`, `VBD`, `VBG`).
   * All noun tags start with **'N'** (e.g., `NN`, `NNS`).
   * All adverb tags start with **'R'** (e.g., `RB`, `RBR`).

 We can use this pattern to create a function that translates the tags.

#### Putting it into Practice

 Here is a complete example showing how to build and use a helper function to perform accurate, POS-aware lemmatization on a sentence.

 ```python linenums="1"
 import nltk

 nltk.download('punkt_tab')
 nltk.download('stopwords')
 nltk.download('wordnet')
 nltk.download('averaged_perceptron_tagger_eng')
 nltk.download('universal_tagset')
 nltk.download('tagsets_json')

 import polars as pl
 from nltk.corpus import stopwords, wordnet
 from nltk.stem import WordNetLemmatizer as wnl
 from nltk.tag import pos_tag

 # The helper function that performs the mapping
 def get_wordnet_pos(treebank_tag):
     """
     Maps Treebank POS tags to WordNet POS tags.
     """
     if treebank_tag.startswith('J'):
         return wordnet.ADJ
     elif treebank_tag.startswith('V'):
         return wordnet.VERB
     elif treebank_tag.startswith('N'):
         return wordnet.NOUN
     elif treebank_tag.startswith('R'):
         return wordnet.ADV
     else:
         # As a default, assume noun if the tag is not recognized
         return wordnet.NOUN

 stop_words = set(stopwords.words('english'))
 lemmatizer = wnl()
 sentence = "The children are running and playing in the beautiful gardens"
 # 1. Tokenize sentence
 tokens = nltk.word_tokenize(sentence)
 # 2. Tag the tokens
 tagged_tokens = pos_tag(nltk.word_tokenize(sentence))
 # 3. Filter the stop words
 filtered_tokens = [word for word in tokens if word.lower() not in stop_words]
 # 4. Lemmatize the tokens using POS tags
 # Stop words are not skipped here just for demonstration
 lemmas = [
     lemmatizer.lemmatize(token, get_wordnet_pos(tag))
     for token, tag in tagged_tokens
     # for token, tag in pos_tag(filtered_tokens)
 ]
 print("Tokens: ")
 print(tokens)
 print("Filtered Tokens: ")
 print(filtered_tokens)
 print("Lemmas: ")
 print(lemmas)
 print("Filtered Lemmas: ")
 print(list(lemma for lemma in lemmas if lemma.lower() not in stop_words))

 ```
 
 **Output:**
 ```
    Tokens: 
    ['The', 'children', 'are', 'running', 'and', 'playing', 'in', 'the', 'beautiful', 'gardens']
    Filtered Tokens: 
    ['children', 'running', 'playing', 'beautiful', 'gardens']
    Lemmas: 
    ['The', 'child', 'be', 'run', 'and', 'play', 'in', 'the', 'beautiful', 'garden']
    Filtered Lemmas: 
    ['child', 'run', 'play', 'beautiful', 'garden']
 ```
 
 Notice how "children" correctly became "child," "are" became "be," and "running" became "run." Without this mapping, the lemmatizer would have defaulted to treating every word as a noun, leading to incorrect results.

!!! info "**What is a Satellite Adjective?**"

    The WordNet lemmatizer also accepts `s` for **satellite adjectives**. This is a specific linguistic category for adjectives that are semantically linked to another adjective and often modify its intensity (e.g., the word "utter" in "an utter fool"). In practice, these are less common, and mapping all adjective tags (`J...`) to the general adjective category (`a`) is the standard and effective approach for most text analysis tasks.
 
!!! tip "**Stemming vs. Lemmatization: Which to Choose?**"

    * Use **Stemming** when speed is critical and you don't need the output to be human-readable. It's often sufficient for machine learning models that just need to group related words.
    * Use **Lemmatization** when you need accurate, dictionary-valid root words, especially if the results need to be interpretable by humans. For most business analyses, lemmatization is the preferred approach.

### Our Toolkit: NLTK and Its Alternatives

Throughout this section, we've used **NLTK**, the Natural Language Toolkit. It's a powerful and highly flexible library that is excellent for learning because it forces you to engage with each step of the process individually. Its modular design gives you complete control over your text-processing pipeline.

It's also worth knowing about **spaCy**, another popular Python library for text analysis. Where NLTK is a flexible workshop, spaCy is a highly optimized, modern assembly line. It's known for its speed, efficiency, and production-readiness. It often performs many pre-processing steps at once in a single, opinionated pipeline.

For this course, we will continue to use NLTK to ensure you understand the fundamentals. However, as you advance, exploring spaCy for building applications is a valuable next step.

### Code Demo: Processing Documents

```python
 def prep_doc(
    doc,
    *,
    lemmatizer=None,
    stemmer=None,
    exclude_stop_words=True,
):
    """Tokenizes, filters, and processes a text document.

    This function takes a raw text string and performs a series of NLP
    preprocessing steps, including tokenization, optional stop word removal,
    optional lemmatization, and optional stemming.

    Args:
        doc (str): The input text document to process.
        lemmatizer (object, optional): An initialized lemmatizer object with a
            `.lemmatize()` method (e.g., from NLTK). Defaults to None.
        stemmer (object, optional): An initialized stemmer object with a
            `.stem()` method (e.g., from NLTK). Defaults to None.
        exclude_stop_words (bool, optional): If True, removes common English
            stop words from the token list. Defaults to True.

    Returns:
        dict: A dictionary containing the results of the processing steps.
            The keys are:
            - "tokens" (list): The original tokens from the document.
            - "filtered_tokens" (list): Tokens after stop word removal (if enabled).
            - "lemmas" (list or None): The lemmatized version of the
              filtered tokens. None if no lemmatizer is provided.
            - "stemmed_tokens" (list or None): The stemmed version of the
              filtered tokens. None if no stemmer is provided.
    """
    tokens = nltk.word_tokenize(doc)

    filtered_tokens = tokens
    lemmas = None
    stemmed_tokens = None

    if exclude_stop_words:
        filtered_tokens = [
            word for word in tokens if word.lower() not in stop_words
        ]

    if lemmatizer:
        lemmas = [
          lemmatizer.lemmatize(token, get_wordnet_pos(tag))
          for token, tag in pos_tag(filtered_tokens)
        ]

    if stemmer:
        stemmed_tokens = [stemmer.stem(token) for token in filtered_tokens]

    return {
        "tokens": tokens,
        "filtered_tokens": filtered_tokens,
        "lemmas": lemmas,
        "stemmed_tokens": stemmed_tokens,
    }

  feedback = df.select(pl.col('FeedbackText')).to_series().to_list()

  processed_docs = [
    prep_doc(doc, lemmatizer=lemmatizer, stemmer=snowball) 
    for doc in feedback
]
```

Now that we have a clean, standardized set of tokens for each document, we are ready for the next critical phase: converting these tokens into a numerical format that a machine learning model can understand.
