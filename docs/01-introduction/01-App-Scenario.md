## 1.1 The App Scenario

### Personalizing E-Commerce with GenAI and Multi-Agent Systems

In the e-commerce space, delivering a personalized shopping experience has become essential to retaining users and increasing conversion. However, most personalization systems are rule-based or rely on shallow behavioral tracking. These approaches struggle to adapt in real-time to user preferences and product attributes derived from unstructured data such as reviews.

The **AgenticShop** accelerator presents a solution that combines **Generative AI**, **multi-agent orchestration**, and **in-database intelligence** to provide personalized and responsive product discovery experiences.

This solution uses a curated electronics dataset—including products, reviews, and user profiles—to simulate real-world personalization scenarios. The system allows users to perform intelligent product searches, analyze reviews for sentiment and features, and update their personalization profile—all in a seamless experience driven by AI agents.

The backend is built using **Azure Database for PostgreSQL – Flexible Server**, enhanced with:

- The **pg_diskann** extension for high-performance vector search
- The **azure_ai** extension for in-database NLP tasks like sentiment and key phrase extraction
- The **Apache AGE** extension for modeling product–review–feature relationships as a graph

**Multi-agent workflows** powered by **LlamaIndex** handle complex tasks such as routing queries, analyzing reviews, and updating user preferences with persistent memory (via [Mem0](https://mem0.ai)). The system includes observability and tracing using **Phoenix by ArizeAI** for full transparency into agent behavior and performance.

### Agentic Query Routing with a Smart Search Box

In addition to personalized workflows, **AgenticShop** features a unified search interface powered by agents and tools, capable of interpreting user queries in multiple intelligent ways.

Behind this single input box, an **Agent equipped with three tools** dynamically selects the appropriate strategy based on the user’s intent. This enables:

**Personalization Updates**
The agent detects if the user is expressing preferences (e.g., *"I prefer lightweight laptops with good battery life"*) and updates their profile in the memory layer (**Mem0**), triggering downstream personalization workflows.

**Standard Vector Search**
For product lookups or general discovery queries (e.g., *"Show me wireless earbuds"*), a **pg_diskann-based vector search** retrieves the most semantically relevant results.

**Sentiment-Aware Search**
For queries like *"Find laptops with positive reviews about battery life"*, the agent combines vector search with in-database **sentiment analysis** and **feature extraction** using **azure_ai**, then optionally filters or reranks results based on extracted review insights.

This intelligent routing architecture allows users to interact naturally with the system, while the agents ensure their intent is correctly interpreted and processed.

