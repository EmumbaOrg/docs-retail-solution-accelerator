# 4.3 Tool-Enabled Agent: Intelligent Query Router

In this section, we explore a powerful enhancement to our multi-agent setup—introducing the User Query Agent, an agent equipped with multiple tools. Unlike earlier agents that perform a single role, this one unifies multiple capabilities under a single interface, allowing it to dynamically respond to different types of user queries.

Where previous agents handled focused tasks like personalization or inventory lookup, this agent handles broader input and delegates work to appropriate tools (including triggering the workflow we discussed in previous section).

You can expand the section below to review the implementation of user query agent (Query Router).

???- info "Query Router Agent Implementation"

    ```python linenums="407" title="backend/src/agents/user_query_agent.py"
    import asyncio
    from typing import Optional

    from fastapi import BackgroundTasks
    from llama_index.core.agent.workflow import FunctionAgent
    from llama_index.core.base.embeddings.base import BaseEmbedding
    from llama_index.core.llms.function_calling import FunctionCallingLLM
    from llama_index.core.memory.types import BaseMemory
    from llama_index.core.tools import FunctionTool
    from llama_index.core.vector_stores.types import MetadataFilter, MetadataFilters
    from openinference.instrumentation.llama_index import get_current_span
    from sqlalchemy import text
    from sqlalchemy.ext.asyncio import AsyncSession
    from src.agents.prompts import USER_QUERY_AGENT_PROMPT
    from src.config.config import settings
    from src.logging import logger
    from src.schemas.enums import AgentNames, EventType, StatusEnum, UserQueryAgentAction
    from src.services.agent_workflow import MultiAgentWorkflowService
    from src.services.product_search import product_search
    from src.utils import get_user_session_key
    from src.utils.utils import convert_trace_id_to_hex, set_personalization_status
    from src.workflows.utils import run_workflows_in_background, send_stream_event


    class UserQueryAgent:
        def __init__(
            self,
            db: AsyncSession,
            user_query: str,
            user_id: int,
            memory: BaseMemory,
            llm: FunctionCallingLLM,
            embed_model: BaseEmbedding,
            message_queue: asyncio.Queue,
            background_tasks: BackgroundTasks,
            vector_store_products_embeddings: BaseEmbedding,
            vector_store_reviews_embeddings: BaseEmbedding,
            product_id: Optional[int] = None,
        ):
            self.db = db
            self.user_query = user_query
            self.user_id = user_id
            self.product_id = product_id
            self.background_tasks = background_tasks

            self.llm = llm
            self.embed_model = embed_model
            self.vector_store_products_embeddings = vector_store_products_embeddings
            self.vector_store_reviews_embeddings = vector_store_reviews_embeddings
            self.message_queue = message_queue
            self.memory = memory

            self.client_id = get_user_session_key(user_id)

        def create_agent(self):
            return FunctionAgent(
                name=AgentNames.USER_QUERY_AGENT.value,
                description="Acts as a smart router that interprets user queries and directs them to the most appropriate tool.",
                system_prompt=USER_QUERY_AGENT_PROMPT,
                tools=self._get_tools(),
                llm=self.llm,
                verbose=settings.VERBOSE,
            )

        async def query_about_product(self, query: str) -> dict:
            """
            Executes an instruction provided by the user about the product he is viewing.
            For example generating summary of product reviews, regenerating product personalization,
            querying inventory information etc.
            This function is triggered when the user wants to update his personalization.

            Examples queries that this tool answer:
                "Highlight feedback about bluetooth connectivity"
                "Show summary of critical reviews about durability"
                "Is this product available in red"
                "Generate a personalized section for me"

            Parameters:
                query (str): A string containing the command or directive to execute.
            Returns:
                A confirmation or result indicating the outcome of the executed command.
            """

            await send_stream_event(
                {
                    "message": "Your personalized section is being generated",
                },
                EventType.PERSONALIZATION_WORKFLOW.value,
                self.product_id,
                self.message_queue,
            )

            trace_id = convert_trace_id_to_hex(  # Save User Query Agent trace
                get_current_span().get_span_context().trace_id,
            )
            try:
                personalized_section = await self._run_multi_agent_workflow(
                    self.user_query,
                    trace_id,
                )
                await send_stream_event(
                    personalized_section,
                    EventType.PERSONALIZATION_WORKFLOW.value,
                    self.product_id,
                    self.message_queue,
                )
            except Exception as e:
                logger.exception("Error in handle_command: %s", e)
                await send_stream_event(
                    {"message": str(e)},
                    EventType.ERROR.value,
                    self.product_id,
                    self.message_queue,
                )
            finally:
                # Ensure we always send the end signal
                await self.message_queue.put(None)

        async def search_products(self, product_category: str) -> None:
            """
            Executes a product search query
            This function executes a vector query over the product dataset based on
            the provided query string.

            Examples queries that this tool answer:
                "Wireless headphones"
                "Tablets with 8GB RAM"
                "Earphones with bluetooth"
                "Fitness Smartwatches"

            Parameters:
                product_category (str): Specifies the category of the product being queried.
                Possible values are: "headphones", "tablets", or "smartwatch", or None.
            """
            # To get product_category we can also use azure_ai semantic operator

            logger.info("product search invoked product_category=%s", product_category)

            products = []
            product_ids = []
            message = ""
            if product_category:
                results = await product_search(
                    self.db,
                    self.vector_store_products_embeddings,
                    self.llm,
                    self.embed_model,
                    self.user_query,
                    self.user_id,
                    product_category,
                )

                products = results[: settings.PRODUCT_SEARCH_RESPONSE_SIZE]
                if results:
                    product_ids = [product.id for product in products]
                else:
                    message = "No products found matching your query."

            response_data = {
                "products": [product.model_dump() for product in products],
                "agent_action": UserQueryAgentAction.PRODUCT_SEARCH.value,
                "trace_id": convert_trace_id_to_hex(
                    get_current_span().get_span_context().trace_id,
                ),
                "message": message,
            }
            await send_stream_event(
                response_data,
                EventType.PRODUCT_SEARCH.value,
                self.product_id,
                self.message_queue,
            )
            await run_workflows_in_background(
                self.db,
                self.user_id,
                product_ids,
                self.llm,
                self.embed_model,
                self.memory,
                self.vector_store_products_embeddings,
                self.vector_store_reviews_embeddings,
                self.background_tasks,
            )
            return response_data

        async def query_reviews_with_sentiment(
            self,
            product_category: str,
            product_feature: str,
            sentiment: str,
        ):
            """response_data
            This function searches products within a specified category based on review sentiment
            related to a specific product feature. It combines product attributes and sentiment
            analysis to surface relevant reviews.

            Examples queries that this tool answer:
                "Headphones with positive reviews about noise cancellation"
                "Tablet with negative reviews about fast charging"

            Parameters:
                product_category (str): Specifies the category of the product being queried.
                Possible values are: "headphones", "tablets", or "smartwatch", or None.
                product_feature (str): The product feature to focus on (e.g., "noise cancellation", "battery life").
                sentiment (str): Desired sentiment to filter reviews by ("positive", "neutral", "negative").
            """

            logger.info(
                "sentiment query invoked product_category=%s, product_feature=%s, sentiment=%s",
                product_category,
                product_feature,
                sentiment,
            )

            products = []
            product_ids = []
            message = ""
            if product_category:
                results = await product_search(
                    self.db,
                    self.vector_store_products_embeddings,
                    self.llm,
                    self.embed_model,
                    self.user_query,
                    self.user_id,
                    product_category,
                )

                feature = await self.get_feature(product_feature)
                if feature:
                    product_ids_for_query = [product.id for product in results]
                    product_ids_with_count = (
                        await self.fetch_product_with_feature_and_sentiment_count(
                            product_ids_for_query,
                            sentiment,
                            feature[0],
                        )
                    )
                    logger.info(
                        "product_ids_with_count=%s, feature=%s",
                        product_ids_with_count,
                        feature,
                    )
                    products = self._get_products_by_ids(
                        results,
                        product_ids_with_count,
                    )

                    if products:
                        products = products[: settings.PRODUCT_SEARCH_RESPONSE_SIZE]
                        product_ids = [product.id for product in products]
                    else:
                        logger.warning(
                            "No products found matching the feature and sentiment criteria."
                        )
                        products = results[: settings.PRODUCT_SEARCH_RESPONSE_SIZE]
                        product_ids = [product.id for product in results]
                        message = "We could not find review-based matches. Here are some similar products."

            response_data = {
                "products": [product.model_dump() for product in products],
                "agent_action": UserQueryAgentAction.PRODUCT_SEARCH.value,
                "trace_id": convert_trace_id_to_hex(
                    get_current_span().get_span_context().trace_id,
                ),
                "message": message,
            }

            await send_stream_event(
                response_data,
                EventType.PRODUCT_SEARCH.value,
                self.product_id,
                self.message_queue,
            )
            await run_workflows_in_background(
                self.db,
                self.user_id,
                product_ids,
                self.llm,
                self.embed_model,
                self.memory,
                self.vector_store_products_embeddings,
                self.vector_store_reviews_embeddings,
                self.background_tasks,
            )

            return response_data

        async def get_feature(self, feature_name: str) -> Optional[tuple[int, str]]:
            query = text(
                f"""
                WITH feature_schema AS (
                    SELECT
                        'productFeature: string - A feature of a product. Features: ' ||
                        STRING_AGG(fx.feature_name, ', ' ORDER BY fx.feature_name) || ' or NULL' AS feature_schema,
                        ARRAY_AGG(fx.id) AS feature_ids,
                        ARRAY_AGG(fx.feature_name) AS feature_names
                    FROM features fx
                )
                    SELECT
                        (azure_ai.extract('{feature_name}', ARRAY[(SELECT feature_schema FROM feature_schema)],
                        '{settings.LLM_MODEL}')::JSONB->>'productFeature') AS mapped_feature
                """,
            )

            result = await self.db.execute(query)
            return result.first()

        async def fetch_product_with_feature_and_sentiment_count(
            self,
            product_ids: list[str],
            sentiment: str,
            feature_name: Optional[str] = None,
        ) -> list[tuple]:
            if not product_ids or not feature_name:
                return []

            sentiments_map = {
                "positive": "positive_sentiment",
                "negative": "negative_sentiment",
                "neutral": "neutral_sentiment",
            }
            sentiment = sentiments_map.get(sentiment, "positive_sentiment")
            logger.info("Sentiment being passed to cypher query: %s", sentiment)

            await self.db.execute(text('SET search_path = ag_catalog, "$user", public;'))
            cypher_query = text(
                f"""
                    SELECT * FROM ag_catalog.cypher('product_review_graph', $$
                        MATCH (p:Product), (f:Feature), (r:Review)
                        WHERE p.id IN {product_ids} AND f.name = '{feature_name}'
                        MATCH (p)-[frel:HAS_FEATURE]->(f)
                        MATCH (f)<-[rel:{sentiment} {{product_id: p.id}}]-(r)
                        RETURN p.id AS product_id, COUNT(rel) AS positive_review_count
                    $$) AS graph_query(product_id agtype, positive_review_count agtype)
                    ORDER BY (graph_query).positive_review_count DESC;
                """,
            )
            result = await self.db.execute(cypher_query)
            return result.fetchall()

        def _get_tools(self) -> list:
            tools_func = [
                self.query_about_product,
                self.search_products,
                self.query_reviews_with_sentiment,
            ]
            return [
                FunctionTool.from_defaults(tool, return_direct=True) for tool in tools_func
            ]

        async def _run_multi_agent_workflow(
            self,
            command: str,
            trace_id: str,
        ) -> None:
            logger.info(
                "Starting multi-agent workflow with command: %s, user_id: %s, product_id: %s",
                command,
                self.user_id,
                self.product_id,
            )

            filters = MetadataFilters(
                filters=[
                    MetadataFilter(key="product_id", value=self.product_id),
                ],
            )
            workflow_service = MultiAgentWorkflowService(
                user_id=self.user_id,
                product_id=self.product_id,
                db=self.db,
                llm=self.llm,
                embed_model=self.embed_model,
                vector_store_products_embeddings=self.vector_store_products_embeddings,
                vector_store_reviews_embeddings=self.vector_store_reviews_embeddings,
                filters=filters,
                memory=self.memory,
                message_queue=self.message_queue,
            )
            await set_personalization_status(
                self.db,
                self.user_id,
                self.product_id,
                StatusEnum.running,
            )
            response, _ = await workflow_service.run_workflow(
                user_query=command,
            )
            await workflow_service.save_workflow_response(
                response,
                trace_id,
            )
            return response

        def _get_products_by_ids(
            self,
            products: list,
            product_ids_with_count: list,
        ) -> list:
            product_id_set = [int(row[0]) for row in product_ids_with_count]
            filtered_products = [
                next(product for product in products if product.id == product_id)
                for product_id in product_id_set
            ]
            return filtered_products
    ```

