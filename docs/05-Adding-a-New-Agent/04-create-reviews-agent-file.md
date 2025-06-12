# 5.5 🏗️🗂️ Define the Review Agent in a Separate File

In this step, you'll create a new file `review_agent.py` in `src/agents/` that defines the Reviews Agent and its logic. This file will set up the agent to use a vector store and LLM to retrieve and summarize product reviews. Get ready to build the brains of your new agent!

> **File location:** `src/agents/review_agent.py`
> 
> **Purpose:** Encapsulates all logic for the Reviews Agent, making it reusable and easy to maintain.

---

```python
# --- Imports ---
from llama_index.core import VectorStoreIndex
from llama_index.core.agent.workflow import FunctionAgent
from llama_index.core.base.embeddings.base import BaseEmbedding
from llama_index.core.base.llms.base import BaseLLM
from llama_index.core.tools import QueryEngineTool, ToolMetadata
from llama_index.core.vector_stores.types import (
    BasePydanticVectorStore,
    MetadataFilters,
)
from src.agents.prompts import REVIEWS_AGENT_PROMPT
from src.config.config import settings
from src.schemas.enums import AgentNames


def get_reviews_agent(
    llm: BaseLLM,
    embed_model: BaseEmbedding,
    vector_store: BasePydanticVectorStore,
    filters: MetadataFilters,
):
    """
    Create and return a Reviews Agent for summarizing product reviews.
    Args:
        llm: The language model to use.
        embed_model: The embedding model for vector search.
        vector_store: The vector store containing review embeddings.
        filters: Metadata filters for narrowing search results.
    Returns:
        Configured FunctionAgent for product review summarization.
    """

    # 1. Create an index from the vector store
    index = VectorStoreIndex.from_vector_store(
        vector_store=vector_store,
        embed_model=embed_model,
    )

    # 2. Create a query engine for searching and summarizing reviews
    query_engine = index.as_query_engine(
        similarity_top_k=settings.TOP_K,
        verbose=settings.VERBOSE,
        use_async=True,
        llm=llm,
        filters=filters,
    )

    # 3. Wrap the query engine as a tool for the agent
    query_engine_tools = [
        QueryEngineTool(
            query_engine=query_engine,
            metadata=ToolMetadata(
                name="product_reviews_summarization",
                description=(
                    "Retrieves, summarizes and answers questions about customer reviews for a specified product."
                ),
            ),
        ),
    ]

    # 4. Create and return the FunctionAgent
    return FunctionAgent(
        name=AgentNames.REVIEWS_AGENT.value,
        llm=llm,
        tools=query_engine_tools,
        verbose=settings.VERBOSE,
        allow_parallel_tool_calls=False,
        system_prompt=REVIEWS_AGENT_PROMPT,
    )
```

---

**What this does:**
This file defines the Reviews Agent, which uses a vector store and LLM to retrieve and summarize product reviews. It exposes a function to create and configure the agent. 🧠🤩

---

**Additional Explanation:**

- 🗂️ **Vector Stores:** Store and retrieve product review embeddings for efficient search.
- 🔍 **Query Engine:** Lets you ask questions over your data and get rich, LLM-powered answers.
- 🛠️ **Query Engine Tool:** Wraps the query engine so your agent can use it as a tool in its workflow.

All these pieces work together to make your agent smart, fast, and ready to help!
