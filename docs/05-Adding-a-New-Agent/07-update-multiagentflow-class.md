# 5.8 🔄🧩 Update the MultiAgentFlow Class

In this step, you'll update the `MultiAgentFlow` class to accept the Reviews Agent, emit and handle review events, and add a step function for the Reviews Agent. This will integrate the Reviews Agent into the multi-agent workflow, allowing it to be triggered, run, and its results to be handled like other agents. Let's connect all the moving parts!

> **File location:** `src/agents/multi_agent_workflow.py`
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
These changes integrate the Reviews Agent into the multi-agent workflow, allowing it to be triggered, run, and its results to be handled like other agents. 🤝🚦

---

**Further Explanation:**

- 🧩 Each function decorated with `@step` is a workflow step, handling specific events and emitting new ones.
- 🔄 The Reviews Agent is now a modular, event-driven part of your system, ready to collaborate with other agents!
- 👀 You get observability and validation for every step, making debugging and extension a breeze.

You're building a truly flexible, intelligent system—great job!
