# 2.5 Provision and Deploy

You will need a valid Azure subscription, a GitHub account, and access to relevant Azure OpenAI models to complete this lab. Review the [prerequisites](./00-Prerequisites.md) section if you need more details. After completing this section, you should have:

- [X] Authenticated with Azure
- [X] Provisioned Azure resources and deployed the AgenticShop solution

## Authenticate With Azure

Before running the `azd up` command, you must authenticate your VS Code environment to Azure.

To create Azure resources, you need to be authenticated from VS Code. Open a new integrated terminal in VS Code. Then, complete the following steps:

!!! info "Visual Studio Code Integrated terminal"

    The integrated terminal in Visual Studio Code is a built-in console panel that runs your system’s shell such as Bash, PowerShell etc. within the editor. It lets you execute commands without switching windows, and enhances workflow with extensions, persistent sessions, and context-aware shell integration
    To open an integrated terminal, you can use this keyboard shortcut "Ctrl + `" or click View in the Menu Bar → Click Terminal

### Authenticate with `az` for post-provisioning tasks

1. Log into the Azure CLI `az` using the command below.

    ```bash  title=""
    az login
    ```

2. Complete the login process in the browser window that opens.

    !!! info "If you have more than one Azure subscription, you may need to run `az account set -s <subscription-id>` to specify the correct subscription to use."

### Authenticate with `azd` for provisioning & managing resources

1. Log in to Azure Developer CLI. This is only required once per-install.

    ```bash title=""
    azd auth login
    ```

    !!! info "Alternative if above command fails"

        If the above command fails, use the following flag which will provide a code in the terminal. Copy this code for later use.
        On pressing enter, a window will open where paste the code you previously copied and you shall be able to login.

        ```bash title=""
        azd auth login --use-device-code
        ```

    !!! info "Difference between az CLI and azd CLI"

        az CLI is a granular control-plane tool for managing Azure resources such as VMs, networking, storage, etc. and is used when you need fine‑grained control over infrastructure configuration.

        azd CLI is a higher-level, developer-focused workflow tool that scaffolds, provisions, deploys, and monitors entire applications to automates the full app lifecycle, together with IaC, CI/CD, and monitoring so you don't handcraft each resource individually.

## Change PostgreSQL Authentication Credentials

You should change the default postgres authentication credentials. This serves as a best security practice to avoid unauthorised access and credentials leakage of PostgreSQL database.

1. In the root directory of your project, change directory into `infra` directory and edit the `main.parameters.json` file.

2. Edit the following parameters with your own username and password. You can refer to this file anytime you want to connect to the database to perform any action.

    ```bash title=""
    ADMINISTRATOR_LOGIN_USER=rtbackend
    ADMINISTRATOR_LOGIN_PASSWORD=Agent2shop
    ```
    
    !!! danger "Only for workshop purposes"

        The credentials shown above are only for workshop or learning purposes. You must change these parameters as best security practice if you intend to deploy this solution in production.

## Provision Azure Resource Without Apps Deployment

You are now ready to provision your Azure resources without deployment of AgenticShop apps. You shall be directed later in the workshop to deploy apps on Azure infrastructure.

1. Use `azd up` to provision your Azure infrastructure and skip deployment of apps on Azure infrastructure.

    ```bash title=""
    azd up
    ```

    !!! info "You will be prompted for several inputs for the `azd up` command:"

        - **Enter a new environment name**: Enter a value, such as `dev`.
        - The environment for the `azd up` command ensures configuration files, environment variables, and resources are provisioned and deployed correctly.
        - Should you need to delete the `azd` environment, locate and delete the `.azure` folder at the root of the project in the VS Code Explorer.
        - **Select an Azure Subscription to use**: Select the Azure subscription you are using for this workshop using the up and down arrow keys.
        - **Select two Azure locations to use**: Ensure both selected regions are same. 
            - Select the Azure region into which resources should be deployed using the up and down arrow keys.
            - Select the Azure region into which Azure OpenAI models should be deployed using the up and down arrow keys.        
        - **Enter a value for the `resourceGroupName`**: Enter `rg-dev`, or a similar name.

