# Cleanup Resources

## Clean-up

Once you have completed this guide, delete the Azure resources you created. You are charged for the configured capacity and resource usage. Follow these instructions to delete your resource group and all resources you created for this solution accelerator.

1. In the VS Code integrated terminal, navigate to the root directory of your repository, execute the following command to delete the resources created by the `azd` workflow:

    !!! danger "Execute the following Azure Developer CLI command to delete resources!"

    ```bash title=""
    azd down --purge
    ```

    !!! info "The `--purge` flag purges the resources that provide soft-delete functionality in Azure, including Azure KeyVault and Azure OpenAI. This flag is required to remove all resources completely."

2. You will be shown a list of the resources that will be deleted and prompted about continuing. Enter "y" at the prompt to begin the resource deletion. When the tear down is completed, you shall see a `SUCCESS: Your application was removed from Azure in xx minutes xx seconds.` message at the end.

    ![tear-down](../img/tear-down.png)

## Give us a ⭐️ on GitHub

!!! question "FOUND THIS GUIDE AND SAMPLE USEFUL? MAKE SURE YOU GET UPDATES."

The **[Postgres Agentic Shop](https://github.com/Azure-Samples/postgres-agentic-shop)** sample is an actively updated project that will reflect the latest features and best practices for code-first development of multi-agent workflows on the Azure AI platform. **[Visit the repo](https://github.com/Azure-Samples/postgres-agentic-shop)** or click the button below, to give us a ⭐️.

## Provide Feedback

Have feedback that can help us make this lab better for others? [Open an issue](https://github.com/Azure-Samples/postgres-agentic-shop) and let us know.
