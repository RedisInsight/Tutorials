In addition to a comprehensive set of basic data structures, Redis supports the following probabilistic data structures:

- Bloom filter
- Cuckoo filter
- Count-min sketch
- Top-K
- t-digest
- Hyperloglog

In this tutorial you will learn how to use a Bloom filter, a resource efficient data structure, in a bike shop use case.

A Bloom filter allows you to check if an element is present in a set in a memory-efficient and scalable way. A query will return one of two possible answers:

1. the element *might* be in the set
2. the element is definitely not in the set

In other words, a Bloom filter will guarantee the absence of an element in a set, but it can only give an estimation about its presence. False positives are entirely possible. See [this Wikipedia article](https://en.wikipedia.org/wiki/Bloom_filter) for more detailed information about false positives and their frequency.

Despite the uncertainty involved when using Bloom filters, they are still valuable for many applications.

How can a Bloom filter be helpful to an online bike shop service? For starters, you can use a Bloom filter to store the usernames of people who've already registered with the shop. When someone creates a new account, the system can very quickly check if the user's proposed username is available. If the answer is yes, you can confirm using your primary database. But, if the answer is no, further checks are not required and registration can proceed.

Another use case is targeting ads to users. A per-user Bloom filter can be created and populated with all the products each user has purchased from the shop. When the shop's ad suggestion engine provides a list of possible ads to show a user, it can check each item against the user's Bloom filter. Each item that is not part of the filter are good targets. For each item that might already be part of the filter, a second query can be made to the primary database to confirm. If the second confirmation is negative, then that ad can be added to the target list.

First, create the Bloom filter.

```redis Add all bought product IDs to a Bloom filter
BF.MADD user:778:bought_products  4545667 9026875 3178945 4848754 1242449
```

Next, run a couple of queries.

```redis Has a user bought this product?
BF.EXISTS  user:778:bought_products 1234567  // No, the user has not bought this product
BF.EXISTS  user:778:bought_products 3178945  // The user might have bought this product
```

You can read more about Bloom filters and their use cases [here](https://redis.io/docs/data-types/probabilistic/bloom-filter/). See [here](https://redis.io/commands/?group=bf) for the complete list of Bloom filter commands.