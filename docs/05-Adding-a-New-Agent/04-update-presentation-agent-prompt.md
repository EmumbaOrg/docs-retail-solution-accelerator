# 5.4 Update Presentation Agent Prompt

In this step, we will update the `PRESENTATION_AGENT_PROMPT` to list the Reviews Agent as one of the available agents. This makes the Reviews Agent visible to the presentation layer, allowing it to be referenced.

!!! info "**File location:** `backend/src/agents/prompts.py`"

!!! info "**Purpose:** This step ensures the Reviews Agent is discoverable and selectable in the UI or any presentation layer."

!!! danger "Add the `- Reviews Agent` line after `- Inventory Agent` in the agents list within `PRESENTATION_AGENT_PROMPT`."

```python
Agents:
- Product Personalization Agent
- Inventory Agent
- Reviews Agent
```

---

!!! info "**What this does:**"
    This makes the Reviews Agent visible to the presentation layer, allowing it to be referenced and invoked as needed.
