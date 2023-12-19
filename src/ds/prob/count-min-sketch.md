In this tutorial you will learn how to use a count-min sketch to track profits in a bike shop use case.

A count-min sketch is used to determine the frequency of events in a stream. You can query the count-min sketch to get an estimate of the frequency of any given event.

You can initialize the count-min sketch and specify a width (the number of counters in each array) and depth (the number of counter-arrays).

```redis Initialize a sketch with dimensions
CMS.INITBYDIM bikes:profits 2000 5 // initializes a count-min sketch with 2000 counters in each array and 5 counter-arrays

```

You can also initialize a count-min sketch by providing an acceptable error rate and probability for inflated (over) count for your use case:


```redis Initialize a sketch with tolerances
CMS.INITBYPROB bikes:profits 0.001 0.01 // initializes the count-min sketch with 0.1% estimation of error and 1% desired probability for inflated count
```

You can increase the count associated with an item by a supplied increment:

```redis Update
CMS.INCRBY bikes:profit "Smokey Mountain Striker" 100
CMS.INCRBY bikes:profit "Rocky Mountain Racer" 200 "Cloudy City Cruiser" 150
```

Retrieve the counts for several items from the sketch:

```redis Return query
CMS.QUERY bikes:profit "Smokey Mountain Striker" "Rocky Mountain Racer" "Cloudy City Cruiser" "Terrible Bike Name"
```

Notice that `CMS.QUERY` returns `0` if item doesn't exist.

Use the `CMS.INFO` command to get more information about the width, depth, and total count of your sketch.

```redis Information About The Sketch
CMS.INFO bikes:profit
```
