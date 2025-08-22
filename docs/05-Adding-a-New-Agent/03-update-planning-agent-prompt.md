# 5.3 Update Planning Agent Prompt


This section explores how the `PLANNING_AGENT_PROMPT` is updated to include a description of the Reviews Agent and to update the example agents list in the JSON response. Notice how these changes ensure the planning agent is aware of the Reviews Agent and can include it in its planning and agent selection logic.

!!! info "**File location:** `backend/src/agents/prompts.py`"

!!! info "**Purpose:** This step makes sure the planner knows about your new agent and can suggest it for relevant user queries."

!!! info "Observe the following line, which is included inside the PLANNING_AGENT_PROMPT string under the section that begins with 'Following agents are available:'."

```python
"""
- Reviews Agent: Answer queries related to product reviews. Can be used to generate personalized
  content related to product reviews. Invoke if user is interested in reviews.
"""
```

---

!!! info "Notice the update to the JSON agents list present in the planning agent prompt."

```python
"Respond with a JSON list of agents to call: ["product_personalization", "reviews", "inventory"]"
```

---

The updated prompt should look like this.

```python
PLANNING_AGENT_PROMPT = """
Given a user profile, and optional user query, your task is to determine which specialized agents should be invoked as part
of an agentic workflow to personalize the product information section of an ecommerce page.

Following agents are available:
  - Product Personalization Agent: Analyzes the users profile and then suggests the features of the product that match
    the users profile and create customized description of the product based on the user profile.
  - Inventory Agent: Checks availability and related options based on users preference. Invoke if user
    has certain preferences that can be checked against the product inventory.
  - Reviews Agent: Answer queries related to product reviews. Can be used to generate personalized
    content related to product reviews. Invoke if user is interested in reviews.

Only include agents that would provide value given the context.
Respond with a JSON list of agents to call: ["product_personalization", "reviews", "inventory"]" and
don’t include any code blocks or backticks.
"""
```

---

!!! info "**What this does:**"
    This change ensures the planning agent is aware of the Reviews Agent and can include it in its planning and agent selection logic.
