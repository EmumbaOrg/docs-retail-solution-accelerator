# 5.7 🛎️📣 Add Reviews Events

In this step, you'll define `ReviewsEvent` and `ReviewsCompletedEvent` classes in `multi_agent_workflow.py` to handle events related to the Reviews Agent. These event classes allow the workflow to track and manage the execution and completion of the Reviews Agent. Get ready to make your agent event-driven!

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
These event classes allow the workflow to track and manage the execution and completion of the Reviews Agent. 🕹️📊

**Tip:**
- Use clear, descriptive event names and attributes to make your workflow logic easy to follow and debug!
