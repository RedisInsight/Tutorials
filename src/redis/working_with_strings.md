Redis [strings](https://redis.io/docs/data-types/strings) are used to store strings, numbers, and other kinds of information. They are the simplest of the Redis data types.

As a first example, use the [SET](https://redis.io/commands/set/) command to store the value "Deimos", a type of bike, at key "bike:1".

```redis Create a new key
SET bike:1 "Deimos"
```

Redis will store the `bike:1` data permanently. You can retrieve the data using the [GET](https://redis.io/commands/get/) command.

```redis Retrieve the data
GET bike:1
```

To see if a key exists, use the [EXISTS](https://redis.io/commands/exists) command.

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

All the Redis operations implemented by single commands are atomic, including those that operate on more complex data structures. So, when you use a command that modifies the value of a key, any concurrent operations will also succeed.

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

`EXPIRE` and `TTL` operate in seconds, but there are analogous commands that operate in milliseconds. They are [PEXPIRE](https://redis.io/commands/pexpire) and [PTTL](https://redis.io/commands/pttl).

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
