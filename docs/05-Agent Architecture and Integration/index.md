# Agent Architecture and Integration: Guide Overview

Welcome to "Agent Architecture and Integration"! In this guide, you'll get an in-depth overview of how the Reviews Agent is integrated within the multi-agent system. This section explains the architecture, implementation, and codebase details, so you can understand how the agent operates and interacts with other components in the system.

By the end of this guide, you'll understand:

- How a specialized agent prompt is designed
- How agents are integrated into the planning and presentation layers
- How new agents are registered in code
- How agents connect to vector stores and query engines
- How LlamaIndex workflows enable event-driven orchestration
- How agents participate in multi-step, multi-agent flows

**Here's what is involved, step by step:**

1. **Reviews Agent Prompt:** How the prompt is crafted to generate insightful, user-focused review summaries.
2. **Planning Agent Integration:** How the Reviews Agent is made discoverable and selectable by the system's planner.
3. **Presentation Agent Integration:** How the agent is surfaced in the presentation layer.
4. **Reviews Agent Definition:** The core logic for the agent, including connections to vector stores and query engines.
5. **Reviews Events:** The events that trigger and track the agent's actions.
6. **MultiAgentFlow Integration:** How the agent is incorporated into the event-driven workflow for seamless collaboration.
7. **Workflow Service Integration:** How the agent is wired into the workflow service for full
---


Let's deep dive into the implementation and code-level details to understand how the Reviews Agent operates within the multi-agent system.
