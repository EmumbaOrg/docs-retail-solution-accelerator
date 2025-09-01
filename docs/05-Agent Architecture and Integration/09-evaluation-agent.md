# 5.9 Evaluation Agent

Our Agentic Workflow includes an Evaluation Agent that reviews the outputs of other agents to detect any faulty or inappropriate information before it reaches the final response. If a discrepancy is found, the Evaluation Agent automatically retriggers the agent to generate a corrected output. Currently set up specifically for the Review Agent, this mechanism ensures that only accurate and user-appropriate information is presented in the final workflow results.   

The Evaluation Agent is activated when `Enable Self Correction` is set to true from the frontend. Then, during the workflow, some irrelevant information is intentionally added to the Review Agent's output to demonstrate the correction process. The Evaluation Agent then detects this faulty output and automatically retriggers the Review Agent to generate a revised response. Let's walk through the implementation details and see an example of the Evaluation Agent in action.

## Implementation

- Evaluation Agent prompt defined in `src/agents/prompts.py`:

    ```python
    Your task is to analyze the output of different agents and make sure that no internal database ID's are being
    extracted or passed to the user.

    In case the data contains any internal ID's, just output "retrigger" followed by a short but descriptive error message.
    In case the data doesn't contain any internal ID's, just output "ok".
    ```
- Evaluation Agent defined in `src/agents/evaluation_agent.py`:
    ```python
    def get_evaluation_agent(llm: BaseLLM):
        return FunctionAgent(
            name=AgentNames.EVALUATION_AGENT.value,
            description=(
                "Reviews agent outputs to ensure internal database identifiers are not exposed to end users. "
                "Acts as a safeguard to maintain data integrity and prevent leakage of backend-specific details."
            ),
            llm=llm,
            system_prompt=EVALUATION_PROMPT,
            tools=[],
            verbose=settings.VERBOSE,
            allow_parallel_tool_calls=False,
        )
    ```


- The `evaluate_output` step function creates an instance of the Evaluation Agent, provides it with the current output from the Review Agent, and analyzes the response. If the Evaluation Agent identifies any issues, it automatically retriggers the Review Agent to produce a corrected output.

    
    `src/workflows/multi_agent_workflow.py`: 
    ```python
    @step
    async def evaluate_output(
        self,
        ctx: Context,
        ev: EvaluationEvent,
    ) -> ReviewsCompletedEvent | ReviewsEvent:

        agent_output = ev.result

        try:
            result = await self.evaluation_agent.run(
                f"Review the following output: \
                output={agent_output}",
            )

            logger.info("Evaluation Result: %s", result)

            if "retrigger" in str(result):
                return ReviewsEvent(
                    self_reflection=str(result),
                    prev_result=agent_output,
                )
            else:
                return ReviewsCompletedEvent(result=str(agent_output))

        except WorkflowTimeoutError:
            logger.info("Evaluation Agent has timed out.")

        return ReviewsCompletedEvent(result=str(agent_output))
    ```

- When `Enable Fault Correction` is set to true on the frontend, we deliberately add some irrelevant information to the output of the Review Agent in order to show that it is caught down the line by the Evaluation Agent. The following code within the Review Agent step function achieves this.

    review step function in `src/workflows/multi_agent_workflow.py`:
    ```python
    if self.fault_correction and not ev.self_reflection:
                # To mock faulty output...
                # Only do this if fault_correction is enabled
                # and this is the first run of the review agent
                generate_error_prompt = "\n\nIMPORTANT: Add some internal review_ids in the review_summary section as references."
    ```

    trigger `evaluation_output` step function from the review step function
    ```python
    if self.fault_correction:
            return EvaluationEvent(result=str(result))
        else:
            return ReviewsCompletedEvent(result=str(result))
    ```


## Example

Let's go through an example run and see the Evaluation Agent in action.

- Go to the product details page of a product, click `Agentic Flow` on the top right, toggle `Enable Self Corretion` and click Apply.

    ![Self Correction Toggle.](../img/self_correction_toggle.png)

    The workflow will be retriggered.

- Once completed, click on `Agentic Flow`. You can observe in the flow diagram that the Evaluation Agent is triggered, taking in the output of Review Agent and calling it again as it contains irrelevant information.

    ![Evaluation agent flow diagram.](../img/evaluation_agent_flow_diagram.png)

- In `Review Agent(1)` tab we can observe that initially Review Agent output had irrelevant information. 
    ![Review Agent 1.](../img/review_agent_1.png)

- `Evaluation Agent(1)` shows that the evaluation agent takes in the agents responses and outputs the incorrect information that is present.
    ![Evaluation Agent 1.](../img/evaluation_agent_1.png)

- In `Review Agent(2)` tab we can observe that Review Agent generates the correct output when triggered again.
    ![Review Agent 2.](../img/review_agent_2.png)

- Finally in `Evaluation Agent(2)` we can see that the evaluation agent verifies that now the output doesnt contain the irrelevant information
    ![Evaluation Agent 2.](../img/evaluation_agent_2.png)    

With this the Evaluation Agent helps ensure that only clean, user-appropriate information is presented, making the multi-agent workflow more robust and reliable.