The JSON capability of Redis Stack provides JavaScript Object Notation (JSON) support for Redis, which allows Redis to function as a document database.
It lets you store, update, and retrieve JSON values in a Redis database, similar to any other Redis data type. Redis JSON also works seamlessly with Search and Query to let you index and query JSON documents.

Primary features include:

- Full support for the JSON standard.
- [JSONPath](https://goessner.net/articles/JsonPath/) syntax for selecting/updating elements inside documents.
- Documents are stored as binary data in a tree structure, allowing fast access to sub-elements.
- Typed atomic operations for all JSON value types.