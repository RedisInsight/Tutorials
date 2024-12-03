## Vector search

Data is often unstructured, which means that it isn't described by a well-defined schema.

Examples of unstructured data include:
- Text passages
- Images
- Videos
- Music titles

One approach to dealing with unstructured data is to vectorize it. Vectorizing means mapping unstructured data to a flat sequence of numbers.

Vectors represent the data embedded in an N-dimensional space.

Machine learning models have facilitated the rise of embeddings as a widely embraced method for generating dense, low-dimensional vector representations.

Given a suitable machine learning model, the generated embeddings can encapsulate complex patterns and semantic meanings inherent in data.

You can use Redis Stack as a vector database, which allows you to:

- Store vectors and the associated metadata within hashes or JSON documents
- Retrieve vectors
- Perform vector searches


### Prerequisites

[Redis Stack](https://redis.io/downloads/?utm_source=redisinsight&utm_medium=app&utm_campaign=vss_tutorial) >=7.2.0-v7 \
OR \
[RediSearch](https://github.com/RediSearch/RediSearch/) >=2.8.11 \
OR \
A free Redis Stack instance on [Redis Cloud](https://redis.com/try-free/?utm_source=redisinsight&utm_medium=app&utm_campaign=vss_tutorial).
