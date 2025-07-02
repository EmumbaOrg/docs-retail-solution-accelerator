# 3.2 Alembic Migrations

In this section, you'll learn about how **Alembic** is used to manage the database schema in the AgenticShop backend.

## What is Alembic?

[Alembic](https://alembic.sqlalchemy.org/) is a lightweight database migration tool for Python. It is commonly used alongside SQLAlchemy to manage schema changes over time while preserving data. Alembic enables you to apply version-controlled changes to your database, ensuring consistent environments across development, staging, and production.

In the **AgenticShop** solution, Alembic plays a central role in:

- Creating and modifying relational schema (tables, indexes, constraints)
- Populating initial data from CSVs
- Generating and storing vector embeddings
- Applying in-database AI operations using the `azure_ai` extension
- Building a graph layer using Apache AGE for advanced search and reasoning

## Why Use Alembic in This Project?

Instead of requiring manual SQL scripts, Alembic automates:

- The full setup of the PostgreSQL schema
- Seeding and cleaning data
- Integration with AI services and vector store configuration
- Controlled rollouts of graph-based data structures

## Where to Find the Migration Code

The migration scripts are located in the [`alembic/versions`](https://github.com/Azure-Samples/postgres-agentic-shop/tree/main/backend/alembic) directory of the repository. Each migration file corresponds to a specific step in the data setup.


!!! info "Alembic tracks which migrations have already been applied. If you run the migration command again and there are no new migrations, nothing will change. Only newly created migration scripts will be executed. We have already executed the Alembic migrations and set up the database."