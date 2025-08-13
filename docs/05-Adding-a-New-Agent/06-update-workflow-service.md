# 5.6: Update Workflow Service

In this step, we will re-import `get_reviews_agent` in `backend/src/services/agent_workflow.py` and pass it to the `MultiAgentFlow` when initializing. This ensures the Reviews Agent is properly initialized and included in the workflow service.

!!! info "**File location:** `backend/src/services/agent_workflow.py`"

!!! info " **Purpose:** This step wires your Reviews Agent into the workflow service, so it can be used as part of the overall multi-agent orchestration."

!!! danger "Import `get_reviews_agent` in `backend/src/services/agent_workflow.py` file."

```python
from src.agents import (
    get_inventory_agent,
    get_planning_agent,
    get_presentation_agent,
    get_product_personalization_agent,
    get_reviews_agent,  # 👈 Newly added
)
```

---

## Pass Review Agent to MultiAgentFlow

!!! danger "Add reviews_agent in MutliAgentFlow class initialization inside MultiAgentFlowService `create_workflow` function"

```python
reviews_agent=get_reviews_agent(
    self.llm,
    self.embed_model,
    self.vector_store_reviews_embeddings,
    self.filters,
),
```

The `create_workflow` function of MultiAgentFlowService class should now look like this

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
