# 2.7 Setup Local Dev Environment

In this step, you will configure your Python development environment in Visual Studio Code. At the end of this step, you should have:

- [X] Created virtual environment
- [x] Installed required libraries
- [X] Create and populated a `.env` files for our apps.
- [X] Ran our apps locally.

## Create a virtual environment

Virtual environments in Python are essential for maintaining a clean and organized development space, allowing individual projects to have their own set of dependencies, isolated from others. This prevents conflicts between different projects and ensures consistency in your development workflow. By using virtual environments, you can manage package versions easily, avoid dependency clashes, and keep your projects running smoothly. It's a best practice that keeps your coding environment stable and dependable, making your development process more efficient and less prone to issues.

1. Return to Visual Studio Code, where you have the **postgres agentic shop** project open.

2. In Visual Studio Code, open a new terminal window and change directories to the `backend` folder of the repo, and create a virtual environment named `.venv` by running the following command at the terminal prompt:

    ```bash title=""
    cd backend
    python -m venv .venv 
    ```

    The above command will create a `.venv` folder under the `api` folder, which will provide a dedicated Python environment for the `api` project that can be used throughout this lab.

3. Activate the virtual environment.

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

4. Execute the command at the terminal prompt to activate your virtual environment.

## Install required libraries

The `pyproject.toml` file in the `backend` folder contains the set of Python libraries needed to run the Python components of the solution accelerator.

!!! tip "Review required libraries"

    Open the `backend/pyproject.toml` file in the repo to review the required libraries and the versions that are being used.

1. From the integrated terminal window in VS Code, run the following commands to install the required libraries in your virtual environment:

    ```bash title=""
    cd backend
    poetry install
    ```

2. From the integrated terminal window in VS Code, change directory into the frontend directory and run the following commands to install the required libraries in your virtual environment:

    ```bash title=""
    cd ../frontend
    npm install
    ```

## Create `.env` file for apps

1. For each of the following directories, create a `.env` file and populate it with the required environment variables:

    - `frontend/.env`
    - `backend/.env`
    - `arize-ai/.env`

    Refer to the project documentation or any provided `.env.example` files for the required variables.

## Run apps in local environment

1. Setup **Arize AI** docker container:

    - In the integrated terminal window in VS Code, navigate to the `arize-phoenix` directory and run the docker build command to build the container.

    ```sh
    cd ../arize-ai
    docker build -t arize-ai .
    ```

    - When the container is built, execute the following command to run the docker container in the integrated terminal window in VS Code.

    ```sh
    docker run --name arize-ai-container -p 6006:6006 arize-ai
    ```

2. Use the following commands to run the apps in the local environment you have setup in previous steps:

    - To run **backend** app, run the following in the integrated terminal window in VS Code

      In the `backend` directory, activate the poetry environment:
      
      ```sh
      uvicorn src.main:app --host 0.0.0.0 --port 8000 --log-config logging_config.yaml --reload
      ```

    - To run **frontend** app, run the following in the integrated terminal window in VS Code

      In the `frontend` directory, start the frontend development server:
      ```sh
      npm run dev
      ```
    
