# 5.3 Add the Reviews Agent Description in the Planning Agent Prompt

In this step, we will update the `PLANNING_AGENT_PROMPT` to include a description of the Reviews Agent and update the example agents list in the JSON response. This ensures the planning agent is aware of the Reviews Agent and can include it in its planning and agent selection logic.

---

```python
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
