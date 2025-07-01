# 4.3 Tool-Enabled Agent: Intelligent Query Router

In this section, we explore a powerful enhancement to our multi-agent setup—introducing the User Query Agent, an agent equipped with multiple tools. Unlike earlier agents that perform a single role, this one unifies multiple capabilities under a single interface, allowing it to dynamically respond to different types of user queries.

Where previous agents handled focused tasks like personalization or inventory lookup, this agent handles broader input and delegates work to appropriate tools (including triggering the workflow we discussed in previous section).

---

### 🔧 What Makes This Agent Special?

Unlike earlier agents, the **User Query Agent** is initialized with three tools:

| Tool Name                   | Purpose                                                                 |
|----------------------------|-------------------------------------------------------------------------|
| `query_about_product`      | Triggers the multi-agent personalization workflow for the current user |
| `search_products`          | Performs a vector search over product embeddings                       |
| `query_reviews_with_sentiment` | Combines sentiment and feature filters to search reviews graphically     |

These tools are defined as callable Python functions and wrapped into LlamaIndex `FunctionTool` objects. The agent then uses the LLM to choose the correct tool based on user intent.

```python
FunctionAgent(
    name="user_query_agent",
    description="Smart router that interprets user queries and directs them to the most appropriate tool.",
    system_prompt=USER_QUERY_AGENT_PROMPT,
    tools=[...],  # Three tool functions
    llm=llm,
    verbose=settings.VERBOSE,
)
```

---

### How It Works

When a query reaches this agent, here’s the high-level flow:

1. **LLM Reasoning**: The LLM receives the user query and system prompt. Based on the tool metadata, it selects the most relevant tool to invoke.
2. **Tool Invocation**: The corresponding Python function is executed:
   - For **sentiment-based queries**, it performs a vector + graph query.
   - For **general product searches**, it uses embedding search.
   - For **personalization flows**, it dispatches a background multi-agent workflow.
3. **Streaming Response**: Each tool emits events back to the frontend via a message queue for real-time updates.

!!! note "This setup demonstrates how a single LLM-powered agent can identify user intent in real time and route the query to the most appropriate tool, enabling more natural interactions and unlocking dynamic, intent-driven experiences."

---

### Example Queries & Tool Actions

You can experiment with this agent directly from the Product Listing or Product Detail Page. Just type in a query that reflects your interest or concern, and the agent will intelligently route your request to the most suitable tool.

| Query Example                                                  | Tool Triggered               |
|----------------------------------------------------------------|------------------------------|
| “Wireless headphones with positive reviews about noise cancellation”                         | `query_reviews_with_sentiment` |
| “Waterproof headphones”                                    | `search_products`           |
| “Show me summary of positive reviews about durability” (on product page)     | `query_about_product`       |

