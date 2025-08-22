# 5.7: Add Agent Timeout


This section describes the addition of the 'REVIEW_AGENT_TIMEOUT' in `backend/src/config/config.py`. Notice how this variable sets the maximum amount of time the review agent will take to process a request, after which it will timeout, allowing the agentic flow to move forward gracefully.

!!! info "**File location:** `backend/src/config/config.py`"

!!! info "**Purpose:** This step creates a timeout configuration for the review agent."

!!! info "Observe the following config variable added as a class variable in the settings class."

```python
REVIEW_AGENT_TIMEOUT: int = 60
```

---

!!! info "**What this does:**"
    This ensures the Reviews Agent has a proper timeout configuration, allowing it to timeout gracefully without disrupting the rest of the agentic flow.
