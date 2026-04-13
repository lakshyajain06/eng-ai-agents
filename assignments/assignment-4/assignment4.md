1. Deep Document Understanding vs Naive Chunking

Deep document understanding lets you create much smarter chunks than a naive chunking method. Naive methods might segment chunks based on simple things like character or word count. However this creates the problem of cutting off chunks mid sentence, or even just mid idea, and it is unable to account for tables. In contrast, deep document understanding actually groups things based on vision, so table data gets grouped together into chunks. This provides greater retrieval fidelity because more relevant data will be grouped together for retrieval rather than getting a partial context. With deep document understanding, each group gains a more semantic meaning. Each chunk is now variable sizes which gives the index a more significant meaning than simply this comes first and this next. The indexes might correspond to full ideas and such. However this all will come at the cost of preprocessing time. DeepDoc runs a vision based model which will require more computation to run than a naive parser that cuts based on length.

2. Template chunking vs Semantic Chunking

In template chunking, you will have a specific template of the documents you are trying to chunk. For example, resumes, scripts, and manuals all tend to have a very defined structure to the document that you can write in a template. This chunking method will look for things that match those rules and chunk based off of those. For example, each header in a document might correspond to a new chunk. However, this will fail to scale to general documents because there is no single template that works well for every kind of document. Embedding-driven semantic segmentation will look at the similarity between the embeddings of consecutive sentences, and starts a new chunk when it is above some threshold. This will work without clear headers or format but is reliant on ideal chunks flowing cleanly in terms of semantics. Highly structured documents should be chunked via template based because it is likely the format is strict and definable. On the other hand loosely structured corpora should be chunked with embedding-driven semantic segmentation.

3. 

Lexical only retrieves things based on strict key word retrieval. As a result it might fail on things that use similes or that might be phrased slightly differently. For example, if someone searches for “break a leg”, they might find results related to medical attention when what you mean to find is to hurry. Vector similarity on the other hand looks at the embedded semantic meaning. However this will fail when it encounters text it has never seen before when creating the encoder. For example, searching up specific error codes will not be possible with this because it is unable to understand them semantically. The hybrid method combines both of these by weighting the scores and combining them. This allows for greater recall because the individual methods cover for each other's inability to detect some things. It increases precision since it takes input from both, they can be used to cross verify essentially. An edge case might be something where the sentence contains many keywords like instructions to do x without y. It might pull up instructions to do x with y if the semantic encoder doesn’t perform well enough to account for the negation.

4.

The multi-stage pipeline allows you get more deep comprehension than a single pass ANN search with minimal latency costs. It first does fast lightweight searches to get a high recall option sample. Then it uses a more expensive model to rank the sample of options which allows you to perform this higher precision retrieval with a relatively low latency. However a risk arises that if an early stage of fails to retrieve good options, that error will propagate throughout the multi stage pipeline.

5. 

Elasticsearch-like hybrid store needs to give robust inverted indexing closely integrated with metadata filtering and vector search capabilities. It works best in workloads where keywords are best to search on. Essentially when specific language and syntax are important.

Vector-Native DB has to be optimized entirely for high dimensional math so that it can calculate vector distances with minimal latency. It performs best in searching where semantic meaning matters a lot.

A graph-augmented store needs to have a direct graph with relationships and vector embeddings to traverse that graph. Is best for tracing lines of reasoning.

6. 

Static query to retrieve will use an input directly as it is given. This can be a problem because a user's prompt will often lack context and will result in inaccurate retrieval. Agent driven iterative query refinement will intercept the prompt maybe with an LLM and fill it with context from chat history. It could also break up the question into “steps” and retrieve for each of the steps instead. This will help clean up and account for messy prompts.

7.

Dense vector spaces are good for finding things that are semantically similarly, but fails to expand that to reasoning that is composed of multiple steps. It also becomes very difficult to explain the retrieval because it is dependent on a vector space that is computed, but is very hard to translate back into what each dimension/distance means.

Relational schema is good at composing logic together so long as the things that need to be connected fit into the defined metadata. The retrieval is very explainable because you can look at the metadata and explain why the cell was chosen.

A knowledge graph is really good at compositional reasoning because it connects those reasoning steps as edges between nodes. You can also easily trace the path of the graph to explain a retrieval result.

8.
First I will convert different kinds of data into one standard format through schema normalization. This ensures the chunking and embedding models don’t have to consider where where the data came from. To avoid the massive computational cost of rebuilding the entire database every time something changes, the system should use incremental indexing. This looks at document hashes or timestamps to only update the specific chunks that were recently added or modified. When building this pipeline, you also have to balance consistency versus throughput. Prioritizing throughput means batching documents in the background, which handles large volumes well but means the data isn't searchable immediately. On the other hand, prioritizing consistency allows a user to query a document the exact second they upload it, but this will heavily bottleneck the system when there is a lot of traffic.


9.

Vector memory saves general themes using semantics but forgets strict facts and timelines. Structured memory extracts exact facts and entities into a database but fails with messy, unformatted conversation. Episodic memory keeps a raw chronological log of everything said, which is perfect for immediate context but will eventually overwhelm the system's context window in long conversations.

10.

I will separate the stateless services (parsing) from the stateful things (like database) to allow the stateless services to easily spin up and down without losing data. The parsing workers will scale based off of how many documents need to be parsed and the retrieval engine will essentially scale based on how many queries it receives from the user. I will separate the ingestion pipeline from the serving layer with a message queue. This will allow the retrieval engine to keep working even if ingestion workers crash.

