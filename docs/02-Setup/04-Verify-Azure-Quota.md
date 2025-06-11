# 2.4 Verify Azure Quota

This solution requires an appropriate region where Azure OpenAI models are supported and those regions should contain required quota for `GPT-4o` and `text-embedding-ada-002` models. In this section, you should have:

- [X] Verified your Azure OpenAI Models quota
- [X] Authenticated with Azure
- [X] Provisioned Azure resources and deployed the starter solution

## Verify Azure OpenAI Models Quota¶

In this task, you must verify you have available quota for the Azure OpenAI models, and if not, request additional quota.

1. To view your available quota, you first need to retrieve your Microsoft Entra ID **Tenant ID** from the [Azure portal](https://portal.azure.com/).

2. In the Azure portal, enter "Microsoft Entra ID" into the search bar, then select **Microsoft Entra ID** from the **Services** list in the results.

3. On the **Overview** page of your Microsoft Entra ID tenant, select the **Copy to clipboard** button for your **Tenant ID**.