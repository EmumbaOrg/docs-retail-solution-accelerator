# 5.7: Agent Timeout Configuration

This section provides an overview of the `REVIEW_AGENT_TIMEOUT` setting in `backend/src/config/config.py`. This configuration sets the maximum amount of time the Reviews Agent will take to process a request, after which it will timeout, allowing the agentic flow to move forward gracefully.

!!! info "**File location:** `backend/src/config/config.py`"

!!! info "**Purpose:** This configuration ensures the Reviews Agent has a timeout, allowing it to exit gracefully if a request takes too long."

The timeout is set as a class variable in the settings class:

```python
REVIEW_AGENT_TIMEOUT: int = 60
```

---

!!! info "**What this does:**"
    This ensures the Reviews Agent has a proper timeout configuration, allowing it to timeout gracefully without disrupting the rest of the agentic flow.
