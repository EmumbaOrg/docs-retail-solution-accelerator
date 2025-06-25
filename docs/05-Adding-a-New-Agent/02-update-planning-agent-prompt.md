# 5.3 Update Planning Agent Prompt

In this step, we will update the `PLANNING_AGENT_PROMPT` to include a description of the Reviews Agent and update the example agents list in the JSON response. This ensures the planning agent is aware of the Reviews Agent and can include it in its planning and agent selection logic.

> **File location:** `backend/src/agents/prompts.py` (or wherever your planning agent prompt is defined)
> 
> **Purpose:** This step makes sure the planner knows about your new agent and can suggest it for relevant user queries.

---
```python
# --- Planning Agent Prompt Update ---
"""
- Reviews Agent: Answer queries related to product reviews. Can be used to generate personalized
  content related to product reviews. Invoke if user is interested in reviews.
"""
```
---

Update the json agents list present in planning agent prompt
---
```
Respond with a JSON list of agents to call: ["product_personalization", "reviews", "inventory"]
```
---



**What this does:**
This change ensures the planning agent is aware of the Reviews Agent and can include it in its planning and agent selection logic.
