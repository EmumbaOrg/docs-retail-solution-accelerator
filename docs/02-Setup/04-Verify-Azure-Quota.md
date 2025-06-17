# 2.4 Verify Azure Quota

This solution requires an appropriate region where Azure OpenAI models are supported and those regions should contain required quota for `GPT-4o` and `text-embedding-ada-002` models. In this section, you should have:

- [X] Verified your Azure OpenAI Models quota

## Request Azure OpenAI Models Quota

In this task, you must request Azure OpenAI models quota in the region that you have selected.

1. To view the [default available quota]((https://learn.microsoft.com/en-us/azure/ai-services/openai/quotas-limits?utm_source=chatgpt.com&tabs=REST#gpt-4o-global-standard)) for Azure OpenAI models in your selected region.

2. To [view and manage current quota](https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/quota?tabs=rest) for Azure OpenAI models available in your selected region.

3. To request additional Azure OpenAI models quota, you must [submit a form](https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/quota?tabs=rest#request-more-quota)

4. Once the quota is approved, you shall receive a confirmation email stating that your quota has been approved. This process may take some time depending on the requests Azure support team receives. 

    !!! failure "Not enough Azure OpenAI models quota"

        If you did not approve your Azure OpenAI models quota prior to sdeployment, you may receive a quota error message similar to the following:

        _(InsufficientQuota) This operation require 150 new capacity in quota Tokens Per Minute (thousands) - gpt-4o GlobalStandard, which is bigger than the current available capacity._
        
	    _(InsufficientQuota) This operation require 120 new capacity in quota Tokens Per Minute (thousands) - text-embedding-ada-002 GlobalStandard, which is bigger than the current available capacity._