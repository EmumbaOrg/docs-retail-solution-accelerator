# Introduction

Welcome to the **AgenticShop Guide** – a detailed reference guide built around a prototype e-commerce store that demonstrates how **Generative AI** can be used to personalize shopping experiences through a **multi-agent architecture**.

In this guide, you will:

- Learn how to structure and implement **multi-agent workflows** using **LlamaIndex**
- Explore **in-database AI capabilities** using the **Azure AI extension** on **Azure Database for PostgreSQL – Flexible Server**
- Interact with product data, reviews, and user profiles using **graph queries** powered by **Apache AGE**
- Use **pg_diskann** for fast and efficient **vector search** in PostgreSQL

You’ll work with a curated sample dataset that includes:

- A catalog of products and their features
- User profiles and product reviews

Throughout the guide, you’ll discover how GenAI-based systems work and how they can be applied in real-world scenarios.

You can find the complete source code here:
[github.com/Azure-Samples/postgres-agentic-shop](https://github.com/Azure-Samples/postgres-agentic-shop)

The application has the following architecture:

![High-level architecture diagram for the solution](../img/solution-accelerator-architecture.png)

---

## Learning Objectives

This solution accelerator will equip you with the skills to enhance your applications with AI capabilities using Azure Database for PostgreSQL and Azure AI Services. You’ll gain hands-on experience integrating advanced AI-powered extraction into the data ingestion process.

By incorporating Apache AGE, you’ll learn how to model and store product-review relationships as a graph—capturing features, sentiments, and user interactions. Using Azure OpenAI, you’ll extract structured product attributes and sentiment insights, enabling rich graph construction and support for complex search queries like “headphones with positive reviews about noise cancellation” that include both vector and Cypher queries.

You’ll explore pg_diskann to perform fast and scalable vector similarity searches within PostgreSQL, enabling real-time product and review retrieval.

The guide also demonstrates how to design and orchestrate multi-agent workflows using LlamaIndex, leveraging strategies such as tool-using agents, Text-to-SQL, and Retrieval-Augmented Generation (RAG). You’ll also learn how to incorporate persistent agent memory using Mem0, allowing agents to retain user-specific context across interactions.

To ensure transparency and debuggability, the solution includes end-to-end observability using Phoenix (by Arize), enabling tracing, span-level inspection, and performance monitoring of each agent’s execution within the workflow.

**By completing this guide, you will learn how to:**

- Leverage Azure OpenAI to extract product features and sentiment for downstream graph and search applications
- Embed Generative AI capabilities directly into PostgreSQL workflows using the [Azure AI extension](https://learn.microsoft.com/azure/postgresql/flexible-server/how-to-integrate-azure-ai)
- Perform fast, scalable vector similarity searches using [pg_diskann](https://learn.microsoft.com/azure/postgresql/flexible-server/how-to-use-pgdiskann), integrated with [LlamaIndex](https://llamaindex.ai/)
- Design and orchestrate multi-agent systems using LlamaIndex, with persistent memory via [Mem0](https://mem0.ai/) and observability via [Phoenix Arize](https://phoenix.arize.com/)
- Host the solution using [Azure Container Apps](https://learn.microsoft.com/azure/container-apps/overview) to simulate a real-world production deployment
- Use the [Azure Developer CLI](https://learn.microsoft.com/azure/developer/azure-developer-cli/) and templates to provision and deploy applications consistently across teams
- Use [Apache AGE](https://age.apache.org/) along with the Azure AI extension to:
  - Model graph representations of products, reviews, features, and sentiments
  - Run complex Cypher queries to find products based on how users talk about specific features
    (e.g., *“Headphones with positive reviews about noise cancellation”*)

---

## Learning Resources

1. **Azure Database for PostgreSQL – Flexible Server** | [Overview](https://learn.microsoft.com/azure/postgresql/flexible-server/service-overview)
2. **Generative AI with Azure Database for PostgreSQL – Flexible Server** | [Overview](https://learn.microsoft.com/azure/postgresql/flexible-server/generative-ai-overview)
3. **Azure AI extension for PostgreSQL** | [How to integrate Azure AI](https://learn.microsoft.com/azure/postgresql/flexible-server/generative-ai-azure-overview)
4. **Azure AI Foundry** | [Documentation](https://learn.microsoft.com/azure/ai-studio/) · [Architecture](https://learn.microsoft.com/azure/ai-studio/concepts/architecture) · [SDKs](https://learn.microsoft.com/azure/ai-studio/how-to/develop/sdk-overview) · [Evaluation](https://learn.microsoft.com/azure/ai-studio/how-to/evaluate-generative-ai-app)
5. **Azure Container Apps** | [Azure Container Apps](https://learn.microsoft.com/azure/container-apps/) · [Deploy from code](https://learn.microsoft.com/azure/container-apps/quickstart-repo-to-cloud?tabs=bash%2Ccsharp&pivots=with-dockerfile)
6. **Responsible AI** | [Overview](https://www.microsoft.com/ai/responsible-ai) · [With AI Services](https://learn.microsoft.com/azure/ai-services/responsible-use-of-ai-overview?context=%2Fazure%2Fai-studio%2Fcontext%2Fcontext) · [Azure AI Content Safety](https://learn.microsoft.com/azure/ai-services/content-safety/)
7. **pg_diskann** | [Fast vector similarity search](https://learn.microsoft.com/azure/postgresql/flexible-server/how-to-use-pgdiskann)
8. **Apache AGE** | [Graph extension for PostgreSQL](https://age.apache.org/)

