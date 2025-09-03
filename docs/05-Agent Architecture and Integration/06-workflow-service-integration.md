# 5.6: Workflow Service Integration

This section provides an overview of how the Reviews Agent is initialized and included in the workflow service (`backend/src/services/agent_workflow.py`). The agent is imported and passed to the `MultiAgentFlow` during initialization, ensuring it is part of the overall multi-agent orchestration.

!!! info "**File location:** `backend/src/services/agent_workflow.py`"

!!! info "**Purpose:** This integration wires the Reviews Agent into the workflow service, so it can be used as part of the multi-agent system."

---

## Reviews Agent Import

The `get_reviews_agent` function is imported alongside other agent constructors:

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

## Passing Reviews Agent to MultiAgentFlow

The Reviews Agent is passed as an argument when initializing the `MultiAgentFlow` in the `create_workflow` function:

```python
reviews_agent=get_reviews_agent(
    self.llm,
    self.embed_model,
    self.vector_store_reviews_embeddings,
    self.filters,
),
```

The full initialization looks like this:

```python
def create_workflow(self) -> MultiAgentFlow:
    workflow = MultiAgentFlow(
        self.db,
        product_personalization_agent=get_product_personalization_agent(self.llm),
        presentation_agent=get_presentation_agent(self.llm),
        inventory_agent=get_inventory_agent(
            self.llm,
            self.embed_model,
        ),
        planning_agent=get_planning_agent(self.llm),
        reviews_agent=get_reviews_agent(
            self.llm,
            self.embed_model,
            self.vector_store_reviews_embeddings,
            self.filters,
        ),
        memory=self.memory,
        message_queue=self.message_queue,
        timeout=self.timeout,
        verbose=self.verbose,
        fault_correction=self.fault_correction,
    )
    return workflow
```

---

!!! info "**What this does:**"
    This ensures the Reviews Agent is properly initialized and included in the workflow service.
