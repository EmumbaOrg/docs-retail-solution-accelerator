# Step 5: Expose the Agent in the Module Init

In this step, we will update `src/agents/__init__.py` to import and expose the `get_reviews_agent` function. This makes the function available for import elsewhere in your codebase, allowing other modules to use the Reviews Agent.

---

```python
# Import:
from .reviews_agent import get_reviews_agent

# Then add this in the list:
"get_reviews_agent",
```

---

**What this does:**
This makes the `get_reviews_agent` function available for import elsewhere in your codebase.
