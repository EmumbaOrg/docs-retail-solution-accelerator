# 5.5: Update the MultiAgentFlow Class

## Understanding MultiAgent Architecture Using LlamaIndex Workflows

LlamaIndex workflows are designed around an event-driven architecture. The workflow begins when a start event is triggered. Each step in the workflow is defined by a function decorated with the `@step` decorator. These step functions are activated by specific event types, which means the workflow decides which step to execute next based on the event it receives.

Each step processes its input event and returns a new event. This returned event determines the next step in the workflow. Steps can emit multiple events, and a single step can be set up to listen for several different event types. This flexibility allows multiple steps to run in parallel, enabling efficient and scalable workflows.

In the context of multi-agent systems, each step typically interacts with a different agent. By leveraging this event-driven approach, you can run several agents at the same time, making your workflow both modular and highly parallelized. This structure makes it easy to add, remove, or modify agents as your application evolves.

## Define Reviews Events

In this step, we will define `ReviewsEvent` and `ReviewsCompletedEvent` classes in `multi_agent_workflow.py` to handle events related to the Reviews Agent. These event classes allow the workflow to track and manage the execution and completion of the Reviews Agent.

!!! note "**File location:** `backend/src/agents/multi_agent_workflow.py` (or wherever your workflow/event classes are defined)"

!!! note "**Purpose:** These classes represent the start and completion of a review agent task, enabling event-driven orchestration in your workflow."

```python
class ReviewsEvent(Event):
    pass

class ReviewsCompletedEvent(Event):
    result: str
```

!!! note "**What this does:**"
    These event classes allow the workflow to track and manage the execution and completion of the Reviews Agent.

---

## Integrate Reviews Agent in MultiAgentFlow Class

In this step, we will update the `MultiAgentFlow` class to accept the Reviews Agent, emit and handle review events, and add a step function for the Reviews Agent. This will integrate the Reviews Agent into the multi-agent workflow, allowing it to be triggered, run, and its results to be handled like other agents.

!!! note "**File location:** `backend/src/agents/multi_agent_workflow.py`"

!!! note "**Purpose:** This step wires your Reviews Agent into the event-driven workflow, so it can be triggered, run, and its results handled like any other agent."

- **Accept `reviews_agent` in the MultiAgentFlow class constructor argument:**

    ```python
    reviews_agent: BaseAgent,
    ```

- **Assign it in the constructor body:**
    ```python
    self.reviews_agent = reviews_agent
    ```

- **Add `ReviewsEvent` to the planning step return type:**

    Any type of event that a step function can emit must be mentioned in its return type. We add the ReviewsEvent here so that it can be triggered by the planning agent.
    ```python
    # --- Update planning step return type ---
    ProductPersonalizationEvent | InventoryEvent | ReviewsEvent:
    ```

- **Trigger the Reviews Agent when needed:**

    By adding this code logic in the planning step function, it is able to trigger the review agent step function by emitting the ReviewsEvent.
    ```python
    # --- Trigger Reviews Agent ---
    if "reviews" in agents_to_call:
        ctx.send_event(ReviewsEvent())
        triggered_agents.append(ReviewsCompletedEvent)
    ```

- **Add a step function for the Reviews Agent:**

    This is the review agent step function, which is executed when the ReviewsEvent is emitted. Within this, we call the review agent that we previously defined in the agents/reviews_agent.py file.
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

- **Update the presentation step to accept `ReviewsCompletedEvent`:**


    The presentation agent accepts the outputs of agents and then refines them before eventually passing them to the frontend. We add the ReviewsCompletedEvent here so that the presentation agent accepts the output of the review agent step function. 
    ```python
    # --- Update presentation step signature ---
    ev: ProductPersonalizationCompletedEvent | InventoryCompletedEvent | ReviewsCompletedEvent,
    ```

!!! note "**What this does:**"
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
