These tutorials will demonstrate Redis's' ability to store time series data using the bike shop use case.

The bike shop company consists of multiple physical stores and an online presence. It would be helpful to have an aggregate view of sales volume across all the stores.

### Create and delete a time series

The following example creates a time series key for five bike shops that will track the total sales for each. Each key has an appropriate region label, `east` or `west`. This kind of representation will allow you to easily query bike sales performance over specific periods on a per-shop or per-region basis.

```redis Create time series per shop
TS.CREATE bike_sales_1 DUPLICATE_POLICY SUM LABELS region east compacted no
TS.CREATE bike_sales_2 DUPLICATE_POLICY SUM LABELS region east compacted no
TS.CREATE bike_sales_3 DUPLICATE_POLICY SUM LABELS region west compacted no
TS.CREATE bike_sales_4 DUPLICATE_POLICY SUM LABELS region west compacted no
TS.CREATE bike_sales_5 DUPLICATE_POLICY SUM LABELS region west compacted no
```

Notice the `DUPLICATE_POLICY SUM` arguments; these describe how Redis should behave when two events in the same store and region have
the same timestamp. In this case, two sales that happen at exactly the same time in a particular store and region are added together.

Now use the `TS.INFO` command to get all the information and statistics about a time series.

```redis Get time series information
TS.INFO bike_sales_1 // Returns information and statistics about bike_sales_1
TS.QUERYINDEX region=east // Get all the time series matching region = east
```

### Manage data points

You can append new samples to your time series. Note that if the series does not exist, it will be automatically created.

```redis Add data points to a time series
TS.ADD // adds a new data point
    bike_sales_0 // new key name
    1640995200000 // timestamp
    0 // value
 
TS.MADD // adds data points to multiple time series
    bike_sales_1 1640995200000 0
    bike_sales_2 1640995200000 0
    bike_sales_3 1640995200000 0
    bike_sales_4 1640995200000 0
 
TS.ADD 
    bike_sales_0
    * // use the current timestamp (milliseconds since Epock)
    50
```

Use `TS.GET` to retrieve the last sample and the last sample matching a specific filter.

```redis Read
TS.GET bike_sales_0 // gets the last sample in the time series
 
TS.MGET FILTER region=east // returns the last sample from a time series with region=east
```

You can also update an existing time series using the `TS.ADD` command.

```redis Update a time series datum
TS.ADD // use TS.ADD to update an existing sample
    bike_sales_0 // key name for the time series you would like to update
    1640995200000 // existing timestamp
    26 // new value
    ON_DUPLICATE LAST // override existing value with the new one
```

To delete samples, specify the interval (inclusive) between two timestamps for a given time series.

```redis Delete
TS.DEL bike_sales_0 1640995300000 1702405480151 // deletes all the data points between two timestamps (inclusive)
 
TS.DEL bike_sales_0 1640995200000 1640995200000 // to delete a single timestamp, use it as both the "from" and the "to" timestamp
```