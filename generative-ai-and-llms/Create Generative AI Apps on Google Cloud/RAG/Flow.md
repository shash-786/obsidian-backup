![[Data Ingestion.png]]
Data Ingestion

1) Data is uploaded to a Cloud Storage bucket. The data source might be an application user performing an upload, database ingestion, or streaming data.
2) When data is uploaded, a notification is sent to a Pub/Sub topic.
3) Pub/Sub triggers a Cloud Run job to process the uploaded data.
4) Cloud Run starts the job by using configuration data that's stored in an AlloyDB for PostgreSQL database.
5) The Cloud Run job uses Document AI to prepare the data for further processing.For example, the preparation can include parsing the data, converting the data to the required format, and dividing the data into chunks.
6) Cloud Run stores the embeddings in an AlloyDB for PostgreSQL database that has the pgvector extension(opens in a new tab) enabled. When the serving subsystem processes user requests, it uses the embeddings in the vector database to retrieve relevant domain-specific data.

![[Serving Subsystem.png]]

Serving Subsystem

1) Users submit requests to the generative AI application through a frontend (for example, a chatbot or mobile app).
2) The generative AI application converts the natural-language request to embeddings. **Note:** The generative AI app should use the same embeddings model and parameters used by the data ingestion subsystem.
3) The application performs a semantic search for the embedding in the AlloyDB for PostgreSQL vector store that's maintained by the data ingestion subsystem. Semantic search helps find embeddings based on the intent of a prompt rather than its textual content. The application combines the original request with the raw data that's retrieved based on the matching embedding to create a contextualized prompt.
4) The application sends the contextualized prompt to an LLM inference stack that runs on Vertex AI.
5) The large language model (LLM) inference stack uses a generative AI LLM, which can be a foundation LLM(opens in a new tab) or a custom LLM, and generates a response that's constrained to the provided context.
6) The application can store logs of the request-response activity in Cloud Logging. You can view and use the logs for monitoring using Cloud Monitoring.
7) The application sends the responses to BigQuery for offline analytics.
8) The application screens the responses by using responsible AI filters.
9) The application sends the screened responses to users through the frontend.


![[Quality Subsystem.png]]
Quality evaluation subsystem

1) An evaluation is triggered by sending a Pub/Sub message to a Pub/Sub topic.
2) Pub/Sub triggers a Cloud Run job.
3) Cloud Run starts the job by using configuration data that's stored in an AlloyDB for PostgreSQL database.
4) The Cloud Run job pulls evaluation prompts from an AlloyDB for PostgreSQL database. The prompts were previously uploaded to the database by the data ingestion subsystem.
5) The Cloud Run job uses the evaluation prompts to assess the quality of the responses that the serving subsystem generates.
6) Cloud Run stores the evaluation scores and the prompts and responses that were evaluated to BigQuery for future analysis.