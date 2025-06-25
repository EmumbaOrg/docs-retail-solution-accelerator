# 2.1 Development Environment Setup

To get started, you have two options for setting up your development environment:

- Option 1 **(Recommended)**: Use a Dev Container
- Option 2: Set up a Local Development Environment manually

We strongly recommend using the **Dev Container** approach. Dev Containers provide a consistent, pre-configured environment that eliminates many of the common setup issues—often referred to as the _“it works on my machine”_ problem. This solution accelerator has multiple prerequisites, and the Dev Container automatically installs most of them, reducing manual configuration and the risk of version mismatches.

If you prefer to work outside a container, you can follow the local setup instructions to install each prerequisite on your own operating system. The next sections walk you through both workflows in detail.

> 🔎 Want to learn more about Dev Containers? Check out the [official documentation](https://code.visualstudio.com/docs/devcontainers/containers).

## Option 1: Setup Dev Containers (Recommended)

Using a `Dev Container` will minimize the amount of software you need to install on your local operating system.  The following is the minimum needed to build and run the `dev container`:

![Dev Containers.](https://code.visualstudio.com/assets/docs/devcontainers/containers/architecture-containers.png)
[Image reference](https://code.visualstudio.com/assets/docs/devcontainers/containers/architecture-containers.png)

### Install Software

The required development environment uses a Visual Studio (VS) Code editor with a Python runtime. To complete this lab on your own computer, you must install the following required software. On completing this step, you should have installed:

- [X] Windows Powershell 7.5+ (Only if using Windows)
- [X] WSL 2 and Ubuntu (Only if using Windows)
- [X] Git
- [X] Docker desktop
- [X] Visual Studio Code
- [X] pgAdmin

### Install Windows Powershell 7.5+ (Only if using Windows)

1. Download MSI package for [Windows Powershell](https://learn.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-windows?view=powershell-7.5)

2. Run the package to install Windows Powershell.

3. Once installed, open a command prompt on your machine and verify the installation by running the following:

    ```bash title=""
    pwsh
    ```
    
4. The command prompt should show the version of PowerShell as `7.5.0` or greater.

### Install WSL 2 and Ubuntu (Only if using Windows)

Windows Subsystem for Linux (WSL) is a powerful tool that allows the ability to run Linux based Docker images on the Windows operating system.  Plus, WSL 2 provides advantages to using
Docker Desktop on Windows, such as better memory management for large containers.  WSL 2 is needed because the dev container for this solution accelerator is built on an Ubuntu Linux base image.

1. First we need to install Ubuntu from the Microsoft App Store:

    - Open "Microsoft Store" from your Start Menu
    - Search for "Ubuntu"
    - For the search result titled simply "Ubuntu", select and click "Get" to install
    - Reboot if it asks.

2. Open PowerShell as Admin and run:

    ```powershell title=""
    wsl --install
    ```

3. It will install WSL 2.
4. Reboot if it asks.
5. Test the installation:

    - Opening Terminal in PowerShell shell, and type `wsl'
    - This should load an Ubuntu Linux Shell command line and if you type `ps` you will be a list of processes, this means everything works correctly

### Install Git

Git enables you to manage your code by tracking changes, maintaining a version history, and facilitating collaboration with others. This helps in organizing and maintaining the integrity of your project's development.

1. Download Git from <https://git-scm.com/downloads>.

2. Run the installer using the default options.

### Install Docker Desktop

Docker Desktop is an application that allows you to build, share, and run containerized applications on your local machine. It provides a user-friendly interface to manage Docker containers, images, and networks. By streamlining the containerization process, Docker Desktop helps you develop, test, and deploy applications consistently across different environments.

1. Download and install Docker Desktop for your OS:

    - [Linux](https://docs.docker.com/desktop/setup/install/linux/)
    - [Mac](https://docs.docker.com/desktop/setup/install/mac-install/)
    - [Windows](https://docs.docker.com/desktop/setup/install/windows-install/)

2. Configure Docker Desktop to use WSL 2 based engine (For Windows Only)

    - Open Docker Desktop
    - Click Settings
    - Click General
    - Select `Use the WSL 2 based engine`
    - Click `Apply & restart`

3. Configure Docker Desktop to use WSL 2 integration (For Windows Only)

    - Open Docker Desktop
    - Click Resources
    - Click WSL integration
    - Select `Enable integration with me default WSL distro`
    - Select `Ubuntu`
    - Click `Apply & restart`

### Install Visual Studio Code

Visual Studio Code is a versatile, open-source code editor that combines powerful features with an intuitive interface to help you efficiently write, debug, and customize projects. Note that
the needed extensions will automatically be installed within the `dev container`, so no need to install any additional extensions now.

1. Download and install from <https://code.visualstudio.com/download>.

    - Use the default options in the installer.    

2. After installation completed, launch Visual Studio Code.

3. In the **Extensions** menu, search for and install the following extensions from Microsoft:

    - [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

4. Close VS Code.

### Install pgAdmin

Throughout this workshop, you will use pgAdmin to run queries against your PostgreSQL database.

1. Download pgAdmin from <https://www.pgadmin.org/download/>.

2. Run the installer using the default options.

## Option 2: Setup Local Development Environment

### Install Software

The required local development environment uses a Visual Studio (VS) Code editor with a Python runtime. To complete this lab on your own computer, you must install the following required software. On completing this step, you should have installed:

- [X] Windows Powershell 7.5+ (Only if using Windows)
- [X] Azure command-line tools
- [X] Git
- [X] Python 3.12
- [X] Node.js and npm
- [X] Docker desktop
- [X] Visual Studio Code and required extensions
- [X] pgAdmin

### Install Windows Powershell 7.5+ (Only if using Windows)

1. Download MSI package for [Windows Powershell](https://learn.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-windows?view=powershell-7.5)

2. Run the package to install Windows Powershell.

3. Once installed, open a command prompt on your machine and verify the installation by running the following:

    ```bash title=""
    pwsh
    ```
    
4. The command prompt should show the version of PowerShell as `7.5.0` or greater.

### Install Azure command-line tools

!!! note "In this task, you will install both the Azure CLI and the Azure Developer CLI (`azd`)."

    - The Azure CLI enables you to execute Azure CLI commands from a command prompt or VS Code terminal on your local machine.
    - The Azure Developer CLI (`azd`) is an open-source tool that accelerates provisioning and deploying app resources on Azure.

1. Install or upgrade to the latest version of the [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) by following the instructions for your OS at <https://learn.microsoft.com/cli/azure/install-azure-cli>

    !!! info "Upgrade to latest version of Azure CLI"

        If you already have the Azure CLI installed, you'll need to be sure to upgrade to the latest version. This guide requires v2.69.0 or greater. You can use this command to upgrade to the latest version:

        ```azurecli title=""
        az upgrade
        ```

2. Once installed, open a command prompt on your machine and verify the installation by running the following:

    ```azurecli title=""
    az version
    ```

3. Next, install the `rdbms-connect` extension using the Azure CLI.

    !!! info "About the rdbms-connect extension"

        The `rdbms-connect` extension to the Azure CLI is the enhanced interface for Azure Flexible Server for PostgreSQL. This extension enables command to connect to Azure Database for PostgreSQL flexible server instances and perform required actions.

    To install the `rdbms-connect` extension, execute this command:

    ```azurecli title=""
    az extension add -n rdbms-connect
    ```

4. Install or upgrade Azure Developer CLI to the latest version by following the instructions for your OS at <https://learn.microsoft.com/azure/developer/azure-developer-cli/install-azd>.

    !!! info "Upgrade to latest version of Azure Developer CLI"

        If you already have the Azure Developer CLI installed, you'll need to be sure to upgrade to the latest version. This guide requires v1.16 or greater.

5. Execute the following command from a terminal prompt to verify the tools were installed:

    ```bash title=""
    azd version
    ```

### Install Git

Git enables you to manage your code by tracking changes, maintaining a version history, and facilitating collaboration with others. This helps in organizing and maintaining the integrity of your project's development.

1. Download Git from <https://git-scm.com/downloads>.

2. Run the installer using the default options.

### Install Python

Python is the programming language used to build the backend app for the solution. By utilizing Python's versatile programming capabilities and Azure Database for PostgreSQL's generative AI and vector search capabilities, you can create powerful, efficient and complex multi-agent workflows.

1. Download Python 3.12 from <https://python.org/downloads>.

2. Run the installer using the default options.

3. Use the following command from a terminal prompt to verify Python was installed:

    ```bash title=""
    python --version
    ```

### Install Node.js

Node.js is required to run and manage frontend dependencies and build tools for the React-based UI in this project. It enables commands like `npm install`, `npm run dev`, and others used during development.

1. Download **Node.js v22.x** from [https://nodejs.org/en/download/](https://nodejs.org/en/download/).

2. Run the installer and proceed with the default settings.

### Install Docker Desktop

Docker Desktop is an application that allows you to build, share, and run containerized applications on your local machine. It provides a user-friendly interface to manage Docker containers, images, and networks. By streamlining the containerization process, Docker Desktop helps you develop, test, and deploy applications consistently across different environments.

1. Download and install Docker Desktop for your OS:

    - [Linux](https://docs.docker.com/desktop/setup/install/linux/)
    - [Mac](https://docs.docker.com/desktop/setup/install/mac-install/)
    - [Windows](https://docs.docker.com/desktop/setup/install/windows-install/)

### Install Visual Studio Code (and extensions)

Visual Studio Code is a versatile, open-source code editor that combines powerful features with an intuitive interface to help you efficiently write, debug, and customize projects.

1. Download and install from <https://code.visualstudio.com/download>.

    - Use the default options in the installer.
    - Make sure to check "Add to PATH" during install.

2. After installation completed, launch Visual Studio Code.

3. In the **Extensions** menu, search for and install the following extensions from Microsoft:

    - [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
    - [Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)

4. Close VS Code.

### Install pgAdmin

Throughout this workshop, you will use pgAdmin to run queries against your PostgreSQL database.

1. Download pgAdmin from <https://www.pgadmin.org/download/>.

2. Run the installer using the default options.
