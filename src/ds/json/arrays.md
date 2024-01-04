Redis has several commands that allow you to manipulate JSON arrays. Simple documents will be used to demonstrate each command.

```redis Create a document
JSON.SET doc $ '{"a": [10, 20, 30, 40, 50]}'
```

- `JSON.ARRLEN` - get the length of an array:

```redis Get the length of $.a
JSON.ARRLEN doc $.a
```

- `JSON.ARRAPPEND` - append one or more values to an array:

```redis Append two values to $.a
JSON.ARRAPPEND doc $.a 60 '"foo"'
JSON.GET doc $.a
```

- `JSON.ARRPOP` - remove an element from an array:

```redis Remove the last item from $.a
JSON.ARRPOP doc $.a
JSON.GET doc $.a
```

Note: `JSON.ARRPOP` takes two optional arguments: a path and a position. If no path is given, then `$` is assumed.
Position is zero-based, with negative numbers representing last to first.
For example, `-1` mean the last element and `-2` means the second to last element.

- `JSON.ARRTRIM` - trim an array so that it contains only the specified inclusive range of elements.

```redis Trim $.a to just the first 3 elements
JSON.TRIM doc $.a 0 2
JSON.GET doc
```

Now, reset doc to it's original value and trim `$.a` to just the last two values:

```redis Re-create the document
JSON.SET doc $ '{"a": [10, 20, 30, 40, 50]}'
```

```redis Trim to the last two values
JSON.TRIM doc $.a -2 -1
JSON.GET doc
```

As discussed earlier in this tutorial, `JSON.MERGE` can be used to replace entire arrays.

```redis Replace $.a with a different set of values
JSON.MERGE doc $.a '["a", "b", "c"]'
JSON.GET doc
```