2. **Input your choice for the Azure Container Apps deployment**: Enter `no` to skip Azure Container Apps deployment and press enter.

   ```bash title=""
    Do you want to deploy Azure Container Apps? (y/n): no
    ```
   - Selecting `no` will deploy only the azure_ai and the database server in the resource-group --- the frontend, backend and arize container apps will not be deployed.
   - Selecting `yes` here will deploy all container apps in the resource-group.

2. Wait for the process to complete. Depending on the option you selected for the Azure Container Apps, the deployment will take different amounts of time:
    - If you chose Azure Container Apps deployment, it will roughly take 20 minutes.
    - If you chose not to deploy Azure Container Apps, it will roughly take 10 minutes.

    !!! info "Provisioned Resources"

        When you execute the `azd up` command, following resources will be provisioned in your subscription. You can view these resources in the resource group you have provisioned.

        | Service Name                          | 
        | ------------------------------------- | 
        | Azure Flexible server for PostgreSQL  |
        | Azure OpenAI Service                  |

    !!! failure "Not enough Azure OpenAI models quota"

        If you did not check your Azure OpenAI models quota prior to starting running the `azd up` command, you may receive a quota error message similar to the following:

        _(InsufficientQuota) This operation require 150 new capacity in quota Tokens Per Minute (thousands) - gpt-4o GlobalStandard, which is bigger than the current available capacity._
        
	    _(InsufficientQuota) This operation require 120 new capacity in quota Tokens Per Minute (thousands) - text-embedding-ada-002 GlobalStandard, which is bigger than the current available capacity._

    !!! failure "Deployment failed: The resource entity provisioning state is not terminal"

        It's possible that when Azure Bicep deployment attempts to create resources, error may occur when the soft deleted resources are in process to be purged from Azure backend. If you encounter this error, simply re-run the `azd up` command.

3. On successful completion you will see a `SUCCESS: Your application was removed from Azure in xx minutes xx seconds.` message on the console.

## Troubleshooting Errors
 
### Continue with Current Deployment

1. If your deployment failed with an error such as validation error, you can re-run the `azd up` command to restart the deployment of the same `azd` env that you have created before.

    !!! danger "Validation Error"

        InvalidTemplateDeployment: The template deployment 'dev' is not valid according to the validation procedure.

2. If your deployment has failed due to region or quota availability and you want to continue with current deployment in another region, you must purge the existing deployment using `azd down --purge` command before proceeding with another deployment. To set the new region for Azure OpenAI models in current `azd` deployment, you can use the following command:

    ```bash title=""
    azd env set AZURE_OPENAI_LOCATION <region>
    ```

    !!! warning "Soft Delete Resources consume quota"

        If the resource destruction does not complete, interrupted or `--purge` flag is not used, the resources may still exist in the your subscription as soft deleted resources which can be seen from Azure portal such as deleted OpenAI models in `Manage deleted resources`, then these resources will consume OpenAI quota hindering you from creating new deployment. It is very important to permanently delete these resources either from Azure portal or through azure CLI before proceeding with another deployment. 

        ```bash title=""
        az cognitiveservices account purge --location <region> --resource-group <resource-group> --name <openai-resource-name> 
        ```

### Destroy Old Deployment and Create New

1. If your deployment failed and you want to create a new deployment, you must first purge the previous deployment using `azd down --purge` command before creating a new deployment with a new `azd` env. To create new `azd` env, you must execute `azd env new` command and input the name for the new `azd` env.

2. You might run into following error when executing `azd down --purge` command when you have not approved quota before or have not approved yet:

    !!! danger "No Deployment Found!"

        ERROR: deleting infrastructure: error deleting Azure resources: finding completed deployments: 'dev': no deployments found.
    
    To resolve this error, you must either execute the following `az` CLI command or delete that specific resource group from Azure portal. Executing the command will ask you for a confirmation to delete resource group. Input `y` to confirm deletion. Redeploy the env using `azd up` command.

    ```bash title=""
    az group delete --name <resource-group-name>
    Are you sure you want to perform this operation? (y/n): y

    azd up
    ```

3. If you intend to create a new deployment after destroying the resources, you can perform any one of the following operations:

    - You can delete the folder of that env in the `.azure` folder in root directory that you created before and run the `azd up` command again to create a new deployment.

    - You can execute the following command to create a new deployment and run the `azd up` command for new deployment.

    ```bash title=""
    azd env new

    azd up
    ```
