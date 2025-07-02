# 3.4 Generating Memories

In any agentic application, **memory** is a foundational component. It allows agents to retain information about a user—such as preferences, behaviors, or past interactions—and use that knowledge to deliver **personalized experiences**.

In **AgenticShop**, we use [`mem0`](https://github.com/mem0ai/mem0) as the memory engine for storing and retrieving user-specific insights.

---

## What Does Memory Do?

Memory enables agents to:

- Track individual user preferences (e.g., preferred brands, features, or categories)
- Evolve recommendations based on historical interactions
- Persist state across workflows and sessions

!!! note "Example: If a user mentions they prefer "lightweight laptops with long battery life", that preference is stored and reused by downstream agents."

---

## How Is Memory Initialized?

During the data seeding phase of the workshop, we load preconfigured user preferences from a CSV file. These are injected into `mem0` using the following logic:

```python
output = memory.add(
    messages=preference,
    user_id=str(user_id),
)
```

!!! note "This script is executed as part of the Alembic data seeding migration. It ensures each user starts with a base set of memory entries."

---

## Memory Is Dynamic

While the initial preferences come from seed data, **memories are continuously updated** during runtime. For example:

- If a user says, “I prefer to read critical reviews about durability,” during his session, this information is recorded.
- Agents refer to the stored memories to personalize the output.
- Updates are made transparently as part of agent execution logic.

We’ll explore runtime memory updates in later sections during interactive agent flows.

---

## Try It Yourself: Create Memories Manually

You can try storing user memories in an interactive shell within the devcontainer. Here's how:

!!! tip "Try creating new memories by following this section"

### Step 1: Open the Python REPL inside your devcontainer

```bash
cd backend
python
```

### Step 2: Create a memory instance and add preferences

```python
from src.config.memory import get_mem0_memory

memory = get_mem0_memory()

# Simulate adding preferences for user_id=1
memory.add(messages="Prefers long battery life", user_id="1")
memory.add(messages="Interested in noise-cancelling headphones", user_id="1")
```

### Step 3: Retrieve what’s stored (optional)

```python
results = memory.search(query="User's specific preferences, likes, dislikes, past interactions, and shopping behavior patterns?", user_id="1")
print(results)
```

!!! info "These memory entries will now influence how the personalization agent tailors product recommendations in later steps."

---

### ️Step 4: Viewing Stored Memories in PostgreSQL

All memories stored by `mem0` are persisted in a dedicated PostgreSQL table called `mem0_chatstore`. This table is **managed internally by the mem0 framework**—you don't need to create or modify it manually.

!!! tip "To inspect the memory data stored for different users, you can run the following SQL query using **pgAdmin** or any SQL tool"

```sql
SELECT id, payload FROM public.mem0_chatstore
ORDER BY id ASC LIMIT 100;
```

This will return rows containing the memory text, associated user ID, and metadata such as creation timestamp. Here's an example output:

| id                                    | payload                                                                                                                                                  |
|--------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| 254218b4-070a-4573-bd54-6a56962ca216 | {"data": "Prefers critical reviews about durability", "user_id": "1", "created_at": "2025-06-04T00:41:58.613501-07:00"}                                  |
| 298df4f9-4fcb-409e-858b-66372ef8e0c8 | {"data": "Has a deep passion for high-quality audio", "user_id": "1", "created_at": "2025-05-29T07:22:05.293070-07:00"}                                  |
| a3c4ec50-8b0b-46f3-9e85-2e84e2faf805 | {"data": "Prefers black color", "user_id": "1", "created_at": "2025-05-29T07:22:01.393729-07:00"}                                                         |
| d27e92f0-aff3-4e8f-98eb-7efd1f8e3f69 | {"data": "Prefers workout-friendly products", "user_id": "2", "created_at": "2025-05-29T07:22:18.402198-07:00"}                                           |

!!! info "Each `payload` is a JSON object containing a single memory item and the associated user (metadata)."

## Behind the Scenes

While you can add memories manually or programmatically, under the hood `mem0` uses an large language model (LLM) to extract structured insights—called **facts**—from user input. 

These facts are:

- Parsed and summarized from free-form user messages (e.g., “I prefer wireless headphones with long battery life.”)
- Stored with metadata (e.g., user ID, timestamp, hashed ID)
- Indexed and retrieved later by context-aware agents during personalization

The memory engine acts as a **semantic layer**, distilling key preferences or intents from conversations and making them accessible to agents without needing to reprocess raw history.

!!! info "🧠 The LLM prompt used for extraction is defined [here in the mem0 GitHub repo](https://github.com/mem0ai/mem0/blob/main/mem0/configs/prompts.py). You can customize this to fit your domain-specific needs."

## Summary

- `mem0` helps agents remember and adapt to individual users.
- Initial memories are created from CSVs during setup.
- You can manually add entries and inspect them from the REPL.
- Live user preferences will be added during interaction with the system.
