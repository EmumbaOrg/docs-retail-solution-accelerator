# 3.5 Generating Features and Sentiments

In this section, you'll explore how the AgenticShop application uses **in-database AI** to extract valuable insights from raw product reviews.

Thanks to the `azure_ai` extension in Azure Database for PostgreSQL, we can embed powerful language models directly into SQL queries. This enables us to process reviews and identify:

- **Which product feature** a user is talking about
- **What sentiment** (positive, negative, or neutral) they express about that feature

These insights help populate the `product_reviews` table with structured fields like `feature_id` and `sentiment`, which downstream agents and search systems rely on.

---

## Try It Yourself: Extract Sentiment from a Review

Here’s an example SQL query that demonstrates how we use the `azure_ai.extract` function to classify sentiment. You can run this using Postgres extension of VS Code to see it in action.

### 📌 Use Case: Sentiment Classification

!!! tip "You can run this command using the Postgres Extension to try out generating the sentiment."
Query:

```sql
SELECT azure_ai.extract(
  'Review: The battery life is incredible.',
  ARRAY['sentiment - sentiment about the feature as in positive, negative, or neutral'],
  model => 'gpt-4o'
) ->> 'sentiment' AS sentiment;
```

Expected Output:

```
sentiment
----------
positive
```

---

## Try It Yourself: Extract the Product Feature Being Mentioned

This example shows how to extract **which feature** is being talked about in the review. We guide the LLM using a **schema prompt** listing possible feature names.

!!! tip "You can run this command using Postgres extension for VS code to try out extracting product feature."
Query:

```sql
SELECT azure_ai.extract(
  'I love how lightweight and portable this device is.',
  ARRAY['productFeature: string - A feature of a product. Features should be from: weight, battery life, screen quality, portability or NULL'],
  model => 'gpt-4o'
) ->> 'productFeature' AS extracted_feature;
```

Expected Output:

```
extracted_feature
--------------------
portability
```

!!! note "🧠 The AI model picks from the list of feature names you provide in the schema prompt. This is why the feature list is aggregated dynamically in the actual workflow."

---

## Behind the Scenes

Although you’re testing this manually here, the production system runs these AI queries in **batches** as part of the data ingestion pipeline using Alembic migrations. During deployment:

- Reviews are processed in chunks
- Each review is paired with the most likely features based on its product
- The `azure_ai.extract()` function is used to assign feature IDs and sentiments directly in SQL

This approach keeps everything **in-database**, minimizing round-trips and making the AI processing fully declarative and observable.

## Configurable Execution for Faster Migrations

The sentiment and feature extraction runs as part of Alembic migrations using `azure_ai.extract()` in batch mode. However, to speed up migrations during development or testing, we’ve introduced a feature flag: `USE_AZURE_AI_FOR_REVIEWS`.

When this flag is set to `True`, reviews are processed using live AI queries.

If the flag is `False` (the default), no AI queries are executed during migration, and instead, the migration loads pre-computed sentiment and feature data from a CSV file.

This approach balances the power of in-database AI with flexibility and speed—especially useful for CI pipelines or faster local setup. You can modify this logic in the Alembic migration script if needed.
