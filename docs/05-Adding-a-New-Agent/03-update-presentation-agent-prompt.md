# 5.4 Update Presentation Agent Prompt

In this step, we will update the `PRESENTATION_AGENT_PROMPT` to list the Reviews Agent as one of the available agents. This makes the Reviews Agent visible to the presentation layer, allowing it to be referenced.

> **File location:** `backend/src/agents/prompts.py` (or wherever your presentation agent prompt is defined)
> 
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
