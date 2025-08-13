# 5.5: Update the MultiAgentFlow Class

## Understanding MultiAgent Architecture Using LlamaIndex Workflows

LlamaIndex workflows are designed around an event-driven architecture. The workflow begins when a start event is triggered. Each step in the workflow is defined by a function decorated with the `@step` decorator. These step functions are activated by specific event types, which means the workflow decides which step to execute next based on the event it receives.

Each step processes its input event and returns a new event. This returned event determines the next step in the workflow. Steps can emit multiple events, and a single step can be set up to listen for several different event types. This flexibility allows multiple steps to run in parallel, enabling efficient and scalable workflows.

In the context of multi-agent systems, each step typically interacts with a different agent. By leveraging this event-driven approach, you can run several agents at the same time, making your workflow both modular and highly parallelized. This structure makes it easy to add, remove, or modify agents as your application evolves.

## Define Reviews Events

In this step, we will define `ReviewsEvent` and `ReviewsCompletedEvent` classes in `multi_agent_workflow.py` to handle events related to the Reviews Agent. These event classes allow the workflow to track and manage the execution and completion of the Reviews Agent.

!!! info "**File location:** `backend/src/agents/multi_agent_workflow.py` (or wherever your workflow/event classes are defined)"

!!! info "**Purpose:** These classes represent the start and completion of a review agent task, enabling event-driven orchestration in your workflow."

!!! danger "Add the code below to the `backend/src/agents/multi_agent_workflow.py` file, right after the other event classes."

```python
class ReviewsEvent(Event):
    pass

class ReviewsCompletedEvent(Event):
    result: str
```

!!! info "**What this does:**"
    These event classes allow the workflow to track and manage the execution and completion of the Reviews Agent.

---

## Integrate Reviews Agent in MultiAgentFlow Class

In this step, we will update the `MultiAgentFlow` class to accept the Reviews Agent, emit and handle review events, and add a step function for the Reviews Agent. This will integrate the Reviews Agent into the multi-agent workflow, allowing it to be triggered, run, and its results to be handled like other agents.

!!! info "**File location:** `backend/src/agents/multi_agent_workflow.py`"

!!! info "**Purpose:** This step wires your Reviews Agent into the event-driven workflow, so it can be triggered, run, and its results handled like any other agent."

!!! danger "Accept `reviews_agent` in the MultiAgentFlow class constructor argument."

```python
reviews_agent: BaseAgent,
```

!!! danger "Assign `reviews_agent` in the MultiAgentFlow class constructor body."

```python
self.reviews_agent = reviews_agent
```

The constructor of the MultiAgentFlow class should now look like this.

```python
def __init__(
    self,
    db: AsyncSession,
    product_personalization_agent: BaseAgent,
    inventory_agent: BaseAgent,
    presentation_agent: BaseAgent,
    planning_agent: BaseAgent,
    reviews_agent: BaseAgent,
    memory: Memory,
    message_queue: Optional[asyncio.Queue] = None,
    fault_correction: bool = False,
    **kwargs,
):
    self.db = db
    self.product_personalization_agent = product_personalization_agent
    self.inventory_agent = inventory_agent
    self.presentation_agent = presentation_agent
    self.planning_agent = planning_agent
    self.reviews_agent = reviews_agent
    self.memory = memory
    self.message_queue = message_queue
    self.fault_correction = fault_correction

    super().__init__(**kwargs)
```

---

Any type of event that a step function can emit must be mentioned in its return type. We add the ReviewsEvent here so that it can be triggered by the planning agent.

!!! danger "Add `ReviewsEvent` to the planning step return type."

```python
ProductPersonalizationEvent | InventoryEvent | ReviewsEvent:
```

The planning step function signature would look like this

```python
@step
async def planning(
    self,
    ctx: Context,
    ev: StartEvent,
) -> ProductPersonalizationEvent | InventoryEvent | ReviewsEvent:
```

---

The planning step includes conditional logic based on the output of the planning agent. This logic determines which step to trigger next. To support the Reviews Agent, update this logic so it can also trigger the Reviews Agent when appropriate. By adding this code to the planning step function, the workflow can emit a `ReviewsEvent`, which in turn activates the review agent step function.

