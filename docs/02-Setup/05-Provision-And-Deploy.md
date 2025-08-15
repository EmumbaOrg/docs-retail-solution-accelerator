# 2.5 Provision and Deploy

You will need a valid Azure subscription and access to relevant Azure OpenAI models to complete this lab. Review the [prerequisites](./01-Prerequisites.md) section if you need more details. After completing this section, you should have:

- [X] Authenticated with Azure
- [X] Provisioned Azure resources for AgenticShop solution

## Authenticate With Azure

Before running the `azd up` command, you must authenticate your VS Code environment to Azure.

To create Azure resources, you need to be authenticated from VS Code. Open a new integrated terminal in VS Code. Then, complete the following steps:

<!-- markdownlint-disable MD046 -->
!!! info "Visual Studio Code Integrated terminal"

    The integrated terminal in Visual Studio Code is a built-in console panel that runs your system’s shell such as Bash, PowerShell etc. within the editor. It lets you execute commands without switching windows, and enhances workflow with extensions, persistent sessions, and context-aware shell integration
    To open an integrated terminal, you can use this keyboard shortcut "Ctrl + `" or click View in the Menu Bar → Click Terminal

### Authenticate with `az` for post-provisioning tasks

1. Log into the Azure CLI `az` using the command below.

    ```bash
    az login
    ```

2. Complete the login process in the browser window that opens.

    !!! info "If you have more than one Azure subscription, you may need to run `az account set -s <subscription-id>` to specify the correct subscription to use."

### Authenticate with `azd` for provisioning & managing resources

1. Log in to Azure Developer CLI. This is only required once per-install.

    ```bash
    azd auth login
    ```

    !!! info "Alternative if above command fails"

    If the above command fails, use the following flag which will provide a code in the terminal. Copy this code for later use.
    On pressing enter, a window will open where paste the code you previously copied and you shall be able to login.

    ```bash
    azd auth login --use-device-code
    ```

    !!! info "Difference between az CLI and azd CLI"

        az CLI is a granular control-plane tool for managing Azure resources such as VMs, networking, storage, etc. and is used when you need fine‑grained control over infrastructure configuration.

        azd CLI is a higher-level, developer-focused workflow tool that scaffolds, provisions, deploys, and monitors entire applications to automates the full app lifecycle, together with IaC, CI/CD, and monitoring so you don't handcraft each resource individually.

## PostgreSQL Admin Credentials (auto-generated)

> **NOTE:** This solution uses PostgreSQL username/password authentication. During deployment (`azd up`), the admin **username** and **password** are **auto-generated** and written to your project’s `.env` file.

!!! warning "Do not commit secrets"
    Keep your `.env` file **out of version control**.

1. Deploy the infrastructure with `azd up`. When it completes, open the generated `.env` file in your project to view the admin credentials.

2. Use the values from `.env` whenever you need to connect to the database locally (e.g., via a client or CLI). Treat these as **secrets**.

**Example `.env` entries (values will differ):**

```dotenv
DB_USER=rtadmindm2wsm
DB_PASSWORD=Aa1_161a08227c7145dd9e5441741cc776a0
```

## Provision Azure Resource Without Apps Deployment

You are now ready to provision your Azure resources without deployment of AgenticShop apps. You shall be directed later in the workshop to deploy apps on Azure infrastructure.

1. Use `azd up` to provision your Azure infrastructure and skip deployment of apps on Azure infrastructure.

    ```bash
    azd up
    ```

    !!! info "You will be prompted for several inputs for the `azd up` command:"

        - **Enter a new environment name**: Enter a value, such as `dev`.
        - The environment for the `azd up` command ensures configuration files, environment variables, and resources are provisioned and deployed correctly.
        - **Select an Azure Subscription to use**: Select the Azure subscription you are using for this workshop using the up and down arrow keys.
        - **Select two Azure locations to use**: Ensure both selected regions are same. 
            - Select the Azure region into which resources should be deployed using the up and down arrow keys.
            - Select the Azure region into which Azure OpenAI models should be deployed using the up and down arrow keys.        
        - **Enter a value for the `resourceGroupName`**: Enter `rg-dev`, or a similar name.

    !!! info "Pre-deployment Validation Checks"

        Before the `azd` workflow proceeds, checks are performed in the selected region and recommendations are generated on failure for following cases to ensure that the deployment is successful:
        
        - Azure Flexible Server for PostgreSQL SKU
        - Azure Container Apps quota
        - azd env name    

