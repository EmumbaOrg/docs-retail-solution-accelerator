# 2.4 Setup Dev Environment

## Introduction

This section guides you through the process of setting up and configuring the application environment for the solution accelerator. You have two options for running the backend and frontend applications: using Dev Containers (recommended) or setting up everything locally on your host system. Each option has its own set of steps to follow—choose the approach that best fits your workflow and follow the corresponding instructions in this section.

- **Dev Containers (Recommended):** Provides a consistent, containerized development environment with minimal setup on your local machine.
- **Local Development Environment:** Allows you to install and configure all prerequisites directly on your operating system.

Follow the steps under your chosen setup method to ensure your backend and frontend applications are ready to run. The guide covers starting Docker, building and opening the Dev Container in VS Code, installing required libraries for both Python and Node.js components, and configuring essential environment variables. By following these instructions, you will establish a consistent and functional development environment tailored for this solution.


## Dev Containers (Option 1: **Recommended**)

### Confirm Docker Engine is Running

Before proceeding, ensure that the Docker Engine is running on your system. You can check this by running the following command in your terminal or command prompt:

```bash
docker info
```

If Docker is running, this command will display information about your Docker installation. If Docker is not running, you will see an error message.

#### If Docker is Not Running

Follow the instructions below based on your operating system to start the Docker Engine:

---

**Linux**

If you installed Docker using a package manager, you can start the Docker service with:

```bash
sudo systemctl start docker
```

To enable Docker to start automatically on boot:

```bash
sudo systemctl enable docker
```

---

**Windows**

1. Open the Start menu and search for **Docker Desktop**.
2. Click on **Docker Desktop** to launch it.
3. Wait for the Docker icon to appear in your system tray and indicate that Docker is running.

---

**macOS**

1. Open **Launchpad** and search for **Docker**.
2. Click on **Docker** to launch Docker Desktop.
3. Wait for the Docker whale icon to appear in your menu bar and indicate that Docker is running.

---

After starting Docker, re-run `docker info` to confirm that the Docker Engine

### Create `.env` File for Apps


1. For each of the following directories, navigate in each directory to create and create a copy of `.env.example` file and rename it `.env` for now. We will populate the required environment variables later in [Section 2.9](./09-Setting-Up-Env-files.md).

    !!! danger "Create `.env` files for each directory by following instructions below"

    ```bash
    cp frontend/.env.example frontend/.env
    cp backend/.env.example backend/.env
    cp arize-phoenix/.env.example arize-phoenix/.env
    ```

## Build and Open Dev Container

In this step you will open and build your dev container in VS Code.  After you complete this, you can complete the remaining steps in this page by running all commands inside
your dev container command line, not your local operating system command line.

1. Open VS Code
2. Open Folder of your locally cloned repo
3. Press `Ctrl+Shift+P` to open the command palette
4. Type: `Dev Container` and choose "Rebuild and Reopen in Container"
5. In case the correct python interpreter is not set by default - open the Command Palette (Ctrl+Shift+P or Cmd+Shift+P on macOS), then search for and select "Python: Select Interpreter". From the list, choose the interpreter located in your project's virtual environment (`backend/.venv/bin/python`). 

!!! info "Dev Container Build Process"

    This will kick off a docker build process where your dev container will be built by docker desktop. Let this process run, it may take a few minutes.
    You will see VS Code flash and load into a new project environment. Once the process completes, you can open a new terminal in VS Code. You will notice the shell will look a little different as now you are in an Ubuntu Linux Container.

    From here on out in the documentation, run your commands in the dev container shell.  Except for tools like pgAdmin, you will still run these in your main operating system not the dev container.

## Local Dev Environment (Option 2)

### Install Required Libraries

!!! info "When you open this project in VS Code, you may see a prompt or notification suggesting to open the project in a Dev Container. This happens because a `.devcontainer` configuration is present in the repository. For the Local Development Environment option, **ignore this prompt** and continue with the steps below to set up and run everything directly on your host system."

The `pyproject.toml` file in the `backend` folder contains the set of Python libraries needed to run the Python components of the solution accelerator.

