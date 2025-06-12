# Step 6: Add Reviews Events

In this step, we will define `ReviewsEvent` and `ReviewsCompletedEvent` classes in `multi_agent_workflow.py` to handle events related to the Reviews Agent. These event classes allow the workflow to track and manage the execution and completion of the Reviews Agent.

---

```python
class ReviewsEvent(Event):
    self_reflection: Optional[str] = None
    prev_result: Optional[str] = None
class ReviewsCompletedEvent(Event):
    result: str
```

---

**What this does:**
These event classes allow the workflow to track and manage the execution and completion of the Reviews Agent.
