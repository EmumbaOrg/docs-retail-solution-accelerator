# 2.9 Run Application

## Run Apps in Local Environment

There are **two options** for running the backend and frontend applications in your local environment:

1. **Using Command Line:**
   - Start each app manually in separate terminal windows using the provided commands.
2. **Using VS Code Debugger (Recommended):**
   - Use the built-in debugger to launch the backend, frontend, or both apps simultaneously with pre-configured launch settings.

Choose the option that best fits your workflow and follow the corresponding instructions below.

---

We use Arize Phoenix to monitor and gain observability into the workflows and agents, helping you track and debug application behavior effectively.
 
> **Important:** Starting the **Arize Phoenix** container is required before running backend apps locally. (In devcontainers, this starts automatically—see below.)


### 1. Start Arize Phoenix Docker Container

The `Dockerfile` for Arize Phoenix used in this project is very simple. It is based directly on the official Arize Phoenix base image and exposes the required ports for the service:

This means the container runs the standard Arize Phoenix service with no custom modifications, making setup straightforward and reliable.

Open a terminal in VS Code and run:

```bash
cd ../arize-phoenix
# Build the Arize container (only needed once)
docker build -t arize-phoenix .
# Start the Arize container (must be running for the apps to work)
docker run --name arize-phoenix-container -p 6006:6006 arize-phoenix
```
---

### Option 1: Run Apps Using Command Line

- **Backend:**
  ```bash
  cd backend
  uvicorn src.main:app --host 0.0.0.0 --port 8000 --log-config logging_config.yaml --reload
  ```
  > This command starts the FastAPI backend server using Uvicorn with live reload enabled.

- **Frontend:**
  ```bash
  cd frontend
  npm run dev
  ```
  > This command starts the frontend development server using Vite and serves the React app.


You can run these commands in separate terminals to start both apps simultaneously.

---

### Option 2: Run Apps Using VS Code Debugger **Recommended**

You can use the VS Code debugger to start the backend, frontend, or both at once:

1. Open the Run & Debug panel in VS Code (`Ctrl+Shift+D`).
2. Select one of the following configurations from the dropdown:
   - **Launch Backend: FastAPI** – starts the backend (also starts Arize container automatically via preLaunchTask).
   - **Launch Frontend (UI)** – starts the frontend app.
   - **Launch Frontend and Backend** – starts both apps simultaneously.
3. Click the green play button to start debugging.

![debugger-dropdown](../img/debugger-drop-down.png)

> **Tip:** The compound configuration **Launch Frontend and Backend** will run both apps together, each in its own debugger instance.

---

## Running in Devcontainers

When using VS Code Devcontainers:
- The Arize container starts automatically—no manual steps needed.
- Use the VS Code debugger as described above to run backend, frontend, or both (no need to run commands manually).

1. Open the Run & Debug panel (`Ctrl+Shift+D`).
2. Choose the desired configuration (backend, frontend, or both).
3. Start debugging.

> **Note:** Manual Arize container setup is only required for local (non-devcontainer) environments.

