# 5.4: Update the MultiAgentFlow Class

## Define Reviews Events

In this step, we will define `ReviewsEvent` and `ReviewsCompletedEvent` classes in `multi_agent_workflow.py` to handle events related to the Reviews Agent. These event classes allow the workflow to track and manage the execution and completion of the Reviews Agent.

> **File location:** `backend/src/agents/multi_agent_workflow.py` (or wherever your workflow/event classes are defined)
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

---

## Intergrade Reviews Agent in MultiAgentFlow Class

In this step, we will update the `MultiAgentFlow` class to accept the Reviews Agent, emit and handle review events, and add a step function for the Reviews Agent. This will integrate the Reviews Agent into the multi-agent workflow, allowing it to be triggered, run, and its results to be handled like other agents.

> **File location:** `backend/src/agents/multi_agent_workflow.py`
> 
> **Purpose:** This step wires your Reviews Agent into the event-driven workflow, so it can be triggered, run, and its results handled like any other agent.

---

- **Accept `reviews_agent` in the constructor:**

```python
# --- Add to constructor ---
reviews_agent: BaseAgent,
```

- **Assign it in the constructor body:**

```python
# --- Assign in constructor ---
self.reviews_agent = reviews_agent
```

- **Add `ReviewsEvent` to the planning step return type:**

```python
# --- Update planning step return type ---
ProductPersonalizationEvent | InventoryEvent | ReviewsEvent:
```

- **Trigger the Reviews Agent when needed:**

```python
# --- Trigger Reviews Agent ---
if "reviews" in agents_to_call:
    ctx.send_event(ReviewsEvent())
    triggered_agents.append(ReviewsCompletedEvent)
```

- **Add a step function for the Reviews Agent:**

```python
# --- Reviews Agent Step Function ---
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

    self_reflection_prompt = ""
    generate_error_prompt = ""

    if self.fault_correction and not ev.self_reflection:
        # To mock faulty output...
        # Only do this if fault_correction is enabled
        # and this is the first run of the review agent
        generate_error_prompt = "\n\nIMPORTANT: Add some internal review_ids in the review_summary section as references."

    try:
        prompt = textwrap.dedent(
            f"""
            {self_reflection_prompt}
            Generate a summary of relevant reviews of the product based on the
            user's preferences: {user_info['user_preferences']}
            and the optional user query: {user_message}.
            {generate_error_prompt}
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

- **Update the presentation step to accept `ReviewsCompletedEvent`:**

```python
# --- Update presentation step signature ---
ev: ProductPersonalizationCompletedEvent | InventoryCompletedEvent | ReviewsCompletedEvent,
```

---

**What this does:**
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
