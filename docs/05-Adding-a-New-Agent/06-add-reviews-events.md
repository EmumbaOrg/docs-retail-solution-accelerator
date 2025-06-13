# 5.7: Add Reviews Events

In this step, we will define `ReviewsEvent` and `ReviewsCompletedEvent` classes in `multi_agent_workflow.py` to handle events related to the Reviews Agent. These event classes allow the workflow to track and manage the execution and completion of the Reviews Agent.

> **File location:** `src/agents/multi_agent_workflow.py` (or wherever your workflow/event classes are defined)
>
> **Purpose:** These classes represent the start and completion of a review agent task, enabling event-driven orchestration in your workflow.

---

```python
# --- Event Classes for Reviews Agent ---
class ReviewsEvent(Event):
    self_reflection: Optional[str] = None
    prev_result: Optional[str] = None

class ReviewsCompletedEvent(Event):
    result: str
```

---

**What this does:**
These event classes allow the workflow to track and manage the execution and completion of the Reviews Agent.
