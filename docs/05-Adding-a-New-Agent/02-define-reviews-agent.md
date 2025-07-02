# 5.2: Define the Review Agent.

In this step, we will create a new file `reviews_agent.py` in `backend/src/agents/` that defines the Reviews Agent and its logic. This file will set up the agent to use a vector store and LLM to retrieve and summarize product reviews.

!!! info "**File location:** `backend/src/agents/reviews_agent.py`"

!!! info "**Purpose:** Encapsulates all logic for the Reviews Agent, making it reusable and easy to maintain."

---

```python
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


!!! info "**What this does:**"
    This file defines the Reviews Agent, which uses a vector store and LLM to retrieve and summarize product reviews. It exposes a function to create and configure the agent.

---

## Expose the Agent in the Module Init

In this step, we will update `backend/src/agents/__init__.py` to import and expose the `get_reviews_agent` function. This makes the function available for import elsewhere in your codebase, allowing other modules to use the Reviews Agent.

!!! info "**File location:** `backend/src/agents/__init__.py`"

!!! info "**Purpose:** Register your agent so it can be easily imported and used throughout your project."

---

```python
# --- Expose the Reviews Agent ---
from .reviews_agent import get_reviews_agent

# Then add this in the list:
"get_reviews_agent",
```

!!! info "**What this does:**"
    This makes the `get_reviews_agent` function available for import elsewhere in your codebase.

---

**Additional Explanation:**

#### Vector Stores
A vector store is a storage system that holds embedding vectors of document chunks (and sometimes the document chunks themselves). When you ingest documents, they are split into smaller pieces (chunks), and each chunk is converted into a vector using an embedding model. These vectors are then stored in the vector store, enabling efficient similarity search and retrieval.

In the code, we use a vector store to store and retrieve product review embeddings. This allows the agent to efficiently find and retrieve relevant review chunks based on the user’s query and preferences. 

!!! note "For more details, see the [LlamaIndex documentation on Vector Stores.](https://docs.llamaindex.ai/en/stable/module_guides/storing/vector_stores/)"

#### Query Engine
A query engine is a generic interface in LlamaIndex that allows you to ask questions over your data. It takes a natural language query and returns a rich response, often by retrieving relevant chunks from one or more indexes (such as a vector store) and generating an answer using an LLM. Query engines can be composed to provide more advanced capabilities.

In the code, we create a query engine from the vector store index. This query engine is configured to use the LLM, embedding model, and filters, and is responsible for retrieving the most relevant product review chunks and generating a summary or answer based on them. 

!!! note "For more details, see the [LlamaIndex documentation on Query Engine.](https://docs.llamaindex.ai/en/stable/module_guides/deploying/query_engine/)"

#### Query Engine Tool
A Query Engine Tool is a wrapper that allows a query engine to be used as a tool within an agent workflow. It provides metadata (such as a name and description) and exposes the query engine’s capabilities to the agent, so the agent can invoke it as needed.

In the code, we wrap the query engine in a `QueryEngineTool`, providing it with a name and description. This tool is then passed to the agent, enabling the agent to use the query engine to retrieve and summarize product reviews as part of its workflow.

!!! note "For more details, see the [LlamaIndex documentation on Query Engine Tools](https://docs.llamaindex.ai/en/stable/examples/agent/openai_agent_with_query_engine/)."
