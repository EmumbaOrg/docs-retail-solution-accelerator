# Multi-Agent System Overview

In this section of the guide, we'll introduce the foundational concepts and architecture behind multi-agent systems using **LlamaIndex**. These systems consist of multiple specialized agents working collaboratively to solve complex tasks by breaking them down into smaller, manageable parts.

## What You'll Learn

This section will help you understand the **building blocks** of a multi-agent system, how they are coordinated using workflows, and how to implement them using LlamaIndex's flexible, event-driven architecture.

You will learn about:

- **The role of agents** in AI-powered applications
- **Events** as the primary communication mechanism between agents
- **Workflow structure** for organizing agent interactions in a traceable and scalable manner

We will explore how these components are combined to form a structured, reactive pipeline that drives intelligent behavior across the system.

### What You'll Accomplish

By the end of this section, you will:

- Understand how to define and connect agents using **LlamaIndex Workflows**
- Review a multi-step agent flow that includes:
  - **Planner** that breaks down user input into tasks
  - **Review Agent** that analyzes product reviews using **Azure AI**
  - **Personalization Agent** that generates personalized content
  - **Evaluation Agent** that evaluates the response of other agents (only triggered when `Enable Self Correction` is set to true from frontend)
  - **Presentation Agent** that formats results for display
- Using memory to persist user preferences across sessions

This architecture brings together the full power of LlamaIndex, pg_diskann, Azure AI, mem0, and Apache AGE to support intelligent, explainable, and modular AI behavior within your application.
