# 2.9 Run Application

## Database Setup

Before starting the backend or frontend application, the database must have the required tables and schema. This is handled automatically: when you deploy the app using `azd up`, all necessary `alembic` migrations are executed as part of the backend deployment process. You do not need to run any migration commands manually—your database will always be up to date after deployment.

!!! info "In future, if there is a need to make updates to the database, a new migration file can be added and the migrations can be re-run to update the database using the command `alembic upgrade head`. This can be done in two ways:"

    - **Local Machine** : From within the devcontainer of your local machine, navigate to the backend directory and run `alembic upgrade head` (Make sure your virtual environment is activated. Refer to [Section 2.4](./04-Setup-Dev-Environment.md)).
  
    - **Redeploy Backend Service** : Run `azd deploy backend` to deploy the backend service again. This process automatically re-runs the migrations from the backend container app server, updating the database. Because the backend container app, the database and other required services are in the same resource group, migrations typically execute faster this way than running them from your local machine.

!!! note "For detailed guidance on Alembic migrations, see [Section 3.2: Alembic Migrations](../03-Setting-Up-Data-in-PostgreSQL/02-Alembic-Migrations.md) of this workshop."

### Run Apps Using VS Code Debugger

You can use the VS Code debugger to start the backend, frontend, or both at once:

1. Open the Run & Debug panel in VS Code (`Ctrl+Shift+D`).
2. Select one of the following configurations from the dropdown:
    - **Launch Backend: FastAPI** – starts the backend (also starts Arize container automatically via preLaunchTask).
    - **Launch Frontend (UI)** – starts the frontend app.
    - **Launch Frontend and Backend** – starts both apps simultaneously.
3. Click the green play button to start the applications.

![debugger-dropdown](../img/debugger-drop-down.png)

!!! info "The compound configuration **Launch Frontend and Backend** will run both apps together, each in its own debugger instance."

---

## Troubleshooting

If you encounter issues while running the application, consider the following common solutions:

- **Error Starting Backend App**

    While starting the backend app from debugger you might face this error `No module named uvicorn`. If you have completed the step of installing backend project dependencies using `poetry install`, then this error means that vscode is not pointing to correct python interpreter.

    To fix this error, open the command panel in vscode using `ctrl + shift + p` type `Python: Select Interpreter` and press enter.
    ![Command Pallete Select Python Interpreter.](../img/select-python-interpreter-option.png)

    Choose the interpreter that is in our backend project virtual environment.
    ![Select Default Interpreter Option.](../img/default-python-interpreter.png)

- **Database Migration Errors:**  
  Ensure your database service is running and accessible. Double-check your environment variables for correct database connection details. Rerun the migration command if necessary.

- **Port Conflicts:**  
  Make sure ports `8000` (backend), `5173` (frontend), and `6006` (Arize Phoenix) are not in use by other applications. Stop any conflicting services or change the port numbers in your configuration.
