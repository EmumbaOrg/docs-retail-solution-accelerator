# 7.0: Add tool for Search-with-Sentiment

In this step, we will add search-with-sentiment tool in the user_query_agent in `backend/src/agents/user_query_agent.py`. This utilizes the azure_ai and cypher query through 'get_feature' and 'fetch_product_with_feature_and_sentiment_count ' functions respectively.

> **File location:** `backend/src/agents/user_query_agent.py`
> 
> **Purpose:** This function acts as a tool for the user_query_agent (a.k.a command routing agent) to perform sentiment based search.

---

```python
async def query_reviews_with_sentiment(
        self,
        product_category: str,
        product_feature: str,
        sentiment: str,
    ):
        """
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


                products = products[: settings.PRODUCT_SEARCH_RESPONSE_SIZE]


                if products:
                    product_ids = [product.id for product in products]


        response_data = {
            "products": [product.model_dump() for product in products],
            "agent_action": UserQueryAgentAction.PRODUCT_SEARCH.value,
            "trace_id": convert_trace_id_to_hex(
                get_current_span().get_span_context().trace_id,
            ),
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

```

---

**What this does:**
This function searches for products based on a category and user query, retrieves a specific feature (using get_feature function), and filters products that have reviews mentioning that feature with a given sentiment (using fetch_product_with_feature_and_sentiment_count function). It then ranks and limits the results based on the number of relevant sentiment-feature mentions.

**'get-feature' function**
---
```python
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

```
---
**What this does:**
The get_feature function uses an Azure AI model to semantically match a given feature_name (e.g., "battery") to one of the predefined features in the database. It constructs a prompt string by aggregating all existing feature names into a structured schema, then passes this to the LLM to extract the best-matching feature. It returns the mapped feature name if a match is found.

**'fetch_product_with_feature_and_sentiment_count' function**
---
```python
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
```
---
**What this does:**
This function runs a Cypher query on the product_review_graph to find how many reviews of a given sentiment (positive, negative, or neutral) mention a specific feature for a list of product IDs. It returns a sorted list of product IDs with their corresponding sentiment-feature review counts in descending order.
