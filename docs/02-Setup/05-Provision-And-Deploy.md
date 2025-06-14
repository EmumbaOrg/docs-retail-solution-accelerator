# 2.5 Provision and Deploy

You will need a valid Azure subscription, a GitHub account, and access to relevant Azure OpenAI models to complete this lab. Review the [prerequisites](./00-Prerequisites.md) section if you need more details. After completing this section, you should have:

- [X] Authenticated with Azure
- [X] Provisioned Azure resources and deployed the AgenticShop solution

## Start Docker Desktop

Docker Desktop is used to create and deploy the containers used for running the _Frontend and Backend_ applications. It must be running before you begin the deployment process using `azd up`.

1. Launch Docker Desktop from the applications menu on your computer.

2. Look for the Docker icon in your system tray or menu bar to confirm it is running.

## Build and Open Dev Container (Only if you chose the Recommended Dev Container Setup Option Previously)

In this step you will open and build your dev container in VS Code.  After you complete this, you can complete the remaining steps in this page by running all commands inside
your dev container command line, not your local operating system command line.

1. Open VS Code
2. Open Folder of your locally cloned repo
3. Press `Ctrl+Shift+P` to open the command palette
4. Type: `Dev Container` and choose "Rebuild and Reopen in Container"

!!! info "Dev Container Build Process"

    This will kick off a docker build process where your dev container will be built by docker desktop.  Let this process run, it may take a few minutes.
    You will see VS Code flash and load into a new project environment.  Once the process completes, you can open a new terminal in VS Code.  You will notice the shell will
    look a little different as now you are in an Ubuntu Linux Container.

    From here on out in the documentation, run your commands in the dev container shell.  Except for tools like pgAdmin, you will
    still run these in your main operating system not the dev container.

## Authenticate With Azure

Before running the `azd up` command, you must authenticate your VS Code environment to Azure.

To create Azure resources, you need to be authenticated from VS Code. Open a new integrated terminal in VS Code. Then, complete the following steps:

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

2. Wait for the process to complete. Depending on the option you selected for the Azure Container Apps, the deployment will take different amounts of time:
    - If you chose Azure Container Apps deployment, it will roughly take 20 minutes.
    - If you chose not to deploy Azure Container Apps, it will roughly take 10 minutes.

    !!! failure "Not enough Azure OpenAI models quota"

        If you did not check your Azure OpenAI models quota prior to starting running the `azd up` command, you may receive a quota error message similar to the following:

        _(InsufficientQuota) This operation require 150 new capacity in quota Tokens Per Minute (thousands) - gpt-4o GlobalStandard, which is bigger than the current available capacity._
        
	    _(InsufficientQuota) This operation require 120 new capacity in quota Tokens Per Minute (thousands) - text-embedding-ada-002 Standard, which is bigger than the current available capacity._

    !!! failure "Deployment failed: Postgresql server is not in an accessible state"

        It's possible a `server is not in an accessible state` error may occur when the Azure Bicep deployment attempts to add the PostgreSQL Admin User after the PostgreSQL Server has been provisioned. This can occur if the PostgreSQL server is still being provisioned in the Azure backend, but the Deployment returned that it's successful already. If you encounter this error, simply re-run the `azd up` command.

        ```bash title=""
        ERROR: error executing step command 'provision': deployment failed: error deploying infrastructure: deploying to subscription:

        Deployment Error Details:
        AadAuthOperationCannotBePerformedWhenServerIsNotAccessible: The server 'dev-cvdjta5pfnc5e' is not in an accessible state to perform Azure AD Principal operation. Please make sure the server is accessible before executing Azure AD Principal operations.
        ```

3. On successful completion you will see a `SUCCESS: ...` message on the console.