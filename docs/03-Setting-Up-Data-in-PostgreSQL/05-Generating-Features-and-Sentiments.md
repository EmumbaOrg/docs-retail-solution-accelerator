# 3.5 Generating Features and Sentiments

In this section, you'll explore how the AgenticShop application uses **in-database AI** to extract valuable insights from raw product reviews.

Thanks to the `azure_ai` extension in Azure Database for PostgreSQL, we can embed powerful language models directly into SQL queries. This enables us to process reviews and identify:

- **Which product feature** a user is talking about
- **What sentiment** (positive, negative, or neutral) they express about that feature

These insights help populate the `product_reviews` table with structured fields like `feature_id` and `sentiment`, which downstream agents and search systems rely on.

---

## 🧪 Try It Yourself: Extract Sentiment from a Review

Here’s an example SQL query that demonstrates how we use the `azure_ai.extract` function to classify sentiment. You can run this directly in **pgAdmin** to see it in action.

### 📌 Use Case: Sentiment Classification

```sql
SELECT azure_ai.extract(
  'Review: The battery life is incredible. Feature: battery life',
  ARRAY['sentiment - sentiment about the feature as in positive, negative, or neutral'],
  model => 'gpt-35-turbo'
) ->> 'sentiment' AS sentiment;
```

Expected Output:

```
sentiment
----------
positive
```

---

## 🧪 Try It Yourself: Extract the Product Feature Being Mentioned

This example shows how to extract **which feature** is being talked about in the review. We guide the LLM using a **schema prompt** listing possible feature names.

```sql
SELECT azure_ai.extract(
  'I love how lightweight and portable this device is.',
  ARRAY['productFeature: string - A feature of a product. Features should be from: weight, battery life, screen quality, portability or NULL'],
  model => 'gpt-35-turbo'
) ->> 'productFeature' AS extracted_feature;
```

Expected Output:

```
extracted_feature
--------------------
portability
```

> 🧠 Note: The AI model picks from the list of feature names you provide in the schema prompt. This is why the feature list is aggregated dynamically in the actual workflow.

---

## 🧩 Behind the Scenes

Although you’re testing this manually here, the production system runs these AI queries in **batches** as part of the data ingestion pipeline using Alembic migrations. During deployment:

- Reviews are processed in chunks
- Each review is paired with the most likely features based on its product
- The `azure_ai.extract()` function is used to assign feature IDs and sentiments directly in SQL

This approach keeps everything **in-database**, minimizing round-trips and making the AI processing fully declarative and observable.

---

> 🔍 Tip: You can view intermediate results using SQL before they’re written back to the `product_reviews` table, making debugging and experimentation simple.
