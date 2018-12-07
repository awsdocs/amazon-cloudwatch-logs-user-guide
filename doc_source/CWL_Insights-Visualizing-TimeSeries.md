# Visualizing Time Series Data<a name="CWL_Insights-Visualizing-TimeSeries"></a>

You can use visualizations to identify trends and patterns that occur over time within your logs\. CloudWatch Logs Insights generates visualizations for all queries with the following characteristics:
+ The query contains one or more aggregation functions\. For more information, see [Aggregation Functions in the Stats Command](CWL_QuerySyntax.md#CWL_Insights_Aggregation_Functions)\.
+ The query uses the `bin()` function to group the data by one field\. 

 CloudWatch Logs Insights displays visualizations using line charts and stacked area charts\. 

**Visualization Examples**

The following query generates a visualization of the average values of the `myfield1` field, with a data point created every five minutes\. Each data point is the aggregation of the averages of the `myfield1` values from the logs from the previous five minutes\.

```
stats avg(myfield1) by bin(5m)
```

The following query generates a visualization of three values based on different fields, with a data point created every five minutes\. The visualization is generated because the query contains aggregate functions and uses bin\(\) as the grouping field\.

```
stats avg(myfield1), min(myfield2), max(myfield3) by bin(5m)
```

**Common Mistakes**

The following query does not generate a visualization, because it contains more than one grouping field\.

```
stats avg(myfield1) by bin(5m), myfield4
```

The following query does not generate a visualization, because the `by bin()` grouping function is not used\. 

```
stats avg(myfield1) by myfield4
```