2. **Input your choice for the Azure Container Apps deployment**: Enter `no` to skip Azure Container Apps deployment and press enter.

    ```bash
    Do you want to deploy Azure Container Apps? (y/n): no
    ```

    - Selecting `no` will deploy only the Azure OpenAI models and the database server in the resource group while frontend, backend and arize Azure Container Apps will not be deployed.

    - Selecting `yes` here will deploy the solution with default settings on Azure including the Azure Container Apps. This is not recommended at this stage. We shall deploy our apps on Azure later in this workshop.

3. Wait for the process to complete. Depending on the option you selected for the Azure Container Apps, the deployment will take different amounts of time:

    - If you chose Azure Container Apps deployment, it will roughly take 20 minutes.

    - If you chose not to deploy Azure Container Apps, it will roughly take 10 minutes.

    !!! info "Provisioned Resources"

        When you execute the `azd up` command, following resources will be provisioned in your subscription. You can view these resources in the resource group you have provisioned.

        | Service Name                          | 
        | ------------------------------------- | 
        | Azure Flexible server for PostgreSQL  |
        | Azure OpenAI Service                  |

4. On successful completion you will see a `SUCCESS: Your up workflow to provision and deploy to Azure completed in xx minutes xx seconds.` message on the console.

## Troubleshooting Errors

### Errors During azd Workflow

1. **Error: "Deployment failed: The resource entity provisioning state is not terminal"**

    If your deployment faces an error like such as "The resource entity provisioning state is not terminal", try running your deployment again using `azd up`.

    ```
    ERROR: error executing step command 'provision': deployment failed: error deploying infrastructure: deploying to resource group: Deployment Error Details: RequestConflict: Cannot modify resource with id '/subscriptions/{sub-id}/resourceGroups/{rg-name}/providers/Microsoft.CognitiveServices/accounts/{Resourcename}' because the resource entity provisioning state is not terminal. Please wait for the provisioning state to become terminal and then retry the request.
    ```

2. **Error: "Validation Error"**

    If your deployment failed with an error such as validation error, you must name the `azd` env with [rules and restrictions](https://learn.microsoft.com/azure/azure-resource-manager/management/resource-name-rules) for naming conventions. To fix this error, you can create a new `azd` env with `azd env new` with supported naming convention and proceed with the deployment steps mentioned in previous section.

    ```
    InvalidTemplateDeployment: The template deployment 'dev' is not valid according to the validation procedure.
    ```

3. **Error: "Network Issues"**

    At any point during provisioning of resources, the deployment can fail due to transient errors or network issues. For such occurrences, you can take either of following actions:

    - Restart the deployment with `azd up` command.

    - If the above fails, you can delete the existing deployment with `azd down --purge`, create a new `azd` env with `azd env new` and follow the deployment steps from previous section.

### Destroy Old Deployment and Create New

1. If your deployment failed and you want to create a new deployment, you must first purge the previous deployment using `azd down --purge` command before creating a new deployment with a new `azd` env. To create new `azd` env, you must execute `azd env new` command and input the name for the new `azd` env.

2. You might run into following error when executing `azd down --purge` command when you have not approved quota before or have not approved yet:

    !!! danger "No Deployment Found!"

        ERROR: deleting infrastructure: error deleting Azure resources: finding completed deployments: 'dev': no deployments found.

    To resolve this error, you must either execute the following `az` CLI command or delete that specific resource group from Azure portal. Executing the command will ask you for a confirmation to delete resource group. Input `y` to confirm deletion. Redeploy the env using `azd up` command.

    ```bash
    az group delete --name <resource-group-name>
    Are you sure you want to perform this operation? (y/n): y

    azd up
    ```

3. If you intend to create a new deployment after destroying the resources, you can perform any one of the following operations:

    - You can delete the folder of that env in the `.azure` folder in root directory that you created before and run the `azd up` command again to create a new deployment.

    - You can execute the following command to create a new deployment and run the `azd up` command for new deployment.

    ```bash
    azd env new

    azd up
    ```
