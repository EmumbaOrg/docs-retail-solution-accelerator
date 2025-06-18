# 4.2 Multi-Agent Workflow Implementation

This section provides a deep dive into the **multi-agent workflow** implemented in AgenticShop. You’ll learn how the architecture ties together multiple specialized agents—each with distinct responsibilities—and how these agents collaborate via LlamaIndex Workflows to personalize product experiences.

We’ll walk through:

- The **overall agent roles**
- A detailed look at the **Product Personalization Agent**
- How **user memory** enhances personalization
- The **anatomy of an agent** in our implementation

---

## Agent Overview

The current multi-agent system includes the following agents:

| Agent                    | Description                                                                                 |
|-------------------------|---------------------------------------------------------------------------------------------|
| **Planner Agent**       | Determines which agents to invoke based on user query and profile.                          |
| **Product Personalization Agent** | Tailors product descriptions to the user’s preferences.                            |
| **Inventory Agent**     | Analyzes inventory constraints and availability.                                            |
| **Presentation Agent**  | Synthesizes responses from other agents into a final, user-facing product description.      |

Agents are connected through **events** and executed via the LlamaIndex `Workflow` system, which provides modularity, traceability, and event-driven orchestration.

---

##  Example: Product Personalization Agent

Let’s walk through one of the core agents—**Product Personalization Agent**—and how it operates within the workflow.

When triggered, it receives user profile data, product attributes, and product variants. It then uses this context to generate a personalized product summary.

### Event Flow:
- Triggered by: `ProductPersonalizationEvent`
- Returns: `ProductPersonalizationCompletedEvent`

```python
result = await self.product_personalization_agent.run(
    f"""Personalize the product for user: {user_info},
    product: {product_info}, product variants: {vaiants_info}""",
    timeout=settings.PRODUCT_PERSONALIZATION_AGENT_TIMEOUT,
)
```

If the agent fails to respond within the timeout, a fallback message is returned to ensure the workflow continues.

---

## Anatomy of an Agent

Agents in AgenticShop are implemented using LlamaIndex’s `FunctionAgent`. Each agent wraps a system prompt and has a clear role.

Here’s how the **Product Personalization Agent** is defined:

```python
return FunctionAgent(
    name=AgentNames.PRODUCT_PERSONALIZATION_AGENT.value,
    description=(
        "Analyzes user profiles and product data to highlight features most relevant to individual preferences."
    ),
    llm=llm,
    system_prompt=PRODUCT_PERSONALIZATION_AGENT_PROMPT,
    tools=[],
    allow_parallel_tool_calls=False,
    verbose=settings.VERBOSE,
)
```

### Where to Find System Prompts

All system prompts for these agents are defined in:

```bash
backend/src/agents/prompts.py
```

You can modify the tone, structure, or output expectations of each agent by editing the associated prompt in this file.

---

## Memory Integration

The system uses `mem0` to personalize interactions based on **user memory**. Here’s how memory contributes:

- During **workflow setup**, user preferences are retrieved from memory and included in the profile.
- If a user adds new input (e.g., "I care about portability"), it’s stored back to memory.
- Memory influences agent outputs like personalization.

### Memory Update Example

```python
results = self.memory.add(messages=user_msg, user_id=str(user_id))
```

These updates are stored in PostgreSQL under the `mem0_chatstore` table.

---

## 🧪 Try This Yourself

If you want to modify agent behaviors:

- Review their prompts in `prompts.py`
- Adjust their logic in the workflow
- Explore agent outputs by triggering workflows via user queries

This modular setup allows you to easily add new agents or tweak existing ones. In the next section, you’ll do just that—implement a new agent to handle review analysis.