!!! danger "Add the code below in planning step function."

```python
if "reviews" in agents_to_call:
    ctx.send_event(ReviewsEvent())
    triggered_agents.append(ReviewsCompletedEvent)
```

The conditional logic inside planning step, to trigger next events should look like this.

```python
if "product_personalization" in agents_to_call:
    ctx.send_event(ProductPersonalizationEvent())
    triggered_agents.append(ProductPersonalizationCompletedEvent)
if "inventory" in agents_to_call:
    ctx.send_event(InventoryEvent())
    triggered_agents.append(InventoryCompletedEvent)
if "reviews" in agents_to_call:
    ctx.send_event(ReviewsEvent())
    triggered_agents.append(ReviewsCompletedEvent)
```

---

This is the review agent step function, which is executed when the ReviewsEvent is emitted. Within this, we call the review agent that we previously defined in the agents/reviews_agent.py file.

!!! danger "Add a step function for the Reviews Agent."

```python
@step
async def review(
    self,
    ctx: Context,
    ev: ReviewsEvent,
) -> ReviewsCompletedEvent:
    """
    Handles the review event by running the Reviews Agent and returning the result.
    """
    user_info = await ctx.get("user_profile")
    user_message = await ctx.get("user_msg")

    try:
        prompt = textwrap.dedent(
            f"""
            Generate a summary of relevant reviews of the product based on the
            user's preferences: {user_info['user_preferences']}
            and the optional user query: {user_message}.
        """,
        )

        logger.info(f"Review Prompt: {prompt}")

        result = await self.reviews_agent.run(
            prompt,
            timeout=settings.REVIEW_AGENT_TIMEOUT,
        )

    except WorkflowTimeoutError:
        logger.info("Review Agent has timed out.")
        result = "Review agent timed out. No response"

    return ReviewsCompletedEvent(result=str(result))
```

---

The presentation agent accepts the outputs of agents and then refines them before eventually passing them to the frontend. We add the ReviewsCompletedEvent here so that the presentation agent accepts the output of the review agent step function.

!!! danger "Update the presentation step function to accept `ReviewsCompletedEvent`."

```python
ev: ProductPersonalizationCompletedEvent | InventoryCompletedEvent | ReviewsCompletedEvent,
```

The presentation step function should look now look like this.

```python
@step
async def presentation(
    self,
    ctx: Context,
    ev: ProductPersonalizationCompletedEvent | InventoryCompletedEvent | ReviewsCompletedEvent,
) -> StopEvent:
```

---

!!! info "**What this does:**"
    These changes integrate the Reviews Agent into the multi-agent workflow, allowing it to be triggered, run, and its results to be handled like other agents.

---

**Further Explanation:**

In this step, we are integrating the Reviews Agent into the LlamaIndex workflow system. According to the [LlamaIndex Workflows documentation](https://docs.llamaindex.ai/en/stable/module_guides/workflow/#workflows), a workflow is an event-driven abstraction that chains together several steps, each responsible for handling specific event types and emitting new events.

Here's how the code in this step leverages LlamaIndex workflows:

- The `MultiAgentFlow` class is structured as a workflow, where each function decorated with `@step` represents a step in the workflow. Each step listens for certain event types (like `ReviewsEvent`) and produces new events (like `ReviewsCompletedEvent`).
- By adding the Reviews Agent, we introduce a new step (`review`) that is triggered when a `ReviewsEvent` is emitted. This step processes the event, runs the Reviews Agent, and emits a `ReviewsCompletedEvent` with the result.
- The workflow system ensures that each step only runs when the appropriate event is ready, and the input/output types are validated automatically.
- This modular, event-driven approach allows you to flexibly chain together multiple agents and logic, making it easy to extend or modify the workflow as your application grows.

In summary, this step connects the Reviews Agent to the overall multi-agent workflow, enabling event-driven, modular, and observable orchestration of agent logic using LlamaIndex's workflow system.
