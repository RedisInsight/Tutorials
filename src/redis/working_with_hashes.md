Lists, sets, and sorted sets are great for many use cases, but the hash data type is on a higher level. Redis hashes are maps (sequences of field-value pairs) and are used to represent objects in Redis. Consider the following example that shows how to create a hash key using the `HSET` command.

```redis Create a hash
HSET bike:1 model Deimos brand Ergonom type "Enduro bikes" price 4972
```

`HSET` returns the number of added key/value pairs.

To retrieve the stored data, use the `HGETALL` command.

```redis HGETALL usage
HGETALL bike:1 // returns all the field-value pairs associated with the key
```

If you only want the values of a subset of the fields, use the `HGET` command.

```redis HGET usage
HGET bike:1 price
```

You can update fields in a hash using `HSET` by specifying a subset of its field-value pairs.

```redis Update an existing hash
HSET bike:1 model "Kraken" price 3000
```

Use the `HDEL` command to delete one or more fields from a hash. Once all fields are removed, the hash key itself will be deleted.

```redis Delete hash fields and keys
HDEL bike:1 model
HGETALL bike:1
HDEL bike:1 brand type price
HGETALL bike:1
EXISTS bike:1
```

Numerical values in hash keys can be incremented in the same way as simple string keys using the `HINCRBY` command.

```redis Hash INCRBY usage
HINCRBY bike:1 price 100
HINCRBY bike:1 price -100
```

See [here](https://redis.io/docs/data-types/hashes) for the hash type reference page, and [here](https://redis.io/commands/?group=hash) for the entire set of Redis hash commands.

**Note**:
> If you're more familiar with JSON and want to use it with Redis, there is a [JSON data type](https://redis.io/docs/data-types/json) that is provided as part of Redis Stack. There's also a tutorial titled "Working with JSON" that is bundled with RedisInsight.
