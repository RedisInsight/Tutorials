
t-digest is a probabilistic data structure that can be used to answer the following questions:

* Which fraction of the values in the data stream are smaller than a given value?

* How many values in the data stream are smaller than a given value?

* Which value is smaller than p percent of the values in the data stream? (what is the p-percentile value)?

* What is the mean value between the p1-percentile value and the p2-percentile value?

* What is the value of the nth smallest / largest value in the data stream? (what is the value with [reverse] rank n)?

Using t-digest in Redis is simple and straightforward. Follow these examples to learn how.

## Create a sketch and add observations

You can simply create a t-digest with `TDIGEST.CREATE` and add observations with `TDIGEST.ADD`.

`TDIGEST.CREATE key [COMPRESSION compression]` initializes a new t-digest sketch (and error if such key already exists). You use the `COMPRESSION` argument to specify the tradeoff between accuracy and memory consumption. The default is 100. Higher values mean more accuracy.

`TDIGEST.ADD key value...` adds a new floating-point value (observation) to the sketch.

Let’s create a digest named t with compression 1000 (very accurate) and add 15 observations.

```redis Create a digest
TDIGEST.CREATE t COMPRESSION 1000
TDIGEST.ADD t 1 2 2 3 3 3 4 4 4 4 5 5 5 5 5
```

You can repeat calling `TDIGEST.ADD` whenever new observations are available.

## Estimate fractions or ranks by values

Use TDIGEST.CDF value... to retrieve, for each input value, an estimation of the fraction of (observations smaller than the given value + half the observations equal to the given value).

```redis Estimate fractions
TDIGEST.CDF t 0 1 2 3 4 5 6
1) "0"
2) "0.033333333333333333"
3) "0.13333333333333333"
4) "0.29999999999999999"
5) "0.53333333333333333"
6) "0.83333333333333337"
7) "1"
```

As you can see, all the estimations in this simple example are accurate.

`TDIGEST.RANK key value...` is similar to `TDIGEST.CDF`, but used for estimating the number of observations instead of the fraction of observations. More accurately, it returns, for each input value, an estimation of the number of observations smaller than a given value + half the observations equal to the given value.

`-1` is returned when value is smaller than the value of the smallest observation

The number of observations is returned when the value is larger than the value of the largest observation. 
Otherwise, an estimation of the number of observations smaller than a given value + half the observations equal to the given value is returned. 
The estimations are rounded to the nearest integer before being reported.

``` redis Use TDIGEST.RANK
TDIGEST.RANK t 0 1 2 3 4 5 6
1) "-1"
2) "1"
3) "2"
4) "5"
5) "8"
6) "13"
7) "15"
```

Again, all estimations in this example are accurate.

And lastly, `TDIGEST.REVRANK key value...` is similar to `TDIGEST.RANK` but returns, for each input value, an estimation of the number of observations larger than a given value + half the observations equal to the given value.

`-1` is returned when value is larger than the value of the largest observation.

The number of observations is returned when the value is smaller than the value of the smallest observation.
Otherwise, an estimation of the number of observations larger than a given value + half the observations equal to the given value is returned. 
The estimations are rounded to the nearest integer before being reported.

```redis Use TDIGEST.REVRANK
TDIGEST.REVRANK t 0 1 2 3 4 5 6
1) "15"
2) "14"
3) "13"
4) "10"
5) "7"
6) "2"
7) "-1"
```

`TDIGEST.RANK(v) + TDIGEST.REVRANK(v)` for any `v` between the minimum and the maximum observation is equal to the number of observations.

## Estimate values by fractions or ranks

`TDIGEST.QUANTILE key fraction...` returns, for each input fraction, an estimation of the value (floating point) that is smaller than the given fraction of observations.

```redis Use TDIGEST.QUANTILE
TDIGEST.QUANTILE t 0 0.1 0.2 0.3 0.4 0.5 0.6 0.7 0.8 0.9 1
 1) "1"
 2) "2"
 3) "3"
 4) "3"
 5) "4"
 6) "4"
 7) "4"
 8) "5"
 9) "5"
10) "5"
11) "5"
``` 

`TDIGEST.BYRANK key rank...` returns, for each input rank, an estimation of the value (floating point) with that rank.

Let's use `n` to denote the number of observations added to the sketch. `TDIGEST.BYRANK` returns:

* An accurate result when rank is `0` (the value of the smallest observation).

* An accurate result when rank is `n-1` (the value of the largest observation).

* `inf` when rank is equal to `n` or larger than `n`.

```redis Use TDIGEST.BYRANK
TDIGEST.BYRANK t 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15
 1) "1"
 2) "2"
 3) "2"
 4) "3"
 5) "3"
 6) "3"
 7) "4"
 8) "4"
 9) "4"
10) "4"
11) "5"
12) "5"
13) "5"
14) "5"
15) "5"
16) "inf"
``` 

