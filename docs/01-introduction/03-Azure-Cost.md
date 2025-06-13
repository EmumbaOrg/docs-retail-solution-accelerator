# 1.3 Azure Cost Estimate

The Microsoft Azure resources you deploy will be provisioned within your Azure Subscription. You are responsible for the cost of those services. The cost of the solution will vary depending on the Azure region selected and deployment options you choose.

The Setup section of this guide will tell you how to choose the deployment options.

!!! note "Review the estimated costs of deployed resources."

    Here's a breakdown of the _estimated costs_ of Azure resources deployed for this solution:

    | Service Name                          | Cost per day ($) |
    | ------------------------------------- | ---------------- |
    | Azure Flexible server for PostgreSQL  |            ~4.31 |
    | Azure Container Registry              |           ~0.167 |
    | Azure Keyvault                        |            ~0.18 |
    | Azure Container Apps                  |            ~4.57 |
    | Azure OpenAI Service                  |    Pay-as-you-go |

To summarize the estimated monthly cost:

- Per day cost for Azure deployment: ~$9.227 excluding OpenAI models costs
- Per day cost for local deployment: ~$4.31 excluding OpenAI models costs

!!! warning "The above costs are only estimates."

    The costs provided here are estimates based on running the solution accelerator using the provided configuration and are intended to provide general guidance about the costs associated with running the solution accelerator. Depending on deployment options, region selection, and requests generated, individual costs will vary.
