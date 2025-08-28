# 2.1 Prerequisites

Review the details about what is needed before following this guide, what you are expected to know beforehand and what you can expect to learn after going through it.

## What You Need

To follow this guide, you will need:

1. **Your own computer.**
    - Any computer capable of running Visual Studio Code, Docker Desktop, and a modern web browser will do.
    - You must have the ability to install software on the computer.
    - We recommend installing a recent version of Edge, Chrome, or Safari.

2. **An Azure Subscription.**
    - This is needed to provision the Azure infrastructure for your AI project.
    - If you don't have an Azure account, [sign up for a free one](https://azure.microsoft.com/en-gb/pricing/purchase-options/azure-account) now. (It takes just a few minutes.)

3. **User Permissions for Deployment.**
    - In order to deploy AgenticShop successfully in your subscription, following roles must be attached to the user deploying the solution:

        - Contributor
        - Role Based Access Control Administrator

4. **Identify a suitable Azure region for your OpenAI models.**

    To successfully deploy and run the solution accelerator with Generative AI capabilities, you must ensure that your Azure subscription has access to the necessary Azure OpenAI models with sufficient quota in at least one supported region.

    - This solution uses following Azure OpenAI models:
        - `gpt-4o` with a minimum quota requirement of **50K TPM** (`GlobalStandard`)
        - `text-embedding-3-small` with a minimum quota requirement of **70K TPM** (`GlobalStandard`)

    - **Before proceeding**, identify an Azure region where both models are available and you have the required quota. Refer to the following documentation for regional availability:
        - [gpt-4o model availability](https://learn.microsoft.com/azure/ai-services/openai/concepts/models?tabs=global-standard%2Cstandard-chat-completions#standard-models-by-endpoint)
        - [text-embedding-3-small model availability](https://learn.microsoft.com/azure/ai-services/openai/concepts/models?tabs=global-standard%2Cstandard-embeddings#standard-models-by-endpoint)

    - You can check your current quota and request increases using the steps outlined [here](https://learn.microsoft.com/azure/ai-services/openai/how-to/quota?tabs=rest#view-and-request-quota).

    !!! info "Automatic region filtering in `azd` workflow"

        When you run `azd up`, the tool automatically filters and displays only those regions that meet the above requirements — i.e., regions where both models are available **and** the corresponding quota is sufficient.

5. **Select appropriate Azure region for your infrastructure resources.**

    Before selecting an Azure region for infrastructure, you must ensure that the region you have selected supports following Azure services:
        - `Standard_D2ds_v4` Azure Flexible server for PostgreSQL (`General Purpose`)
        - Resource Group
        - Azure Key vault
        - Azure Container Environment and Azure Container Apps
        - Azure Container Registry

## What You Should Know

To get the most of this solution accelerator, you should have:

### Recommended knowledge and experience

1. **Familiarity with Visual Studio Code**
    - The default editor used in this guide is Visual Studio Code. You will configure your VS Code development environment with the required extensions and code libraries.
    - The guide requires Visual Studio Code and other tools to be installed on your computer. You will be running the solution code from your local computer.
2. **Familiarity with the Azure portal**
    - The guide assumes you are familiar with navigating to resources within the Azure portal.
    - You will use the Azure portal to retrieve endpoints, keys, and other values associated with the resources described in this guide.
3. **Familiarity with PostgreSQL**
    - The guide assumes you are familiar with basic SQL syntax.

### Preferred knowledge and experience

1. **Familiarity with `git` operations**
    - You will be cloning the sample repository from Github.
2. **Familiarity with the `bash` shell**.
    - If needed, you will use `bash` in the VS Code terminal to run post-provisioning scripts.
    - You will also use it to run Azure CLI and Azure Developer CLI commands during setup.
3. **Familiarity with Python frameworks**.
    - This guide provides an overview of the solution, including relevant Python code components.

## What You Will Take Away

After going through this guide, you will have:

1. An understanding of the Azure portal and relevant developer tools (e.g., Azure Developer CLI, FastAPI) to streamline end-to-end development workflows for your own AI apps.

2. An understanding of how Azure AI services can be integrated into applications to create powerful AI-enabled applications.
