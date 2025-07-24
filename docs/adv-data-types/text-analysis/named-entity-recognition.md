---
icon: material/numeric-6
---

# Information Extraction with Named Entity Recognition (NER)

Our text analysis workflows have so far focused on understanding documents at a high level. With sentiment analysis, we assigned a single label (like "positive") to a whole document. With topic modeling, we discovered the abstract themes a document belongs to.

Now, we will shift our focus to a different type of task: **information extraction**. Instead of asking "What is this document about?", we will ask, "What specific, structured information can I pull *out of* this unstructured text?"

The primary technique for this is **Named Entity Recognition (NER)**. NER is the process of locating and categorizing predefined entities—real-world objects—within a text. These entities can include product names, companies, locations, dates, people's names, and more.

For a business, this is an incredibly powerful capability. It allows you to automatically parse reviews, reports, and emails to answer questions like:

  * Which of our products are mentioned most often alongside the word "damaged"?
  * Are customers in specific cities or countries complaining about shipping times?
  * Are our competitors being mentioned in our customer support tickets?

### Our Tool for the Job: `spaCy`

While `NLTK` has NER capabilities, this is a perfect opportunity to introduce another industry-standard Python library: **`spaCy`**. `spaCy` is renowned for its speed, ease of use, and production-readiness. It provides access to highly optimized, pre-trained models that make tasks like NER incredibly straightforward.


!!! info "**Setting up `spaCy`**"

    To get started, you will need to install the library and download its small, 
    pre-trained English language model. In Google Colab environment, this is 
    already done for you. Depending on the language of the text data, you may 
    need to install a specific language model.

    ```bash
    # Install or update python setup tools
    pip install -U pip setuptools wheel
    # Install or update Specy
    pip install -U spacy
    # Next, download the English model package
    python -m spacy download en_core_web_sm
    ```

### Performing NER in Practice

Using `spaCy` to perform NER is a simple, three-step process: load a model, process your text, and then iterate through the identified entities.

Let's use a new example from our "GlobalCart" dataset that contains a few different entities.

```python
import spacy
from spacy import displacy

# Why: We load the pre-trained English model we just downloaded. 
# This 'nlp' object now contains all the components of the processing pipeline, including the NER model.
nlp = spacy.load("en_core_web_sm")

# A new, more complex example document
text = "On July 23, a customer from Berlin, Sarah Miller, reported that her Pro-Grade Blender from GlobalCart arrived with a defect."

# Why: We process the text with the 'nlp' object. This creates a 'doc' object that
# has been tokenized, tagged, and analyzed. The named entities are stored in the `doc.ents` attribute.
doc = nlp(text)

# Why: We can now iterate through the identified entities and print their text
# and their corresponding label.
print("Identified Entities:")
for ent in doc.ents:
    print(f"- Text: {ent.text}, Label: {ent.label_}")
# The following line assumes a notebook environment.
# In others, use displacy.serve()
# displacy output not shown.
displacy.render(doc, style="ent")

```

**Output:**

```
Identified Entities:
- Text: July 23, Label: DATE
- Text: Berlin, Label: GPE
- Text: Sarah Miller, Label: PERSON
- Text: Pro-Grade Blender, Label: PRODUCT
- Text: GlobalCart, Label: ORG
```

In just a few lines of code, `spaCy` has transformed an unstructured sentence into structured, categorized data.

**Rendered Table of Entities:**

| Entity Text | Label | Description |
| :--- | :--- | :--- |
| July 23 | `DATE` | An absolute or relative date. |
| Berlin | `GPE` | Geopolitical Entity (countries, cities, states).|
| Sarah Miller| `PERSON` | A person's name. |
| Pro-Grade Blender| `PRODUCT`| An object, vehicle, or other product. |
| GlobalCart | `ORG` | An organization, company, or agency. |

!!! question "**What's Happening Under the Hood?**"

    You might wonder how `spaCy` accomplishes this. The pre-trained `en_core_web_sm` model contains a sophisticated deep learning neural network that has been trained on millions of text examples. It learned to recognize the patterns and contexts that typically surround named entities. For this course, it's not necessary to understand the complex architecture of the model itself. The key skill is knowing how to effectively use this powerful, pre-trained tool to extract valuable, structured information from your text data.

!!! info "**Connecting the Dots: Using NER to Enhance Vectorization**"
    
    Now that you can identify named entities, you can use that information as an advanced step in your data preparation to create more powerful and meaningful features for your models.
    
    **The Problem:**
    Our standard pre-processing workflow would take a phrase like "**Pro-Grade Blender**" and, after cleaning, might result in two separate tokens: `['pro-grade', 'blender']`. When this goes into the `TfidfVectorizer`, the link between these two words is lost. The model sees them as two independent features.
    
    **The Solution:**
    A more advanced workflow involves running NER *before* vectorization to "weld" the words of an entity into a single, protected token.
    
    The process would look like this:
    
    1.  Process your raw text with `spaCy` to identify the entities.
    2.  Create a new version of the text where the spaces within each entity are replaced with underscores.
    3.  Feed this modified text into your `TfidfVectorizer`.
    
    **Conceptual Example:**
    
    * **Original Text:** `"her Pro-Grade Blender from GlobalCart arrived"`
    * **Modified Text (after NER):** `"her Pro-Grade_Blender from GlobalCart arrived"`
    * **Resulting Feature:** The vectorizer will now learn a single feature for `pro-grade_blender`, preserving the full identity of the product.
    
    **Why is this useful?**
    This technique protects the integrity of important multi-word concepts, such as product names, company names, and locations, leading to a richer and more accurate numerical representation of your text. It's a powerful way to inject domain knowledge directly into your feature set.
    
By extracting entities, you can now aggregate and analyze this information. For example, you could count how many times each product is mentioned or create a map showing the locations of customers who are providing feedback. This ability to create structured data from unstructured text is a cornerstone of modern data analysis.
