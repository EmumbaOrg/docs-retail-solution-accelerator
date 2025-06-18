# 5.7: Add Agent Timeout

In this step, we will add the 'REVIEW_AGENT_TIMEOUT'  in `backend/src/config/config.py`. This is the maximum amount of time the review agent will take to process a request, after which it will timeout, allowing the agentic flow to move forward gracefully.

> **File location:** `backend/src/config/config.py`
>
> **Purpose:** This step creates a timeout configuration for the review agent.


Add the following in the Settings class

---

```python
    REVIEW_AGENT_TIMEOUT: int = 60
```

---

**What this does:**
This ensures the Reviews Agent has a proper timeout configuration, allowing it to timeout gracefully without disrupting the rest of the agentic flow.
