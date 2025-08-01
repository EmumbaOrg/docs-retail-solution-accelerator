# 2.4 Setup Dev Environment

## Introduction

This section guides you through setting up and configuring the application environment for the solution accelerator. To ensure a consistent and simplified experience across all platforms, we use **Dev Containers** as the exclusive method for running both the backend and frontend applications.

Dev Containers provide a pre-configured, containerized development environment that minimizes manual setup and eliminates version mismatches across systems. You’ll be using Visual Studio Code with Docker to build and open the Dev Container, install required libraries for both Python and Node.js components, and configure essential environment variables.

By following these instructions, you will establish a consistent and fully functional development environment tailored for this solution.

---

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


1. For each of the following directories, navigate in each directory to create a copy of `.env.example` file and rename it `.env` for now. We will populate the required environment variables later in [Section 2.9](./09-Setting-Up-Env-files.md).

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

    From here on out in the documentation, run your commands in the dev container shell.
