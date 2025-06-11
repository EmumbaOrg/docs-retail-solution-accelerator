# Integrate Generative AI into Azure Database for PostgreSQL – Flexible Server

The **AgenticShop** prototype showcases how **Generative AI (GenAI)** can be deeply integrated into your database infrastructure to power intelligent e-commerce experiences. Built on **Azure Database for PostgreSQL – Flexible Server**, this solution leverages a combination of **vector search**, **graph modeling**, and **in-database AI** to deliver advanced personalization and product discovery.

GenAI uses **natural language processing (NLP)** techniques like **prompting** and **retrieval-augmented generation (RAG)** to understand user intent, extract actionable insights, and generate personalized responses. This enables use cases like:

- Extracting structured features and sentiments from unstructured product reviews
- Modeling user–product–review relationships as a graph
- Powering multi-modal product discovery: *“Headphones with positive reviews about noise cancellation”*
- Dynamically updating personalization profiles based on user conversations

## PostgreSQL Extensions Used in This POC

This solution integrates several key PostgreSQL extensions to enable AI-native capabilities:

### `azure_ai`

The **Azure AI** extension allows direct interaction with Azure’s AI services from within SQL. In AgenticShop, it is used to:

- Extract **product features** and **sentiment scores** from free-text reviews during data ingestion
- Enable in-database feature tagging for downstream personalization and filtering
- Integrate seamlessly with LLMs like **Azure OpenAI**

### `vector` and `pg_diskann`

The **vector** extension enables storage and querying of vector embeddings alongside your data.
The **pg_diskann** extension provides high-speed, scalable **approximate nearest neighbor (ANN)** search using the DiskANN algorithm.

These are used to:

- Store semantic embeddings for product descriptions and reviews
- Perform fast vector similarity queries via **LlamaIndex**
- Power smart search experiences in real-time

### `apache_age`

The **Apache AGE** extension transforms your PostgreSQL database into a graph database, enabling:

- Representation of complex relationships like:
  _User → wrote → Review → talks_about → Feature → belongs_to → Product_
- Querying with **Cypher** to answer:
  - *“Headphones with positive reviews about noise cancellation”*

---

## What You'll Do in This Section

In this module, you'll prepare your PostgreSQL database to support these AI-driven capabilities:

- [ ] **Install Extensions**
  Install `azure_ai`, `pg_diskann`, `vector`, and `apache_age`

- [ ] **Configure Azure AI Integration**
  Set up `azure_ai` with connection credentials for Azure OpenAI

- [ ] **Embed Intelligence into Data**
  - Generate and store embeddings for product content and reviews
  - Use `azure_ai` to extract sentiment and key features from raw reviews

- [ ] **Enable Scalable Search**
  - Improve vector query performance with **DiskANN**

- [ ] **Enable Graph-Based Reasoning with Apache AGE**
  - Model relationships between users, reviews, features, and products as a graph

## Note on Manual Steps

> **Manual Configuration as an Optional Deep Dive**
> While this section walks through how to manually install and configure PostgreSQL extensions (e.g., `azure_ai`, `pg_diskann`, `apache_age`) and generate embeddings or run feature extraction:
>
> - These steps are included to show what is happening behind the scenes.
> - In practice, the full setup is **automated using Infrastructure-as-Code (IaC)** via **Bicep scripts**, which provision the database and configure the required extensions.
> - **Data ingestion**, **vector generation**, and **in-database AI processing** (e.g., feature and sentiment extraction) are executed during application bootstrapping using **Alembic**, a lightweight database migration tool for Python. Alembic ensures database schema consistency across environments.
> - **Vector embeddings** for product content and reviews are generated using the **LlamaIndex** framework and stored in the database during these migrations.

This walk-through will help you:

- Understand how the PostgreSQL extensions are configured
- See how embeddings and AI operations are applied to data during migrations
- Gain visibility into what’s automated behind the scenes via **Bicep** and **Alembic**

