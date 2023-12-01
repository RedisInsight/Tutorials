Lists, sets, and sorted sets are great for many use cases, but the [hash](https://redis.io/docs/data-types/hashes) data type is on a higher level. Redis hashes are maps (sequences of key-value pairs) and are used to represent objects in Redis. Consider the following example that shows how to create a hash key using the [HSET](https://redis.io/commands/hset) command.

```redis Create a hash
HSET bike:1 model Deimos brand Ergonom type "Enduro bikes" price 4972
```

`HSET` returns the number of added key/value pairs.

To retrieve the stored data, use the [HGETALL](https://redis.io/commands/hgetall) command.

```redis HGETALL usage
HGETALL bike:1
```

If you only want the values of a subset of the fields, use the [HGET](https://redis.io/commands/hget) command.

```redis HGET usage
HGET bike:1 price
```

Numerical values in hash keys can be incremented in the same way as simple string keys using the [HINCRBY](https://redis.io/commands/hincrby) command.

```redis Hash INCRBY usage
HINCRBY bike:1 price 100
HINCRBY bike:1 price -100
```

See [here](https://redis.io/commands/?group=hash) for the entire set of Redis hash-related commands.

**Note**:
> If you're more familiar with JSON and want to use it with Redis, there is a [JSON data type](https://redis.io/docs/data-types/json) that is provided as part of Redis Stack. There's also a tutorial titled "Working with JSON" that is bundled with RedisInsight.
