**Triggers and functions** can execute server-side JavaScript functions that can either be called explicitly or are triggered by a keyspace change or stream entry arrival. This empowers developers to build and maintain real-time applications that sit closer to the data, ensuring lower latency while delivering the best developer experience.

To use triggers and functions, you need to write functions that describe how the data should be processed. Then, you register triggers that define the keyspace notifications or streams that will execute these functions.

Common examples of **Triggers and Functions**:

* [Automatic Expire](https://redis.io/docs/interact/programmability/triggers-and-functions/examples/#automatic-expiration?utm_source=redis\&utm_medium=app\&utm_campaign=redisinsight_triggers_and_functions_guide)
* [Enrich and transform data](https://redis.io/docs/interact/programmability/triggers-and-functions/examples/#enrich-and-transform-data?utm_source=redis\&utm_medium=app\&utm_campaign=redisinsight_triggers_and_functions_guide)
* [Batch operations](https://redis.io/docs/interact/programmability/triggers-and-functions/examples/#batch-operations?utm_source=redis\&utm_medium=app\&utm_campaign=redisinsight_triggers_and_functions_guide)

### Prerequisites

[Redis Stack Server](https://redis.io/download/?utm_source=redis\&utm_medium=app\&utm_campaign=redisinsight_triggers_and_functions_guide) >= 7.2.0-RC2 \
OR \
[Triggers and Functions](https://redis.io/docs/interact/programmability/triggers-and-functions/?utm_source=redis\&utm_medium=app\&utm_campaign=redisinsight_triggers_and_functions_guide) >=2.0 \
OR \
A free Redis Stack instance on [Redis Cloud](https://redis.com/try-free/?utm_source=redis\&utm_medium=app\&utm_campaign=redisinsight_triggers_and_functions_guide "Redis Cloud") in Google Cloud Asia Pacific (Tokyo) or AWS Asia Pacific (Singapore) in a Fixed subscription plan.
