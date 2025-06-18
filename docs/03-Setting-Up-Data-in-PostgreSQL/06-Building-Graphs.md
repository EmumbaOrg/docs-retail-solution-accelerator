
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

Each sentiment relationship is directional (from review to feature) and includes metadata like `sentiment`, `product_id`, and `feature_id`.

---

## Why Build a Graph?

Using a graph database allows AI agents and analysts to:

- Traverse relationships naturally: “Find all reviews for features in the 'Camera' category.”
- Identify patterns: “Which features are most criticized across smartphone reviews?”
- Power personalization: “What similar products have better sentiment scores for ‘Durability’?”

This model significantly reduces complexity compared to relational joins and enables deeper reasoning over connections.

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

### Diagram Notes

- **Nodes**: Represent real-world concepts: Products, Features, and Reviews.
- **Edges**: Capture relationships and sentiment between them.
- Each edge can carry its own **properties**, enabling more nuanced filtering and querying.