!!! tip "Review required libraries"

    Open the `backend/pyproject.toml` file in the repo to review the required libraries and the versions that are being used.


1. From the integrated terminal window in VS Code, run the following commands to install the required libraries in your virtual environment:

    !!! danger "Create virtual environment and install required libraries for backend"
    ```bash
    cd backend
    poetry config virtualenvs.in-project true --local
    poetry install
    ```

    !!! info "The command `poetry config virtualenvs.in-project true --local` ensures that Poetry creates the virtual environment inside your project directory (as `.venv`), making it easier to manage and locate your environment for this project."

2. Activate the Python virtual environment created by Poetry. Run the following command from the `backend` directory:

    !!! note "Select the appropriate command for your OS and shell from the table."

        | Platform | Shell | Command to activate virtual environment |
        | -------- | ----- | --------------------------------------- |
        | POSIX | bash/zsh | `source .venv/bin/activate` |
        | | fish | `source .venv/bin/activate.fish` |
        | | csh/tcsh | `source .venv/bin/activate.csh` |
        | | pwsh | `.venv/bin/Activate.ps1` |
        | Windows | cmd.exe | `.venv\Scripts\activate.bat` |
        | | PowerShell | `.venv\Scripts\Activate.ps1` |
        | macOS | bash/zsh | `source .venv/bin/activate` |


3. In VS Code, open the Command Palette (Ctrl+Shift+P or Cmd+Shift+P on macOS), then search for and select "Python: Select Interpreter". From the list, choose the interpreter located in your project's virtual environment (`backend/.venv/bin/python`). 

4. From the integrated terminal window in VS Code, change directory into the frontend directory and run the following commands to install the required libraries in your virtual environment:
    
    !!! danger "Install required libraries for frontend project."
    ```bash
    cd ../frontend
    npm install
    ```

    !!! info "`npm install` is a command used in Node.js projects to download and install all the dependencies listed in the package.json file. It sets up the required libraries and modules your project needs to run."

### Create `.env` File for Apps

1. For each of the following directories, navigate in each directory to create and create a copy of `.env.example` file and rename it `.env` for now. We will populate the required environment variables later in [Section 2.9](./09-Setting-Up-Env-files.md).

    !!! danger "Create `.env` files for each directory by following instructions below"
    ```bash
    cp frontend/.env.example frontend/.env
    cp backend/.env.example backend/.env
    cp arize-phoenix/.env.example arize-phoenix/.env
    ```

## Troubleshooting: Permissions Error When Rebuilding Dev Containers on Windows

If you encounter a permissions error while rebuilding the Dev Containers on a Windows machine, it is likely due to file or folder ownership issues created by previous builds. This can prevent the container from accessing or modifying certain directories, such as the Python virtual environment or Node.js dependencies.

### How to Resolve

Follow these steps to resolve the permissions error:

!!! info "Exiting the Dev Container to Work Locally"
    To open your project folder outside of the Dev Container environment:
    
    1. Press `Ctrl+Shift+P` in VS Code to open the Command Palette.
    2. Type and select **"Open Folder Locally"**.

    This will open the folder in a new VS Code window running on your local machine, outside of the Dev Container.
    

1. **Delete the problematic directories:**

    - Remove the Python virtual environment directory from the `backend` folder:
        ```bash
        rm -rf backend/.venv
        ```

    - Remove the Node.js dependencies directory from the `frontend` folder:
        ```bash
        rm -rf frontend/node_modules
        ```

    !!! info "You can delete these folders using File Explorer or the terminal."

2. **Rebuild the Dev Container:**

    - Open VS Code.
    - Open the folder of your locally cloned repository.
    - Press `Ctrl+Shift+P` to open the command palette.
    - Type: `Dev Container` and choose **"Rebuild Container"**.

    !!! info "Dev Container Build Process"
        This will start a Docker build process to rebuild your development container. Wait for the process to complete. Once finished, VS Code will reload into the new container environment.

After completing these steps, the permissions issue should be resolved and you can continue working in your Dev Container as normal.
