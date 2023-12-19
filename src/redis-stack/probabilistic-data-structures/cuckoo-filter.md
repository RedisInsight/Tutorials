In this tutorial you will learn how to use a cuckoo filter, a resource efficient data structure, in a bike shop use case.

[Cuckoo filters](https://en.wikipedia.org/wiki/Cuckoo_filter), which are similar to Bloom filters, are used to determine, with a high degree of certainty, whether or not an element is a member of a set. Unlike Bloom filters, you can delete items from a cuckoo filter.

To begin, create a cuckoo filter with estimated capacity, defined bucket size (the number of items in each bucket), and maximum number of iterations (number of attempts to swap items between buckets).

```redis Create a cuckoo filter
CF.RESERVE bikes:models 100 BUCKETSIZE 10 MAXITERATIONS 2 // creates a “cuckoo” filter for the initial amount of 100 items, 10 items per bucket and 2 iterations to swap items before creating an additional filter
```

Next, add items to the cuckoo filter.
Note that `CF.ADD` creates a new filter if one doesn't already exist.

```redis Add Items
CMS.ADD bikes:models "Smokey Mountain Striker"
CMS.ADD bikes:models "Cloudy City Cruiser"
CMS.ADD bikes:models "Rocky Mountain Racer"
CMS.ADD bikes:models "Smokey Mountain Striker" // second addition of Smokey...
```

Use the `CF.EXISTS` command to determine whether or not an item may exist in the cuckoo filter.

```redis Check if item may exist
CF.EXISTS bikes:models "Smokey Mountain Striker" // returns “1”, the item may exist
CF.EXISTS bikes:models "Non-existant model" // returns “0”, the item certainly does not exist
```

Cuckoo filters support deletion. Here's an example.

```redis Delete an item
CF.DEL bikes:models "Smokey Mountain Striker" // delete the "Smokey Mountain Striker" item once from the filter
```

If an item was added more than once to a filter, and you delete it once, the item will still be present.

You also can see the number of times an item may be in the filter.
Note that because this is a probabilistic data structure, the count may not necessarily be accurate.

```redis Count Items
CF.COUNT bikes:models "Smokey Mountain Striker"
```

Use the `CF.INFO` command to retrieve information about your cuckoo filter:

```redis Information about the filter
CF.INFO cuckoo
```
