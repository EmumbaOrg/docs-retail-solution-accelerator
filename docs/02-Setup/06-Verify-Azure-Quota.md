# 2.6 Verify Azure Quota

This solution requires an appropriate region where Azure OpenAI models are supported and those regions should contain required quota for `GPT-4o` and `text-embedding-ada-002` models. In this section, you should have:

- [X] Verified your Azure OpenAI Models quota

## Request Azure OpenAI Models Quota

In this task, you will request quota for Azure OpenAI models in the region you have selected.

1. Review the [default available quota](https://learn.microsoft.com/en-us/azure/ai-services/openai/quotas-limits?utm_source=chatgpt.com&tabs=REST#gpt-4o-global-standard) for Azure OpenAI models in your chosen region.

2. [View and manage your current quota](https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/quota?tabs=rest) through the Azure portal.

3. If additional capacity is needed, [submit a quota increase request](https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/quota?tabs=rest#request-more-quota).

4. Once your request is approved, you will receive a confirmation email.
   Please note that approval times may vary depending on Azure Support’s current workload.

!!! failure "Not enough Azure OpenAI models quota"

    If your quota was not approved before deployment, you may encounter an error similar to the following:

    _(InsufficientQuota) This operation requires 150 new capacity in quota Tokens Per Minute (thousands) - gpt-4o GlobalStandard, which exceeds the current available capacity._

    _(InsufficientQuota) This operation requires 120 new capacity in quota Tokens Per Minute (thousands) - text-embedding-ada-002 GlobalStandard, which exceeds the current available capacity._

