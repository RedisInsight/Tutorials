Redis is an open source, in-memory data structure store, which provides data structure types such as strings, lists, sets, sorted sets, hashes, and more. It's also widely known as a key-value store.

The essence of a key-value store is the ability to store some data (value) and associate it with a key. Stored data can then be retrieved by its key name.

## Strings

As a first example, use the [SET](https://redis.io/commands/set/) command to store the value "Process 123" at key "bike:1".

```redis Create a new key
SET bike:1 "Deimos"
```

Redis will store the `bike:1` data permanently. You can retrieve the data using the [GET](https://redis.io/commands/get/) command.

```redis Retrieve the data
GET bike:1
```

To see if key exists, use the [EXISTS](https://redis.io/commands/exists) command.

```redis Do these keys exist?
EXISTS bike:1
EXISTS bike:2
```

The `EXISTS` command returns `1` when a key exists and `0` when it does not exist. The `bike:1` key exists, so Redis returns a value of `1`. The `bike:2` key does not exist, so Redis returns `0`.

Use the [DEL](https://redis.io/commands/del) to delete a key and its data.

## Strings as counters

If you store integer data in a string key, you can use these commands to increment and decrement its value:

- [INCR](https://redis.io/commands/incr) - increment a number stored in a key by `1`.
- [INCRBY](https://redis.io/commands/incrby) - increment a number stored in a key by a specific positive or negative amount.
- [DECR](https://redis.io/commands/decr) - decrement a number stored in a key by `2`.
- [DECRBY](https://redis.io/commands/decrby) - decrement a number stored in a key by a specific positive or negative amount.

```redis INCR and DEL usage
SET bikes:total_number 10
INCR bikes:total_number
INCR bikes:total_number
DEL bikes:total_number
INCR bikes:total_number
```

Note how the `INCR bikes:total_number` command that follows the `DEL` command re-creates the `bikes:total_number` key and sets its value to `1`.

```redis INCRBY, DECR, and DECRBY usage
INCRBY bikes:total_number 100
DECR bikes:total_number
DECRBY bikes:total_number 10
```

Using any of the `INCR*` or `DECR*` commands to modify the value of a key is an atomic operation. If two clients were to increment a key's value simultaneously, both operations would increment the key.

All the Redis operations implemented by single commands are atomic, including the ones that operate on more complex data structures. So, when you use a command that modifies the value of a key, you don't have to worry about concurrency.

## Key expiration

When keys are created without constraints, they live for the full lifespan of the Redis server to which they are attached. However, if you want a key to live for only a specific amount of time, you can use the [EXPIRE](https://redis.io/commands/expire) command to alter its lifespan. To find out how much time is left before a key expires, use the [TTL](https://redis.io/commands/ttl) command.

```redis EXPIRE usage
SET bike:1:lock_status "LOCKED"
EXPIRE bike:1:lock_status 120
```

After a few seconds, check the time-to-live status by running the `TTL` command.

```redis Check the bike:1:lock_status key
TTL bike:1:lock_status
```

You'll see one of the two return values:

1. the number of seconds left before the key expires
2. `-2`, indicating that the key has already expired

If you try to use the `TTL` command on a key that does not have an expiration set, it will return `-1`.

```redis TTL on a non-expiring key
SET bike:2:lock_status "LOCKED"
TTL bike:2:lock_status
```

`EXPIRE` and `TTL` operate in seconds, but there are analogous commands that operation in milliseconds. They are [PEXPIRE](https://redis.io/commands/pexpire) and [PTTL](https://redis.io/commands/pttl).

The `SET` command can take additional arguments, one of which allows you to set the value of a key and its time to live value directly in a single, atomic operation. The `EX` and `PX` arguments allow you to specify the TTL value as either seconds or milliseconds, respectively.

```redis SET with time to live
SET bike:1:lock_status "LOCKED" PX 1
```

It's possible to cancel a key's timeout using the [PERSIST](https://redis.io/commands/persist) command.

```redis PERSIST usage
SET bike:1:lock_status "LOCKED" EX 120
PERSIST bike:1:lock_status
TTL bike:1:lock_status
```

See the [SET](https://redis.io/commands/set) command manual page for more time-related and other arguments.

See [here](https://redis.io/commands/?group=string) for the entire set of Redis string-related commands.

## Lists

Redis supports many more complex data structures, including lists, which will be discussed next.

A Redis [list](https://redis.io/docs/data-types/lists) is a series of ordered values. You interact with lists using commands such as:

- [RPUSH](https://redis.io/commands/rpush) - adds items to the end of a list.
- [LPUSH](https://redis.io/commands/lpush) - adds items to the beginning of a list.
- [LLEN](https://redis.io/commands/llen) - retrieves the length of a list.
- [LRANGE](https://redis.io/commands/lrange) - retrieves list items from a specified range.
- [LPOP](https://redis.io/commands/lpop) - removes and returns an item from the beginning of a list.
- [RPOP](https://redis.io/commands/rpop) - removes and returns an item from the end of a list.

You can begin working with a list without first creating its key, simply by adding values to the key. This works as long as the key doesn't already exist as a different type.

**Note**:
> This is generally true for every Redis data structure, though there are a few exceptions.

If a key's values are removed entirely, the key will be removed from the keyspace.

In the following example, a list key is created and then a second element is added to it using the `RPUSH` command. The length of the list is returned after each command.

```redis Create a list with two elements
RPUSH bike:colors "Blue"
RPUSH bike:colors "White"
```

Next, an element is added to the beginning of the list.

```redis Prepend a new element
LPUSH bike:colors "Red"
```

When creating a list, there's a short cut to the above examples. Both `LPUSH` and `RPUSH` accept a variable number of argments (variadic), so you can create the entire list with a single command. The number of added elements is returned.

```redis Add multiple elements
DEL bike:colors
RPUSH bike:colors "Red" "Blue" "White" "Yellow"
```

The `LRANGE` command returns a subset of a key's elements. You provide two arguments in addition to the key name:

1. the index of the first element; note Redis lists use zero-based indexes
2. the index of the last element

For (2), a value of `-1` means the last element in the list; `-2` means the penultimate (second to last) element, and so on.

To return the entire list use `LRANGE keyname 0 -1`.

```redis LRANGE usage
LRANGE bike:colors 0 -1
LRANGE bike:colors 1 2
LRANGE bike:colors 1 -2
```

To find out how many elements are in a list, use the `LLEN` command.

```redis LLEN usage
LLEN bike:colors
```

The `LPOP` and `RPOP` commands will remove and return one or more elements from the beginning or end of a list, respectively.

In this next example, you will remove a single element from the beginning of the list, then a single element from the end of the list, and, finally, the remaining items using a `count` argument.

```redis LPOP/RPOP usage
LPOP bike:colors
LRANGE bike:colors 0 -1
RPOP bike:colors
LRANGE bike:colors 0 -1
LPOP bike:colors 2
LRANGE bike:colors 0 -1
KEYS bike:colors
```

**Note**:
> `LRANGE` will return an empty (null) list if a key no longer exists.

See [here](https://redis.io/commands/?group=list) for the entire set of Redis list-related commands.

## Sets

Redis [sets](https://redis.io/docs/data-types/sets/) are similar to lists, except they are unordered, and each element must be unique. You can use sets to:

- Track unique items (e.g., track all unique IP addresses accessing a given blog post).
- Represent relations (e.g., the set of all users with a specified role).
- Perform common set operations such as intersection, union, and difference.

Here are some of the important set commands:

- [SADD](https://redis.io/commands/sadd)
- [SREM](https://redis.io/commands/srem)
- [SISMEMBER](https://redis.io/commands/sismember)
- [SMEMBERS](https://redis.io/commands/smembers)
- [SUNION](https://redis.io/commands/sunion)

Use `SADD` to create and update a set. Each `SADD` command will return the number of added members. If you try to add a member that is already present in the set, `0` is returned.

```redis Create a set
SADD bike:1:addons "whitewall tires"
SADD bike:1:addons "bell" "reflectors"
SADD bike:1:addons "bell"
```

Notice that the `SADD` command is variadic.

`SREM` is used to remove members of a list. It returns `1` if the member is present in the set, or `0` if it is not.

```redis Remove set members
SREM bike:1:addons "bell"
SREM bike:1:addons "sissy bar"
```

The `SISMEMBER` command is used to test set membership. It returns `1` if the member is present, and `0` otherwise.

```redis Test set membership
SISMEMBER bike:1:addons "reflectors"
SISMEMBER bike:1:addons "sissy bar"
```

Use `SMEMBERS` to return all the members of a set.

```redis Get set members
SMEMBERS bike:1:addons
```

`SUNION` combines two or more sets and returns all their elements.

```redis SUNION usage
SADD bike:1:special_addons "sparkle coat finish" "banana seat"
SUNION bike:1:addons bike:1:special_addons
```

Sets have two ways to return one or more random members of a set:

- [SPOP](https://redis.io/commands/spop)
- [SRANDMEMBER](https://redis.io/commands/srandmember)

Each command takes a key and, optionally, a count as arguments. `SPOP` removes and returns a random member, whereas `SRANDMEMBER` just returns the randomly selected members without removing them.

```redis SPOP/SRANDMEMBER usage
SMEMBERS bike:1:addons
SRANDMEMBER bike:1:addons
SPOP bike:1:addons
SMEMBERS bike:1:addons
```

See [here](https://redis.io/commands/?group=set) for the entire list of Redis set-related commands.

## Sorted sets

While handy, Redis sets are unsorted, which limits their usefulness. It is for this reason that Redis sorted sets were added to its stable of data types.

Sorted sets are similar to sets, except each unique member has an associated score.

You can use sorted sets to create, for example, a set of bike brands named after famous computer programmers, where the score element, the programmer's birth year, is used to sort the set.

```redis Create a sorted set
ZADD bike:brands 1940 "Alan Kay"
ZADD bike:brands 1906 "Grace Hopper"
ZADD bike:brands 1953 "Richard Stallman"
ZADD bike:brands 1965 "Yukihiro Matsumoto"
ZADD bike:brands 1916 "Claude Shannon"
ZADD bike:brands 1969 "Linus Torvalds"
ZADD bike:brands 1957 "Sophie Wilson"
ZADD bike:brands 1912 "Alan Turing"
```

Now you can use the [ZRANGE](https://redis.io/commands/zrange) command to retrieve members in the order of birth year.

```redis ZRANGE usage
ZRANGE bike:brands 2 4
```

A popular use case for sorted sets is leaderboards for games.

See [here](https://redis.io/commands/?group=sorted-set) for the entire list of Redis sorted set commands.

## Hashes

Lists, sets, and sorted sets cover a lot of ground in terms of use cases, but the [hash](https://redis.io/docs/data-types/hashes) data type is on a higher level. Redis hashes are maps (sequences of key-value pairs) and are used to represent objects in Redis. Consider the following example that shows how to create a hash key using the [HSET](https://redis.io/commands/hset) command.

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

## Wrap up

There are many more Redis data structure types that you can use for a variety of use cases. See [the Redis documentation](https://redis.io/docs/data-types) for the entire list of supported data types.