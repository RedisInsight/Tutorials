Redis sets are unsorted, which limits their usefulness. [Sorted sets](https://redis.io/docs/data-types/sorted-sets) are similar to sets, except each unique member has an associated score, which provides the mechanism for sorting.

The following example creates a set of bike brands named for famous computer programmers, using their birth year as the score element to sort the set.

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
