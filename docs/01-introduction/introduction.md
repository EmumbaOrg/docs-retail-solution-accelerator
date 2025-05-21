# Introduction

This solution accelerator is designed as an end-to-end example of a electronic gadgets store AI-enabled application. It demonstrates the implementation of generative AI capabilities to enhance an existing application with multi-agent workflows, vector search, semantic ranking, and GraphRAG on Azure Database for PostgreSQL, and illustrates how users can get personalized recommendations based on their preferences. The app also uses an AI powered open source tool for agent triggering and tracking. The source code for the accelerator is provided in the following repo: https://github.com/Azure-Samples/postgres-agentic-shop

The application has the following architecture:

![AZURE ARCHITECTURE](https://github.com/user-attachments/assets/5b01c4f0-37fa-428b-82f0-6f4ec4440935)

## Learning Objectives

The goal of the solution accelerator is to teach you to how to **work with multi-agent workflows and track them** using Azure Database for PostgreSQL and Azure AI Services to your existing applications. You will gain hands-on experience integrating advanced AI PostgreSQL extensions and Arize Phoenix tool to witness the personalized recommendations an user will experience based on intelligent Azure OpenAI models. By adding a novel chat feature, you will provide the ability for users to gain chat with Azure OpenAI models and explore the electronic gadgets based on their preference and prompts.

By completing the solution accelerator, you will learn to:

- Use Azure AI Services to trigger and track multi-agent workflows.
- Integrate Generative AI capabilities into your Azure Database for PostgreSQL-based applications using the [Azure AI extension](https://learn.microsoft.com/azure/postgresql/flexible-server/how-to-integrate-azure-ai).
- Use [Azure Container Apps](https://aka.ms/azcontainerapps) for deployment <br/> (to get hosted UI and API apps for real-world use).
- Use [Azure Developer CLI](https://aka.ms/azd) with AI Application Templates <br/> (to provision & deploy apps consistently across teams)

## Learning Resources

1. **Azure Database for PostgreSQL - Flexible Server** | [Overview](https://learn.microsoft.com/azure/postgresql/flexible-server/service-overview)
2. **Generative AI with Azure Database for PostgreSQL - Flexible Server** | [Overview](https://learn.microsoft.com/azure/postgresql/flexible-server/generative-ai-overview)
3. **Azure AI extension for PostreSQL** | [How to integrate Azure AI](https://learn.microsoft.com/azure/postgresql/flexible-server/generative-ai-azure-overview)
4. **Azure AI Foundry**  | [Documentation](https://learn.microsoft.com/azure/ai-studio/) · [Architecture](https://learn.microsoft.com/azure/ai-studio/concepts/architecture) · [SDKs](https://learn.microsoft.com/azure/ai-studio/how-to/develop/sdk-overview) ·  [Evaluation](https://learn.microsoft.com/azure/ai-studio/how-to/evaluate-generative-ai-app)
5. **Azure Container Apps**  | [Azure Container Apps](https://learn.microsoft.com/azure/container-apps/) · [Deploy from code](https://learn.microsoft.com/azure/container-apps/quickstart-repo-to-cloud?tabs=bash%2Ccsharp&pivots=with-dockerfile)
6. **Responsible AI**  | [Overview](https://www.microsoft.com/ai/responsible-ai) · [With AI Services](https://learn.microsoft.com/azure/ai-services/responsible-use-of-ai-overview?context=%2Fazure%2Fai-studio%2Fcontext%2Fcontext) · [Azure AI Content Safety](https://learn.microsoft.com/azure/ai-services/content-safety/)