# 7.1: Add tool in User-Query-Agent

In this step, we will add 'search-with-sentiment' tool,that we created in the previous step, in the user_query_agent in `backend/src/agents/user_query_agent.py` so that it can be called by the agent.

> **File location:** `backend/src/agents/user_query_agent.py`

---
In _get_tools function which looks like this:
```python
def _get_tools(self) -> list:
        tools_func = [
            self.query_about_product,
            self.search_products,
        ]
        return [
            FunctionTool.from_defaults(tool, return_direct=True) for tool in tools_func
        ]
```
---

add `self.query_reviews_with_sentiment`

---
```python
def _get_tools(self) -> list:
        tools_func = [
            self.query_about_product,
            self.query_reviews_with_sentiment
            self.search_products,
        ]
        return [
            FunctionTool.from_defaults(tool, return_direct=True) for tool in tools_func
        ]
```
---

**What this does:**
This allows the user_query_agent to call the search-with-sentiment tool
