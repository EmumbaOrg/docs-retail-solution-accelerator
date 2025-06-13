# 5.2 Update Planning Agent Prompt

In this step, we will update the `PLANNING_AGENT_PROMPT` to include a description of the Reviews Agent and update the example agents list in the JSON response. This ensures the planning agent is aware of the Reviews Agent and can include it in its planning and agent selection logic.

> **File location:** `backend/src/agents/prompts.py` (or wherever your planning agent prompt is defined)
> **Purpose:** This step makes sure the planner knows about your new agent and can suggest it for relevant user queries.

---

```python
# --- Planning Agent Prompt Update ---
"""
- Reviews Agent: Answer queries related to product reviews. Can be used to generate personalized
  content related to product reviews. Invoke if user is interested in reviews.
  Update the json agents list example present in planning agent        
Respond with a JSON list of agents to call: ["product_personalization", "reviews", "inventory"] 
"""
```

---

**What this does:**
This change ensures the planning agent is aware of the Reviews Agent and can include it in its planning and agent selection logic.

---

## Update Presentation Agent Prompt

In this step, we will update the `PRESENTATION_AGENT_PROMPT` to list the Reviews Agent as one of the available agents. This makes the Reviews Agent visible to the presentation layer, allowing it to be referenced.

> **File location:** `backend/src/agents/prompts.py` (or wherever your presentation agent prompt is defined)
> **Purpose:** This step ensures the Reviews Agent is discoverable and selectable in the UI or any presentation layer.

---

```python
# --- Presentation Agent Prompt Update ---
Agents:
- Product Personalization Agent
- Inventory Agent
- Reviews Agent
```

---

**What this does:**
This makes the Reviews Agent visible to the presentation layer, allowing it to be referenced and invoked as needed.
