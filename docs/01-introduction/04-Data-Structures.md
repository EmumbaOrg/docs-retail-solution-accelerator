# 1.4 Backend Data Structures

This section provides an overview of the core database schema and data artifacts used in the AgenticShop backend. It highlights **key tables and fields** that enable AI-powered personalization, semantic search, review analysis, and conversational AI. It also explains how these components are leveraged by backend services and Azure AI integrations.

## Understanding the Table Schema

AgenticShop processes **user profiles, product catalogs, product variants, customer reviews, product features, personalized content, and chat interactions** using a structured PostgreSQL schema. Below is an overview of the **key tables** and their roles:

### Core Tables & Their Purpose

| **Table**                        | **Description** |
|-----------------------------------|----------------------------------------------------------------------------------------------------|
| `users`                          | Stores user profiles, including preferences (hobbies, lifestyle), demographics, and search history. Used for personalization. |
| `products`                       | Contains core product catalog information, specifications (JSONB), pricing, and descriptions.      |
| `product_images`                 | Stores URLs for product images, linked to the `products` table.                                    |
| `features`                       | Defines distinct product features (e.g., "Water Resistance", "Battery Life") and their applicable categories (JSONB). |
| `product_features`               | A junction table linking products to their respective features (many-to-many relationship).        |
| `variants`                       | Represents specific variations of a product (e.g., based on color, size), including price and stock levels. |
| `variant_attributes`             | Stores key-value attributes for each product variant (e.g., `attribute_name`: "Color", `attribute_value`: "Blue"). |
| `product_reviews`                | Stores customer reviews, ratings, associated product, user, and AI-extracted sentiment and feature ID. |
| `personalized_product_sections`  | Stores AI-generated personalized content (JSONB) for specific user-product combinations, including status and tracing IDs for AI workflows. |
| `embeddings_products`            | Vector store holding embeddings for product data (e.g., name, description, category) to enable semantic search for products. |
| `embeddings_reviews`             | Vector store holding embeddings for product review text to enable semantic search and analysis of reviews. |

### How These Tables Work Together

1.  **User data** (`users`) provides context for personalization.
2.  **Product information** (`products`, `product_images`, `features`, `product_features`, `variants`, `variant_attributes`) forms the catalog that AI agents interact with.
3.  **Customer reviews** (`product_reviews`) are processed by AI to extract sentiment and identify key features discussed.
4.  **Embeddings** (`embeddings_products`, `embeddings_reviews`) are generated from product and review text, enabling semantic search and retrieval.
5.  AI agents generate **personalized content** (`personalized_product_sections`) based on user profiles, product data, reviews, and inventory status.
7.  **Apache AGE graph data** (derived from these tables) models relationships between products, reviews, and features for advanced analytics and explainable AI.


![Database ERD Diagram](../img/solution-accelerator-database-erd.png)

---

## Key Fields Used in AI Processing

AgenticShop integrates with Azure AI services, vector databases, and graph databases to extract, validate, personalize, and process information. Below are **key fields** that play a crucial role in these AI workflows:

### Relational, Vector & AI Fields

| **Field Name**                  | **Table(s)**                                     | **AI Usage** |
|---------------------------------|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| `embedding`                     | `embeddings_products`, `embeddings_reviews`      | Stores vector embeddings for semantic search, similarity retrieval, and RAG for products and reviews.             |
| `personalization`               | `personalized_product_sections`                  | JSONB field storing structured AI-generated content (features, descriptions) tailored to the user and product.    |
| `specifications`                | `products`                                       | JSONB field for flexible product attributes; can be used by AI to match user needs or extract detailed info.    |
| `review` (text)                 | `product_reviews`                                | Raw text of customer reviews, processed by NLP models for sentiment analysis and feature extraction.              |
| `sentiment`                     | `product_reviews`                                | AI-extracted sentiment (positive, negative, neutral) from review text.                                            |
| `feature_id`                    | `product_reviews`, `product_features`            | Links reviews and products to specific features, enabling targeted analysis and feature-based recommendations.  |
| `attribute_name`, `attribute_value` | `variant_attributes`                           | Used by AI (e.g., Inventory Agent) to find product variants matching user preferences (e.g., color, size).      |
| `hobbies`, `lifestyle_preferences`, `search_history`, `age`, `gender`, `location` | `users`                                          | User profile attributes used by AI agents to understand user context and personalize recommendations/content.     |
| `status`                        | `personalized_product_sections`                  | Tracks the state of AI content generation workflows (pending, running, done, failed).                             |
| `phoenix_trace_id`              | `personalized_product_sections`                  | Links to observability platforms (like Arize Phoenix) for tracing and debugging AI agent execution.             |
