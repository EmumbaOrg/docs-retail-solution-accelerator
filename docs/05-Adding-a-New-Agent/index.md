# Adding a New Agent: Guide Overview

Welcome to the "Adding a New Agent" module! In this hands-on guide, you'll embark on an exciting journey to extend your multi-agent system by building and integrating a brand new Reviews Agent. This module is designed to give you practical experience with real-world tools and workflows, empowering you to customize and scale intelligent systems.

By the end of this guide, you'll have:
- Designed a specialized agent prompt
- Integrated your agent into the planning and presentation layers
- Built and registered a new agent in code
- Connected your agent to vector stores and query engines
- Leveraged LlamaIndex workflows for event-driven orchestration
- Enabled your agent to participate in multi-step, multi-agent flows

**Here's what you'll accomplish, step by step:**

1. **Add the Reviews Agent Prompt:** Craft a powerful prompt that guides your agent to generate insightful, user-focused review summaries.
2. **Update the Planning Agent Prompt:** Make your new agent discoverable and selectable by the system's planner.
3. **Update the Presentation Agent Prompt:** Ensure your agent is visible and accessible in the presentation layer.
4. **Define the Reviews Agent:** Build the core logic for your agent, connecting it to vector stores and query engines.
5. **Add Reviews Events:** Define the events that will trigger and track your agent's actions.
6. **Update the MultiAgentFlow Class:** Integrate your agent into the event-driven workflow, enabling seamless collaboration with other agents.
7. **Update Workflow Service:** Wire up your agent in the workflow service for full system integration.

---

Before we begin, let's examine the current workflow without the **Reviews Agent**. Reviewing the debug flow diagram below, you'll notice that the Reviews Agent is not yet part of the multi-agent architecture. This will help you understand the baseline system before we integrate the new agent.

![Workflow Without Reviews Agent](../img/workflow-without-review-agent.png)

---

Get ready to dive in, experiment, and see your new agent come to life as part of a robust, intelligent multi-agent system. Let's get started!
