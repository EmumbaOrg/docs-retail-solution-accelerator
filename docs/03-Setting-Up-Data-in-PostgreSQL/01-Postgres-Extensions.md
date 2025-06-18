# 3.1 Install Extensions

Azure Database for PostgreSQL Flexible Server allows you to extend the functionality of your database using extensions. Extensions bundle multiple related SQL objects into a single package that can be loaded or removed from your database with a single command. After being loaded into the database, extensions function like built-in features.

> ✅ These steps have **already been executed** automatically via our [Bicep infrastructure deployment](../path-to-bicep-scripts) as part of the provisioning process.
> The content below is included for reference and understanding of what was configured behind the scenes.

---

## Allowlist the Extensions

To enable extensions in Azure Database for PostgreSQL Flexible Server, they must first be added to the server's _allowlist_. This step is handled automatically by Bicep during deployment.

You can also review how this is done manually in [the official documentation](https://learn.microsoft.com/azure/postgresql/extensions/how-to-allow-extensions).

=== "Azure CLI"

```bash
az postgres flexible-server parameter set \
  --resource-group [YOUR_RESOURCE_GROUP] \
  --server-name [YOUR_POSTGRESQL_SERVER] \
  --subscription [YOUR_SUBSCRIPTION_ID] \
  --name azure.extensions \
  --value azure_ai,pg_diskann,vector,age
```

---

## Install Extensions

Once allowlisted, extensions are installed in your database. This is also fully automated by the deployment process using Alembic migrations and Bicep scripts.

You may optionally inspect or manually install them using SQL:

=== "Azure AI extension"

```sql
CREATE EXTENSION IF NOT EXISTS azure_ai;
```

Enables AI-powered capabilities by connecting your database with Azure OpenAI and Cognitive Services for operations like sentiment analysis, embedding generation, and text extraction.

=== "pgvector extension"

```sql
CREATE EXTENSION IF NOT EXISTS vector;
```

Adds support for storing and querying vector embeddings—used to enable semantic search and similarity ranking.

=== "DiskANN extension"

```sql
CREATE EXTENSION IF NOT EXISTS pg_diskann;
```

Supports fast and scalable approximate nearest neighbor (ANN) search, optimized for large vector datasets.

=== "Apache AGE extension"

```sql
CREATE EXTENSION IF NOT EXISTS age;
```

Enables graph database capabilities within PostgreSQL using Apache AGE. Allows you to model relationships and run Cypher queries for graph traversal and reasoning.

!!! tip "DiskANN depends on pgvector"

Make sure the `vector` extension is created before or use `CASCADE` to automatically handle dependencies.

---

## Verify Installed Extensions

To verify which extensions are currently installed:

1. Open **pgAdmin** and connect to your database.
2. In the **Object Explorer**, expand your database and go to **Extensions**.
3. Alternatively, open a SQL query window and run:

```sql
SELECT * FROM pg_available_extensions WHERE installed_version IS NOT NULL;
```

This will list all extensions currently installed in your PostgreSQL instance.

---

> 💡 If you're interested in understanding how these are configured programmatically, you can explore the [Bicep deployment scripts](../path-to-bicep-scripts).