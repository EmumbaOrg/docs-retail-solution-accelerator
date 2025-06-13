# 3.2 Alembic Migrations

In this section, you'll learn about how **Alembic** is used to manage the database schema in the AgenticShop backend.

## What is Alembic?

[Alembic](https://alembic.sqlalchemy.org/) is a lightweight database migration tool for Python. It is commonly used alongside SQLAlchemy to manage schema changes over time while preserving data. Alembic enables you to apply version-controlled changes to your database, ensuring consistent environments across development, staging, and production.

In the **AgenticShop** solution, Alembic plays a central role in:

- Creating and modifying relational schema (tables, indexes, constraints)
- Populating initial data from CSVs
- Generating and storing vector embeddings
- Applying in-database AI operations using the `azure_ai` extension
- Building a graph layer using Apache AGE for advanced search and reasoning

## Why Use Alembic in This Project?

Instead of requiring manual SQL scripts, Alembic automates:

- The full setup of the PostgreSQL schema
- Seeding and cleaning data
- Integration with AI services and vector store configuration
- Controlled rollouts of graph-based data structures

These migrations are run automatically during the deployment process, but you can also execute them manually during development for better observability.

## Where to Find the Migration Code

The migration scripts are located in the [`alembic/versions`](https://github.com/Azure-Samples/postgres-agentic-shop/tree/main/backend/alembic) directory of the repository. Each migration file corresponds to a specific step in the data setup.

## Running Alembic Migrations Step-by-Step

This section walks through each **Alembic migration** used in the AgenticShop backend. These migrations are designed to progressively build out your database schema, seed it with product and review data, integrate AI capabilities (e.g., embeddings and sentiment analysis), and create a graph-based representation using Apache AGE.

These steps are **automated during deployment**, but you can also run them manually for better visibility or debugging.

To run any migration manually:

```bash
alembic upgrade <revision_id>
```

---

## 1. Initial Migration

**Revision ID:** `1b33292593a9`
**Command:**

```bash
alembic upgrade 1b33292593a9
```

**What it does:**

- Creates all base relational tables:
  - `products`, `users`, `product_reviews`, `features`, `variants`
- Sets up foreign key relationships and indexes
- Adds `personalized_product_sections` table for user-agent output tracking
- Defines enums and other types used across the system

---

## 2. Seed Data Migration

**Revision ID:** `ef85393336cb`
**Command:**

```bash
alembic upgrade ef85393336cb
```

**What it does:**

- Loads data from **CSV files** located in the `backend/data/` directory
- Populates core tables like `products`, `users`, `features`, `product_reviews`
- Cleans and transforms data (e.g., replaces empty strings with `NULL`)
- Inserts **user preferences into Mem0 memory** for personalization workflows

> 🗂️ This migration is powered by a utility that loads and parses the CSV files at startup.

---

## 3. Generate Embeddings and Create Vector Indexes

**Revision ID:** `cc30da9b96c1`
**Command:**

```bash
alembic upgrade cc30da9b96c1
```

**What it does:**

- Uses **LlamaIndex** to generate vector embeddings for:
  - Product descriptions
  - Product reviews
- Stores embeddings in dedicated embedding tables
- Creates `pg_diskann` indexes for fast approximate nearest neighbor (ANN) search

> 🧠 Table names for embeddings are dynamically loaded from environment variables like `DB_EMBEDDING_TABLE_FOR_PRODUCTS`.

---

## 4. Generate Sentiment and Feature IDs Using Azure AI

**Revision ID:** `f3e916f4940d`
**Command:**

```bash
alembic upgrade f3e916f4940d
```

**What it does:**

- Configures the `azure_ai` extension with your OpenAI credentials
- Uses Azure OpenAI to:
  - Extract product **features** mentioned in each review
  - Classify **sentiment** as positive, negative, or neutral
- Updates the `product_reviews` table with enriched values

> Controlled by the environment variable `USE_AZURE_AI_FOR_REVIEWS`.

---

## 5. Create Graph Using Apache AGE

**Revision ID:** `f41ba96cab17`
**Command:**

```bash
alembic upgrade f41ba96cab17
```

**What it does:**

- Enables the `age` extension and creates a graph: `product_review_graph`
- Creates **vertices** for:
  - Products
  - Reviews
  - Features
- Creates **edges** to represent:
  - Product → Review (`HAS_REVIEW`)
  - Product → Feature (`HAS_FEATURE`)
  - Review → Feature with sentiment (`positive_sentiment`, `negative_sentiment`, `neutral_sentiment`)
- Enables Cypher-based graph queries across product-review-feature relationships

---

## ✅ Run All Migrations in One Go

To run everything—from schema setup to data seeding, embeddings, AI enrichment, and graph creation:

```bash
alembic upgrade head
```

> ⚠️ Make sure your `.env` file is configured with valid credentials and settings.

