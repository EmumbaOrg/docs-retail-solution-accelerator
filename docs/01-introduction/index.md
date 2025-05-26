# Introduction

This solution accelerator is designed as an example of an e-commerce store. It demonstrates the implementation of generative AI capabilities using a multi-agent workflow with LlamaIndex, Apache AGE and Azure AI and DiskANN on Azure Database for PostgreSQL (Flexible Server). The app uses a small sample dataset made up of products, its features, reviews and user’s profiles. The source code for the accelerator is provided in the repo: [Agentic Shop](https://github.com/Azure-Samples/postgres-agentic-shop) 

The application has the following architecture:

![AZURE ARCHITECTURE](https://github.com/user-attachments/assets/f3ca0e0d-0c93-4c0f-be5d-958eace3a138)

## Learning Objectives

This solution accelerator will equip you with the skills to enhance your applications with powerful AI capabilities using Azure Database for PostgreSQL and Azure AI Services. You’ll gain hands-on experience integrating advanced AI-powered extraction into the data ingestion process.

By incorporating Apache AGE, you’ll learn how to model and store product-review relationships as a graph—capturing features, sentiments, and user interactions. Using Azure OpenAI, you’ll extract structured product attributes and sentiment insights, enabling rich graph construction and support for complex search queries like “headphones with positive reviews about noise cancellation” that include both Vector and cypher queries.

You’ll explore pg_diskann to perform fast and scalable vector similarity searches within PostgreSQL, enabling real-time product and review retrieval.

The workshop also demonstrates how to design and orchestrate multi-agent workflows using LlamaIndex, leveraging diverse strategies such as Tool-using agents, Text-to-SQL, and Retrieval-Augmented Generation (RAG). You’ll also learn how to incorporate persistent agent memory using Mem0, allowing agents to retain user-specific context across interactions.
To ensure transparency and debuggability, the solution includes end-to-end observability using Phoenix (by Arize), enabling tracing, span-level inspection, and performance monitoring of each agent’s execution within the workflow.

By completing the solution accelerator, you will learn to:

- Leverage **Azure OpenAI** to extract product features and sentiment for use in downstream graph and search applications.
- Use the Azure AI extension for PostgreSQL to embed Generative AI capabilities directly into your database workflows.
- Perform fast, scalable vector similarity searches with **pg_diskann**, integrated into **LlamaIndex** workflows for semantic retrieval.
- Design and orchestrate **multi-agent systems** using **LlamaIndex**, including memory-enabled agents via **Mem0** and observability with **Phoenix (Arize)** for full workflow transparency.
- Deploy your solution using **Azure Container Apps** to host both the UI and API components in a real-world environment.
- Use **Azure Developer CLI (azd)** and **AI application templates** to provision and deploy applications consistently across teams.

## Learning Resources

1. **Azure Database for PostgreSQL - Flexible Server** | [Overview](https://learn.microsoft.com/azure/postgresql/flexible-server/service-overview)
2. **Generative AI with Azure Database for PostgreSQL - Flexible Server** | [Overview](https://learn.microsoft.com/azure/postgresql/flexible-server/generative-ai-overview)
3. **Azure AI extension for PostreSQL** | [How to integrate Azure AI](https://learn.microsoft.com/azure/postgresql/flexible-server/generative-ai-azure-overview)
4. **Azure AI Foundry**  | [Documentation](https://learn.microsoft.com/azure/ai-studio/) · [Architecture](https://learn.microsoft.com/azure/ai-studio/concepts/architecture) · [SDKs](https://learn.microsoft.com/azure/ai-studio/how-to/develop/sdk-overview) ·  [Evaluation](https://learn.microsoft.com/azure/ai-studio/how-to/evaluate-generative-ai-app)
5. **Azure Container Apps**  | [Azure Container Apps](https://learn.microsoft.com/azure/container-apps/) · [Deploy from code](https://learn.microsoft.com/azure/container-apps/quickstart-repo-to-cloud?tabs=bash%2Ccsharp&pivots=with-dockerfile)
6. **Responsible AI**  | [Overview](https://www.microsoft.com/ai/responsible-ai) · [With AI Services](https://learn.microsoft.com/azure/ai-services/responsible-use-of-ai-overview?context=%2Fazure%2Fai-studio%2Fcontext%2Fcontext) · [Azure AI Content Safety](https://learn.microsoft.com/azure/ai-services/content-safety/)