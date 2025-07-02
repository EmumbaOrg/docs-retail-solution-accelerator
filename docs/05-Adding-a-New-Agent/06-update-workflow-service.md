# 5.6: Update Workflow Service

In this step, we will re-import `get_reviews_agent` in `backend/src/services/agent_workflow.py` and pass it to the `MultiAgentFlow` when initializing. This ensures the Reviews Agent is properly initialized and included in the workflow service.

!!! info "**File location:** `backend/src/services/agent_workflow.py`"

!!! info " **Purpose:** This step wires your Reviews Agent into the workflow service, so it can be used as part of the overall multi-agent orchestration."

```python
from src.agents import (
    get_inventory_agent,
    get_planning_agent,
    get_presentation_agent,
    get_product_personalization_agent,
    get_reviews_agent,
)
```

---

## Pass Review Agent to MultiAgentFlow
```python
reviews_agent=get_reviews_agent(
    self.llm,
    self.embed_model,
    self.vector_store_reviews_embeddings,
    self.filters,
),
```

!!! info "**What this does:**"
    This ensures the Reviews Agent is properly initialized and included in the workflow service.
