# 5.4 Presentation Agent Prompt Overview

This section provides an overview of how the `PRESENTATION_AGENT_PROMPT` lists the Reviews Agent as one of the available agents. This makes the Reviews Agent visible to the presentation layer, allowing it to be referenced.

!!! info "**File location:** `backend/src/agents/prompts.py`"

!!! info "**Purpose:** Ensures the Reviews Agent is discoverable and selectable in the UI or any presentation layer."

```python
Agents:
- Product Personalization Agent
- Inventory Agent
- Reviews Agent
```

---

!!! info "**What this does:**"
    This makes the Reviews Agent visible to the presentation layer, allowing it to be referenced and invoked
