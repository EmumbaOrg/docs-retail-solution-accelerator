# 2.9 Run Application

## Run Apps in Local Environment

> **Important:** Starting the **Arize AI** container is required before running backend or frontend apps locally. (In devcontainers, this starts automatically—see below.)

### 1. Start Arize AI Docker Container

Open a terminal in VS Code and run:

```bash
cd ../arize-ai
# Build the Arize container (only needed once)
docker build -t arize-ai .
# Start the Arize container (must be running for the apps to work)
docker run --name arize-ai-container -p 6006:6006 arize-ai
```

### 2. Run Backend and Frontend Apps (Command Line)

- **Backend:**
  ```bash
  cd backend
  uvicorn src.main:app --host 0.0.0.0 --port 8000 --log-config logging_config.yaml --reload
  ```

- **Frontend:**
  ```bash
  cd frontend
  npm run dev
  ```

You can run these commands in separate terminals to start both apps simultaneously.

---

## Run Apps Using VS Code Debugger

You can use the VS Code debugger to start the backend, frontend, or both at once:

1. Open the Run & Debug panel in VS Code (`Ctrl+Shift+D`).
2. Select one of the following configurations from the dropdown:
   - **Launch Backend: FastAPI** – starts the backend (also starts Arize container automatically via preLaunchTask).
   - **Launch Frontend (UI)** – starts the frontend app.
   - **Launch Frontend and Backend** – starts both apps simultaneously.
3. Click the green play button to start debugging.

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

