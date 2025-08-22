# 5.4 Update Presentation Agent Prompt


This section describes how the `PRESENTATION_AGENT_PROMPT` is updated to list the Reviews Agent as one of the available agents. Notice how this change makes the Reviews Agent visible to the presentation layer, allowing it to be referenced.

!!! info "**File location:** `backend/src/agents/prompts.py`"

!!! info "**Purpose:** This step ensures the Reviews Agent is discoverable and selectable in the UI or any presentation layer."

!!! info "Observe the addition of the `- Reviews Agent` line after `- Inventory Agent` in the agents list within `PRESENTATION_AGENT_PROMPT`."

```python
Agents:
- Product Personalization Agent
- Inventory Agent
- Reviews Agent
```

---

!!! info "**What this does:**"
    This makes the Reviews Agent visible to the presentation layer, allowing it to be referenced and invoked as needed.
