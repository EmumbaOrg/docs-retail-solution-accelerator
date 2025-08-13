# 2.7 Setting Up Environment (.env) Files

Environment variable files (`.env`) are essential for securely managing configuration values such as API keys, service URLs, and secrets for each application component. This section guides you through setting up `.env` files for the **frontend**, **backend**, and **arize-phoenix** services.

---

## 1. Overview

After deploying your services on Azure (see section 2.6), a `.env` file is generated in the **root directory** of your project. This file contains all the necessary environment variable values (URLs, keys, etc.) for the deployed services. You do **not** need to manually retrieve these values from the Azure portal.

**To complete your setup:**

- Copy the relevant variables from the root `.env` file into the `.env` files for each service as described below.

---

## 2. Backend: Setting up `.env` file

1. In the `backend` directory, create a file named `.env` if it does not already exist.
2. Copy the backend-related variables from the root `.env` file. Typical variables include:
    - `DB_HOST`
    - `DB_PASSWORD`
    - `DB_NAME`
    - `DB_USER`
    - `AZURE_OPENAI_API_KEY`
    - `AZURE_OPENAI_ENDPOINT`
    - `AZURE_API_VERSION_LLM`
    - `AZURE_API_VERSION_EMBEDDING_MODEL`
    - Any other variables required by the backend (refer to sample `.env.example`).
3. Paste these variable values into `backend/.env`.

---

## 3. Frontend: Setting up `.env` file

1. In the `frontend` directory, create a copy of file named `.env.example` and rename it to `.env` if it does not already exist.
2. Set the below variable to the following.
    - `VITE_BE_APP_ENDPOINT=http://127.0.0.1:8000/`

---

## 4. Arize-Phoenix: Setting up `.env` file

By default, we recommend **not** setting the environment variable below for Arize Phoenix. In this case, traces will be stored locally inside the container using a Docker volume, and will persist as long as the Docker volume exists. However, if the volume is removed, traces will be lost.

!!! info "If you want to persist traces to an external database, follow the steps below to configure the connection."

    1. In the `arize-phoenix` directory, create a file named `.env` if it does not already exist.
    2. Copy the Arize-related variables from the root `.env` file. Typical variables include:
        - `PHOENIX_SQL_DATABASE_URL` (set this only if you want to persist traces externally)
    3. Paste these variables into `arize-phoenix/.env`.

!!! info "If you do not set `PHOENIX_SQL_DATABASE_URL`, Arize Phoenix will use its default local storage for traces."

---

## 5. Summary

- In case of new deployment, always update your service-specific `.env` files with the latest values from the root `.env` file.
- This process ensures each app has the correct configuration and can connect to the necessary Azure resources securely.

!!! warning "Never commit `.env` files with secrets to version control. Use `.gitignore` to keep them private."