### 🔧 What Makes This Agent Special?

Unlike earlier agents, the **User Query Agent** is initialized with three tools:

| Tool Name                   | Purpose                                                                 |
|----------------------------|-------------------------------------------------------------------------|
| `query_about_product`      | Triggers the multi-agent personalization workflow for the current user |
| `search_products`          | Performs a vector search over product embeddings                       |
| `query_reviews_with_sentiment` | Combines sentiment and feature filters to search reviews graphically     |


These tools are defined as callable Python functions and wrapped into LlamaIndex `FunctionTool` objects. The agent then uses the LLM to choose the correct tool based on user intent.

```python
FunctionAgent(
    name="user_query_agent",
    description="Smart router that interprets user queries and directs them to the most appropriate tool.",
    system_prompt=USER_QUERY_AGENT_PROMPT,
    tools=[...],  # Three tool functions
    llm=llm,
    verbose=settings.VERBOSE,
)
```

---

### How It Works

When a query reaches this agent, here’s the high-level flow:

1. **LLM Reasoning**: The LLM receives the user query and system prompt. Based on the tool metadata, it selects the most relevant tool to invoke.
2. **Tool Invocation**: The corresponding Python function is executed:
   - For **sentiment-based queries**, it performs a vector + graph query.
   - For **general product searches**, it uses embedding search.
   - For **personalization flows**, it dispatches a background multi-agent workflow.
