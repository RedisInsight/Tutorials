RedisBloom is developed by Redis Inc., and adds probabilistic data structures, including a Bloom filter, to Redis. To install RedisBloom on top of an existing Redis, download and run [Redis Stack](https://redis.io/docs/stack/get-started/install/), which includes RedisBloom and other capabilities. Also check out [RedisBloom commands](https://redis.io/commands/?group=bf).

A Bloom filter is a probabilistic data structure that enables you to check if an element is present in a set using a very small memory space of a fixed size. When it responds that an element is not present in a set (a negative answer), you can be sure that indeed is the case. However, false-positive matches are possible.

If you receive unhelpful queries from the clients to the database because no keys are matched in Redis, you can use a Bloom filter to filter such queries.

These guidelines help you learn how to use a Bloom filter to reduce heavy calls to the relational database or memory.

## Avoiding cache penetration

* Before checking the cache, implement some logic (e.g., IP range filtering). If the same unacceptable addresses are queried repeatedly, you may consider storing these addresses in Redis with an empty string value.
* If you need to store millions of invalid keys, indeed, you may consider using a Bloom filter.
* Create a Bloom filter using `BF.RESERVE` and add invalid addresses using `BF.ADD`. To determine if an invalid address has been seen before, use `BF.EXISTS`. The answer `1` means that, with high probability, the value has been seen before. An `0` means that it definitely wasn't seen before.

## Handling incoming requests

Because false-positive matches are possible with a Bloom filter (BF), you can use these options to better handle incoming requests.

### Store all valid keys in a BF upfront

* Add all valid keys to the BF.
* When a request is received, search in the Bloom filter.
* If found in the BF, it is, with high probability, a valid key. Try to fetch it from the DB. If not found in the DB (low probability) it was a false positive.
* If not found in the BF, it is necessarily an invalid key.

### Store valid keys in a BF on the fly

* When a request is received, search in the Bloom filter.
* If found in the BF, it is, with high probability, a valid key that was already seen. Try to fetch it from the DB. If not found in the DB (low probability) it was a false positive.
* If not found in the BF - it is either a first-time valid key or an invalid key. Check, and if valid - add to the BF.


### Store invalid keys in a BF

* When a request is received, search in the Bloom filter.
* If found in the BF, it is, with high probability, an invalid key. Note that it may be a valid key (low probability) and you will ignore it, but that's a price you should be ready to pay if you go this way.
* If not found in the BF, it is either a valid key or a first-time invalid key. Check and, if invalid, add it to the BF.

## Notes

* You don't need to add an item to a BF more than once. There is no benefit, but also no harm.
* You can't delete keys from a BF, but you can use a Cuckoo filter instead, which supports deletions but has some disadvantages compared to BF. RedisBloom supports Cuckoo filters as well.
