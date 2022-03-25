# Visualizing log data in graphs<a name="CWL_Insights-Visualizing-Log-Data"></a>

You can use visualizations such as bar charts, line charts, and stacked area charts to more efficiently identify patterns in your log data\. CloudWatch Logs Insights generates visualizations for queries that use the `stats` function and one or more aggregation functions\. For more information, see [Aggregation Functions in the Stats Command](CWL_QuerySyntax.md#CWL_Insights_Aggregation_Functions)\.

All such queries can produce bar charts\. If your query uses the `bin()` function to group the data by one field over time, you can also see line charts and stacked area charts\.

**Topics**
+ [Visualizing time series data](#CWL_Insights-Visualizing-TimeSeries)
+ [Visualizing log data grouped by fields](#CWL_Insights-Visualizing-ByFields)

## Visualizing time series data<a name="CWL_Insights-Visualizing-TimeSeries"></a>

Time series visualizations work for queries with the following characteristics:
+ The query contains one or more aggregation functions\. For more information, see [Aggregation Functions in the Stats Command](CWL_QuerySyntax.md#CWL_Insights_Aggregation_Functions)\.
+ The query uses the `bin()` function to group the data by one field\. 

These queries can produce line charts, stacked area charts, bar charts, and pie charts\. 

**Examples**

For a complete tutorial, see [Tutorial: Run a query that produces a time series visualization](CWL_AnalyzeLogData_VisualizationQuery.md)\. 

Here are more example queries that work for time series visualization\.

The following query generates a visualization of the average values of the `myfield1` field, with a data point created every five minutes\. Each data point is the aggregation of the averages of the `myfield1` values from the logs from the previous five minutes\.

```
stats avg(myfield1) by bin(5m)
```

The following query generates a visualization of three values based on different fields, with a data point created every five minutes\. The visualization is generated because the query contains aggregate functions and uses `bin()` as the grouping field\.

```
stats avg(myfield1), min(myfield2), max(myfield3) by bin(5m)
```

**Line chart and stacked area chart restrictions**

Queries that aggregate log entry information but don't use the `bin()` function can generate bar charts\. However, the queries cannot generate line charts or stacked area charts\. For more information about these types of queries, see [Visualizing log data grouped by fields](#CWL_Insights-Visualizing-ByFields)\.

## Visualizing log data grouped by fields<a name="CWL_Insights-Visualizing-ByFields"></a>

You can produce bar charts for queries that use the `stats` function and one or more aggregation functions\. For more information, see [Aggregation Functions in the Stats Command](CWL_QuerySyntax.md#CWL_Insights_Aggregation_Functions)\.

To see the visualization, run your query\. Then choose the **Visualization** tab, select the arrow next to **Line**, and choose **Bar**\. Visualizations are limited to up to 100 bars in the bar chart\.

**Examples**

For a complete tutorial, see [Tutorial: Run a query that produces a visualization grouped by log fields](CWL_AnalyzeLogData_VisualizationFieldQuery.md)\. The following paragraphs include more example queries for visualization by fields\.

The following VPC flow log query finds the average number of bytes transferred per session for each destination address\.

```
stats avg(bytes) by dstAddr
```

You can also produce a chart that includes more than one bar for each resulting value\. For example, the following VPC flow log query finds the average and maximum number of bytes transferred per session for each destination address\.

```
stats avg(bytes), max(bytes) by dstAddr
```

The following query finds the number of Amazon Route 53 query logs for each query type\.

```
stats count(*) by queryType
```