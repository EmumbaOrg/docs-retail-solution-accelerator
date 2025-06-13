# 7.2: Add tool for Search-with-Sentiment

In this step, we will add search-with-sentiment tool in the user_query_agent in `backend/src/agents/user_query_agent.py`. This utilizes the 'get_feature' and 'Fetch_product_with_feature_and_sentiment_count ' functions discussed in previous sections.

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
This function searches for products based on a category and user query (using product_search function), retrieves a specific feature (using get_feature function), and filters products that have reviews mentioning that feature with a given sentiment (using fetch_product_with_feature_and_sentiment_count function). It then ranks and limits the results based on the number of relevant sentiment-feature mentions.
