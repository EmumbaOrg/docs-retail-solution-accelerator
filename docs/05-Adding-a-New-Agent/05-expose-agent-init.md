# 5.6 📦🔗 Expose the Agent in the Module Init

In this step, you'll update `src/agents/__init__.py` to import and expose the `get_reviews_agent` function. This makes the function available for import elsewhere in your codebase, allowing other modules to use the Reviews Agent. Let's make your agent accessible everywhere it needs to be!

> **File location:** `src/agents/__init__.py`
>
> **Purpose:** Register your agent so it can be easily imported and used throughout your project.

---

```python
# --- Expose the Reviews Agent ---
# Import:
from .reviews_agent import get_reviews_agent

# Then add this in the list:
"get_reviews_agent",
```

---

**What this does:**
This makes the `get_reviews_agent` function available for import elsewhere in your codebase. 🌐✨

**Tip:**
- Always register new agents or utilities in your module's `__init__.py` for easy access and maintainability!
