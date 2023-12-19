It's possible to aggreate samples of one or more time series by leveraging aggregation functions.

```redis Get sales per hour for a single shop
TS.RANGE bike_sales_1 - + AGGREGATION avg 3600000
```

```redis Get sales per day for a single shop
TS.RANGE bike_sales_2 - + AGGREGATION avg 86400000
```

```redis Get sales per day for the west region
// Get sales per day, across all shops in the west region
TS.MRANGE - + AGGREGATION sum 86400000 FILTER region=west compacted=no
```
 
You can use the `GROUPBY` and `REDUCE` options to group results of time series by label and apply an additional aggregation.

```redis All sales per region, compacted
TS.MRANGE - + FILTER region=(east,west) compacted=yes GROUPBY region REDUCE sum
```
