Redis supports **probabilistic data structures** that provide reasonable yet approximate results quickly and with significantly less memory overhead than their deterministic counterparts.
The following data structures trade perfect accuracy for extreme memory efficiency, making them especially useful for big data and streaming applications:

* Bloom filter
* Cuckoo filter
* Count-Min sketch
* Top-k

**Bloom filter:**
* Enables you to check if an element is present in a set using a fixed size (sublinear) memory space, sacrificing some precision.
* Uses multiple hash functions to create a representation of the element in a bit array.
* Answers the question "Is this element present in the set?".
* A negative answer means that the element is definitively not present in a set.
* A positive answer means that the element is most likely present in the set. Note: false positives are possible.
* The error rate can be configured when the filter is created.

**Cuckoo filter:**
* Fulfills the same mission as Bloom filters, but also allow for item deletion.
* Provides better results than Bloom filters under certain circumstances.

**Count-min sketch**
* Estimates the frequency of events/elements in a stream of data.
* Consumes a stream of events/elements and keeps estimates of their frequency.
* Can only be trusted for counts above a certain threshold.
* Biased: results can be higher than reality, but never lower.

**Top k**
* Used to estimate the **k** highest-rank elements in a stream.
* Optimized for large flows; biased on small flows.
* Provide notification when an element is removed from the top k list.
* Discards older approaches like "count-all" and "admit-all-count-some" in favor of a "count-with-exponential-decay" strategy.

### Prerequisites

[Redis Stack Server](https://redis.io/download) >=7.2.0-v7 \
OR \
[RedisBloom](https://oss.redis.com/redisbloom/) >=2.6.10 \
OR \
A free Redis Stack instance on [Redis Cloud](https://redis.com/try-free/?utm_source=redis\&utm_medium=app\&utm_campaign=redisinsight_probabilistic_guide "Redis Cloud").