`TDIGEST.BYREVRANK key rank...` returns, for each input reverse rank, an estimation of the value (floating point) with that reverse rank.

Let's use `n` to denote the number of observations added to the sketch. `TDIGEST.BYREVRANK` returns:

* An accurate result when rank is `0` (the value of the largest observation).

* An accurate result when rank is `n-1` (the value of the smallest observation).

* -inf when rank is equal to `n` or larger than `n`.

```redis Use TDIGEST.BYREVRANK
TDIGEST.BYREVRANK t 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15
 1) "5"
 2) "5"
 3) "5"
 4) "5"
 5) "5"
 6) "4"
 7) "4"
 8) "4"
 9) "4"
10) "3"
11) "3"
12) "3"
13) "2"
14) "2"
15) "1"
16) "-inf"
``` 

## Estimate trimmed mean

`TDIGEST.TRIMMED_MEAN key lowFraction highFraction` estimate the mean value between the specified fractions. 

This is especially useful for calculating the average value ignoring outliers, for example, the average value between the 20th percentile and the 80th percentile.

```redis Estimate trimmed mean
TDIGEST.TRIMMED_MEAN t 0.2 0.8
"3.8888888888888888"
TDIGEST.TRIMMED_MEAN t 0.1 0.9
"3.7692307692307692"
TDIGEST.TRIMMED_MEAN t 0  1
"3.6666666666666665"
``` 

## Merge sketches

Sometimes it is useful to merge sketches. 
Suppose you measure latencies for 3 servers, and you want to calculate the 90%, 95%, and 99% latencies for all the servers combined.

`TDIGEST.MERGE destKey numKeys sourceKey... [COMPRESSION compression] [OVERRIDE]` merges multiple sketches into a single sketch.

If `destKey` does not exist, a new sketch is created.

If `destKey` is an existing sketch, its values are merged with the values of the source keys. To override the destination key contents, use `OVERRIDE`.

When `COMPRESSION` is not specified:

* If `destKey` does not exist or if `OVERRIDE` is specified, the compression is set to the maximum value among all source sketches.

* If `destKey` already exists and `OVERRIDE` is not specified, its compression is not changed.

```redis Merge sketches
TDIGEST.CREATE s1
TDIGEST.ADD s1 1 2 3 4 5
TDIGEST.CREATE s2
TDIGEST.ADD s2 6 7 8 9 10
TDIGEST.MERGE sM 2 s1 s2
TDIGEST.BYRANK sM 0 1 2 3 4 5 6 7 8 9 10
 1) "1"
 2) "2"
 3) "3"
 4) "4"
 5) "5"
 6) "6"
 7) "7"
 8) "8"
 9) "9"
10) "10"
11) "inf"
``` 

## Retrieve sketch information

Use `TDIGEST.MIN` and `TDIGEST.MAX` to retrieve the minimum and maximum values in the sketch, respectively.
Both return `nan` when the sketch is empty.

```redis Retrieve sketch info
TDIGEST.MIN t
"1"
TDIGEST.MAX t
"5"
```

Both commands return accurate results and are equivalent to `TDIGEST.BYRANK key 0` and `TDIGEST.BYREVRANK key 0`, respectively.

Use `TDIGEST.INFO` to retrieve additional information about the t-digest.

```redis Retrieve additional info
TDIGEST.INFO t
 1) Compression
 2) (integer) 1000
 3) Capacity
 4) (integer) 6010
 5) Merged nodes
 6) (integer) 15
 7) Unmerged nodes
 8) (integer) 0
 9) Merged weight
10) "15"
11) Unmerged weight
12) "0"
13) Observations
14) "15"
15) Total compressions
16) (integer) 1
17) Memory usage
18) (integer) 96168
```

The following values are reported:

* `Compression` - The compression (controllable trade-off between accuracy and memory consumption) of the sketch, as set in `TDIGEST.CREATE` or `TDIGEST.MERGE`.

* `Observations` - Number of observations added to the sketch.

* `Memory usage` - Number of bytes allocated for the sketch.

In addition, the following _internals_ are reported: 

* `Capacity` - Size of the buffer used for storing the centroids and for the incoming unmerged observations.

* `Merged nodes` - Number of merged observations.

* `Unmerged nodes` - Number of buffered nodes (uncompressed observations).

* `Merged weight` - Weight of values of the merged nodes.

* `Unmerged weight` - Weight of values of the unmerged nodes (uncompressed observations).

* `Total compressions` - Number of times this sketch compressed data together.

## Reset a sketch

`TDIGEST.RESET` key empties the sketch and re-initializes it.