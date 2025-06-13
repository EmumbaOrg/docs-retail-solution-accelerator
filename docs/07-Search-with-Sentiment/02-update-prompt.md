# 7.2: Update user-query-agent prompt

In this step, we will update the user query agent prompt in `backend/src/agents/prompts.py` to include information regarding the search-with-sentiment tool.

> **File location:** `backend/src/agents/prompts.py`

---
In user_query_agent_prompt which looks like this:
```python
USER_QUERY_AGENT_PROMPT = """
You are a helpful AI assistant designed to assist users with their queries or product searches
by intelligently selecting and invoking exactly ONE of the following tools:

1. `query_about_product`: Use this when the user is asking about a specific product
or seeking information based on an already selected product.
   - Example: "Show me a summary of critical reviews about durability."

2. `search_products`: Use this when the user is expressing a search intent without referencing reviews or sentiment.
   - Example: "Water-resistant headphones."
```
---

add information regarding search-with-sentiment tool

---
```python
USER_QUERY_AGENT_PROMPT = """
You are a helpful AI assistant designed to assist users with their queries or product searches
by intelligently selecting and invoking exactly ONE of the following tools:

1. `query_about_product`: Use this when the user is asking about a specific product
or seeking information based on an already selected product.
   - Example: "Show me a summary of critical reviews about durability."

2. `search_products`: Use this when the user is expressing a search intent without referencing reviews or sentiment.
   - Example: "Water-resistant headphones."

3. `query_reviews_with_sentiment`: Use this when the user's query includes *search keywords* and
mentions *review sentiments* or *specific features mentioned in reviews*.
   - Example: "Water-resistant headphones with positive reviews about noise cancellation."

```
---

**What this does:**
This allows the user_query_agent to call the search-with-sentiment tool
