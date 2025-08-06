# 4.5 🔍 Understanding What Queries Work (and Why)

Your personalized shopping assistant supports product search and recommendation for a limited dataset. To ensure relevant results, it’s important to understand how the system interprets queries, and why some may fail.

### Supported Product Categories

The dataset used in this solution contains product information and customer reviews from **only three product categories**:

- **Smartwatches**
- **Headphones**
- **Tablets**

Any query that refers to a product outside these categories (e.g., *"laptops"*, *"TVs"*, *"keyboards"*) will not return results.

!!! info "What features are supported for each category?"
    "You can review all available categories ("Headphones", "Tablets", "Smartwatches") and features in this file `backend/data/features.csv`"

---

### 🔎 Query Types and Tools

We support two types of search-powered queries using the following tools:

---

#### **Standard Vector Search**

- Used for general **product discovery** queries, such as:
    - *"Wireless earbuds"*
    - *"Smartwatch with fitness tracker"*
- Powered by **pg_diskann-based vector search**, which retrieves the most semantically relevant products based on product descriptions and specs.
- **Internally**, the system tries to infer the product category from the query (e.g., *"earbuds"* maps to *Headphones*). If the LLM fails to map the query to one of the 3 valid categories, no results will be returned.

---

#### **Sentiment-Aware Search**

- Used for **feature-based discovery with sentiment**, such as:
    - *"Find headphones with great noise cancellation"*
    - *"Tablets with reliable cellular connectivity"*
- This tool combines:
    - **Vector search**
    - **Azure AI-powered sentiment analysis and feature extraction**
- The system tries to map:
    1. The **product category** (same limitations apply as above).
    2. The **product feature** mentioned in the query.

If the mentioned feature is **not tracked in our dataset**, the query won’t work.

### ❌ Example that will fail:

> *"Smartwatch with great battery life"*

**Why it might fail**:
    - *Battery life* is **not** one of the predefined features we extract.
    - Even if user reviews mention battery life, it’s not mapped to a known feature for extraction.
    - Even if the feature existed and is extracted and mapped to a product, it *might* not be mapped with positive sentiment.

### Flexible Matching

The feature name in the query doesn’t need to be an exact match. The system can handle close variations and synonyms:

- *"Headphones with great active noise cancellation"*
- *"Headphones with great ANC"*
- *"Headphones with great noise cancellation"*

All will map to the tracked feature **Noise Cancellation**. 

### 📌 Note on Review Coverage

Even if the feature is supported, **relevant reviews must exist** for the system to extract sentiment. If no reviews discuss a feature for a product, it won't affect ranking.

---

## 🧪 Sample Queries That Work

Here are examples of queries that work well with each tool supported in the assistant. These examples can help you test and understand the system behavior.

### Product-Specific Query Handling

Used when the user is already on a product page and refers to that product directly.

**Example Queries:**

- "Show critical reviews about durability"
- "Always show a summary of critical reviews"
- "What do users dislike about this smartwatch"
- "What do people say about its battery life"
- "Is this product available in black"
- "Always highlight the products availability in red"

---

### Standard Vector Search

Used for general product discovery, especially when the user doesn't specify detailed preferences.

**Example Queries:**

- "Wireless headphones"
- "Tablets with camera"
- "Waterproof smartwatches"
- "Tablets with HD display"
- "Smartwatches with step tracking"
- "Headphones for workout"

---

### Sentiment-Aware Search

Used when the user expresses sentiment or preference toward specific product features.

**Example Queries:**

- "Headphones with great noise cancellation"
- "Smartwatches with accurate heart rate monitoring"
- "Top tablets with strong parental controls"
- "Smartwatches praised for their display quality"
- "Headphones with average ANC"

