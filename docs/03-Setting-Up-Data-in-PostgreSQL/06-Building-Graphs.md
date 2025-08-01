
# 3.6 Building Graphs for Relationship Insights

To unlock richer, more contextual insights from structured data, AgenticShop uses **Apache AGE**, a PostgreSQL extension that brings **graph database capabilities** to relational data. By transforming reviews, products, and features into a **graph model**, we enable AI agents to perform sophisticated relationship-based queries.

---

## Introducing the `product_review_graph`

We define a graph named `product_review_graph` to model the interconnected nature of product reviews and their associated features. This graph provides the structural foundation for exploratory analysis and downstream AI tasks like recommendation or trend detection.

Using **Cypher**, Apache AGE’s query language, we construct nodes and relationships that mirror how customers discuss products and features in their reviews.

---

## Graph Entities: Nodes

Each node in the graph represents a distinct type of entity from the underlying database.

1. **Product**
    - Represents a retail product.
    - Properties: `id`, `name`, `category`
    - Example:
        ```cypher
        (p:Product {id: 101, name: "SmartWatch X200", category: "Electronics"})
        ```

2. **Review**
    - Captures a user’s review.
    - Properties: `id`, `product_id`, `feature_id`, `sentiment`, `text`
    - Example:
      ```cypher
      (r:Review {id: 5001, text: "Great battery life!", sentiment: "positive"})
      ```

3. **Feature**
    - Describes a functional or descriptive product attribute.
    - Properties: `id`, `name`, `categories` (JSON array)
    - Example:
      ```cypher
      (f:Feature {id: 10, name: "Battery Life", categories: ["Electronics", "Wearables"]})
      ```

---

## Graph Relationships: Edges

Edges define how these entities relate to one another—particularly which products are being reviewed and what sentiments are expressed about specific features.

1. **Product `-[HAS_FEATURE]->` Feature**
    - Links a product to its associated features.
    - Example:
      ```cypher
      (p:Product)-[:HAS_FEATURE]->(f:Feature)
      ```

2. **Product `-[HAS_REVIEW]->` Review**
    - Connects products to their customer reviews.
    - Example:
      ```cypher
      (p:Product)-[:HAS_REVIEW]->(r:Review)
      ```

3. **Review `-[positive_sentiment]->` Feature**
4. **Review `-[negative_sentiment]->` Feature**
5. **Review `-[neutral_sentiment]->` Feature**
    - Connects reviews to features, annotated with the expressed sentiment.
    - Example:
      ```cypher
      (r:Review)-[:positive_sentiment {feature_id: 10, product_id: 101}]->(f:Feature)
      ```

!!! note "Each sentiment relationship is directional (from review to feature) and includes metadata like `sentiment`, `product_id`, and `feature_id`."

---

## Visualizing the Graph

Here’s a simplified schematic of how entities connect:

```
+-----------+     HAS_FEATURE     +-----------+
|  Product  |-------------------->|  Feature  |
+-----------+                     +-----------+
      |                                 ^
      |                                 |
      | HAS_REVIEW                      | positive_sentiment
      |                                 | negative_sentiment
      v                                 | neutral_sentiment
+-----------+                           |
|  Review   |---------------------------+
+-----------+
```

!!! note "Diagram Notes"

    - **Nodes**: Represent real-world concepts: Products, Features, and Reviews.
    - **Edges**: Capture relationships and sentiment between them.
    - Each edge can carry its own **properties**, enabling more nuanced filtering and querying.


## From Natural Language to Graph Query

Let’s walk through how the graph is used to answer a user query like:

> _“Wireless headphones with positive reviews about noise cancellation”_

This type of question involves multiple steps:

1. Understanding the **product category** (“wireless headphones”).

2. Identifying the **feature** mentioned (“noise cancellation”).

3. Filtering for **positive sentiment** from reviews.

To map the user's phrasing of the feature to a canonical feature in our system, we use an **LLM-powered feature extraction** technique via `azure_ai`. This helps match varied expressions like “noise cancellation” to a defined feature (e.g., `"Noise Reduction"`).

Here’s the query using **azure_ai** that performs that step:

!!! tip "You can run this command using the Postgres extension of Vscode to try out extracting product feature."
```SQL
WITH feature_schema AS (
    SELECT
        'productFeature: string - A feature of a product. Features: ' ||
        STRING_AGG(fx.feature_name, ', ' ORDER BY fx.feature_name) || ' or NULL' AS feature_schema,
        ARRAY_AGG(fx.id) AS feature_ids,
        ARRAY_AGG(fx.feature_name) AS feature_names
    FROM features fx
)
SELECT
    (azure_ai.extract('{feature_name}', ARRAY[(SELECT feature_schema FROM feature_schema)],
    '{settings.LLM_MODEL}')::JSONB->>'productFeature') AS mapped_feature
```

Once the feature is resolved, a Cypher query is constructed to count how many positive reviews reference that feature for each product in the given category:

```SQL
SELECT * FROM ag_catalog.cypher('product_review_graph', $$
    MATCH (p:Product), (f:Feature), (r:Review)
    WHERE p.id IN {product_ids} AND f.name = '{feature_name}'
    MATCH (p)-[frel:HAS_FEATURE]->(f)
    MATCH (f)<-[rel:{sentiment} {{product_id: p.id}}]-(r)
    RETURN p.id AS product_id, COUNT(rel) AS positive_review_count
$$) AS graph_query(product_id agtype, positive_review_count agtype)
ORDER BY (graph_query).positive_review_count DESC;
```

This enables the system to rank products like wireless headphones based on the number of positive mentions for the desired feature, surfacing the most well-reviewed products for that specific aspect.

!!! note "This powerful combination of LLM-driven feature resolution and graph traversal using Cypher unlocks complex insights from unstructured user queries—without requiring hardcoded logic or manual keyword mapping."