3. **Streaming Response**: Each tool emits events back to the frontend via a message queue for real-time updates.

!!! note "This setup demonstrates how a single LLM-powered agent can identify user intent in real time and route the query to the most appropriate tool, enabling more natural interactions and unlocking dynamic, intent-driven experiences."

---

### Example Queries & Tool Actions

You can experiment with this agent directly from the Product Listing or Product Detail Page. Just type in a query that reflects your interest or concern, and the agent will intelligently route your request to the most suitable tool.

| Query Example                                                  | Tool Triggered               |
|----------------------------------------------------------------|------------------------------|
| “Wireless headphones with positive reviews about noise cancellation”                         | `query_reviews_with_sentiment` |
| “Waterproof headphones”                                    | `search_products`           |
| “Show me summary of positive reviews about durability” (on product page)     | `query_about_product`       |


!!! info  "Why Some Queries May Show General Results"
    In some cases, certain search queries may not return any results. For example, a query like "Tablets with good battery" might not yield results if there is no direct association between the feature ("good battery") and the product category ("tablets") in the available data. This can occur when there are no reviews or feature mappings that connect the queried feature to products in the specified category.

    To ensure a better user experience, a fallback mechanism is implemented. If the initial search does not return any products, a general vector search within the relevant category (e.g., "tablets") is performed to provide available products from that category.
