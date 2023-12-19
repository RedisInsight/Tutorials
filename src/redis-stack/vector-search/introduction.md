You can index and query vector data stored in Redis [Hashes](https://redis.io/topics/data-types#hashes) and [JSON](https://redis.io/topics/data-types#json). This capability extends Redis's ability to index NUMERIC, GEO, and TEXT data stored in hashes and JSON documents.

Vector search powers advanced search applications that operate on unstructured data.

A growing number of pre-trained computer vision and natural language processing models allow you to generate vector representations of images, videos, audio, and long pieces of text.

Some of the most common applications built on top of vector search are:

* E-Commerce Visual Similarity: Recommending "visually similar" products within a price range or geo-location.
* E-Commerce Semantic Similarity: Recommending "semantically similar" products matching a description provided by an end user.
* Chatbots / Q&A systems: Finding the most similar questions (and their answers) to a question posted by an end user.

### Prerequisites

[Redis Stack](https://redis.io/download) >=6.2.2-v0 \
OR \
[RediSearch](https://oss.redis.com/redisearch/) >=2.4.3 \
OR \
A free Redis Stack instance on [Redis Cloud](https://redis.com/try-free/?utm_source=redis\&utm_medium=app\&utm_campaign=redisinsight_vecsim_guide "Redis Cloud").
