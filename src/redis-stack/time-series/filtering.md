You can filter by value, timestamp, and label.
 
```redis Filter by label
TS.MRANGE - + FILTER region=east // Shows data from all time series that have a label of region with a value of east. The results will be grouped by time series.
```

Let's see only the information that fits into a specific range of values.

```redis Filtering by value
TS.RANGE bike_sales_3_per_day - + FILTER_BY_VALUE 3000 5000 // returns all data points whose value sits between 3000 and 5000, inclusive.
 
TS.MRANGE - +  FILTER_BY_VALUE 3000 5000 FILTER region=east // filters on multiple series
```

We also can filter the data based on timestamps.

```redis Filtering by specific timestamps
TS.RANGE bike_sales_2 - + FILTER_BY_TS 1647734400000 1648062372210 // Retrieves the data points for specific timestamps from one time series
 
TS.MRANGE - +  FILTER_BY_TS 1647734400000 1648062372210 FILTER region=east // Retrieves the data points for specific timestamps from multiple time series
```
