Modifying JSON documents is straightforward using a combination of JSONPath expressions and Redis's JSON command set.

Here are some examples.

## Extend documents

```redis Add a new key:value pair to an existing document
JSON.SET bicycle:1 $.newkey '"value"'
```

You could also use `JSON.MSET` to create or update multiple documents at the same time. First, delete `$.newkey` from `bicycle:1` using `JSON.DEL`.

```redis Delete $.newkey from bicycle:1
JSON.DEL bicycle:1 $.newkey
```

Next, add `$.newkey` to all three bicycles:

```redis Add $.newkey too all three bicycles using JSON.MSET
JSON.MSET bicycle:1 $.newkey '"value1"' bicycle:2 $.newkey '"value2"' bicycle:3 $.newkey '"value3"'
JSON.MGET bicycle:1 bicycle:2 bicycle:3 $.newkey
```

## Manipulate numeric values

There are two commands that allow you to perform arithmetic operations on components of documents:

- `JSON.NUMINCRBY` - adds a value to a numeric field.

```redis Decrease the price of bicycle:1
JSON.GET bicycle:1 $.price
JSON.NUMINCRBY bicycle:1 $.price -10
JSON.GET bicycle:1 $.price
```

- `JSON.NUMMULTBY` - multiply a numeric field by a value.

```redis Discount bicycle:1 by 10%
JSON.NUMMULTBY bicycle:1 $.price 0.9
```
## Manipulate string and boolean values

Appending information to JSON strings is straightforward.

```redis Append a string bicycle:1's model
JSON.STRAPPEND bicycle:1 $.model '"naut"'
JSON.GET bicycle:1 $.model
```

The `JSON.TOGGLE` command can be used to toggle boolean values.

```redis Toggle the value of bicycle:1's helmet_included value
JSON.GET bicycle:1 $.helmet_included
JSON.TOGGLE bicycle:1 $.helmet_included
JSON.GET bicycle:1 $.helmet_included
```

## Deeper document manipulation

As you saw earlier, the `JSON.MERGE` command can be used to create new documents. Additionally, it can also be used for the following use cases:

- Create a non-existant path-value:

```redis Add a new field-value pair to bicycle:1
JSON.MERGE bicycle:1 $.newkey2 '"value 2 1"'
JSON.GET bicycle:1
```

- Replace an existing value:

```redis Change bicycle:1's model back to Jigger
JSON.MERGE bicycle:1 $.model '"Jigger"'
JSON.GET bicycle:1 $.model
```

- Delete an existing value:

```redis Delete newkey2 from bicycle:1
JSON.MERGE bicycle:1 $ '{"newkey2": null}'
JSON.GET bicycle:1
```

- Replace an array:

```redis Replace bicycle:1's addons
JSON.MERGE bicycle:1 $.addons '["reflectors", "rainbow seat"]'
JSON.GET bicycle:1 $.addons
```

- Make changes to multiple paths (no example).

## Delete information

You can delete field-value pairs using the `JSON.DEL` or `JSON.FORGET` commands:

```redis Delete newkey from bicycle:1
JSON.DEL bicycle:1 $.newkey
JSON.GET bicycle:1
```

The `JSON.CLEAR` command will empty all arrays and set all numeric values to zero. A simple example will illustrate how this works.

```redis JSON.CLEAR usage
JSON.SET doc $ '{"obj":{"a":1, "b":2}, "arr":[1,2,3], "str": "foo", "bool": true, "int": 42, "float": 3.14}'
JSON.CLEAR doc $.*
JSON.GET doc $
```

As with all Redis keys, you can use the `DEL` command to delete keys entirely.