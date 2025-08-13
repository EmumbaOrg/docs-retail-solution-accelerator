# 2.2 Development Environment Setup

To simplify setup and ensure consistency, this solution accelerator uses a **Dev Container** as the exclusive development environment.

Dev Containers provide a clean, pre-configured workspace with all necessary tools and dependencies already installed. Whether you're on Windows, macOS, or Linux, using Dev Containers helps you avoid common setup issues and ensures that everyone in the workshop is working in the same environment.

## Why Dev Containers?

Using Dev Containers offers several benefits:

- ✅ **Consistent Development Environment**: Avoid the “it works on my machine” problem by running the same stack across all participants and contributors.
- ⚡ **Quick Setup**: All required dependencies (Python, PostgreSQL tools, CLI utilities, etc.) are pre-installed and version-controlled.
- 🔄 **Isolated Workspace**: Changes you make inside the container don’t affect your global system setup.
- 🔧 **Reproducibility**: Easily reproduce the environment in CI/CD pipelines or across teams.
- 🧪 **Great for Experimentation**: Break things without consequences — just rebuild the container!

Want to understand more about what Dev Containers are and how they work? Check out this short [YouTube video by Visual Studio Code](https://www.youtube.com/watch?v=b1RavPr_878&ab_channel=VisualStudioCode) (7 mins).

📘 For deeper documentation, visit the [official Dev Container guide](https://code.visualstudio.com/docs/devcontainers/containers).

## Install Prerequisites

To run the Dev Container successfully, you’ll need to install a few tools on your local machine. Once installed, you’ll be able to open the project in VS Code and let it configure everything automatically inside the container.

> 🛠️ **Minimum Setup**  
> You only need to install the following system-level tools — the rest will be handled by the Dev Container itself:

### Install Software

The required development environment uses a Visual Studio (VS) Code editor with a Python runtime. To complete this lab on your own computer, you must install the following required software. On completing this step, you should have installed:

- [X] Git
- [X] Docker desktop
- [X] Visual Studio Code

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

### Install Visual Studio Code

Visual Studio Code is a versatile, open-source code editor that combines powerful features with an intuitive interface to help you efficiently write, debug, and customize projects. Note that
the needed extensions will automatically be installed within the `dev container`, so no need to install any additional extensions now.

1. Download and install from <https://code.visualstudio.com/download>.

    - Use the default options in the installer.

2. After installation is completed, launch Visual Studio Code.

3. In the **Extensions** menu, search for and install the following extensions from Microsoft:

    - [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

4. Close VS Code.

## ✅ You're Ready to Launch

Once you’ve installed everything above, you can move to the next step where you’ll clone the repo and then open the cloned repo in VS Code, and it will **automatically prompt you to reopen in a Dev Container**.

From there, VS Code will take care of building the container and installing all required tools inside it.

Let’s get started!
