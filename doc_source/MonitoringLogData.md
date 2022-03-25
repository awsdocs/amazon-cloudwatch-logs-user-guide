# Creating metrics from log events using filters<a name="MonitoringLogData"></a>

You can search and filter the log data coming into CloudWatch Logs by creating one or more *metric filters*\. Metric filters define the terms and patterns to look for in log data as it is sent to CloudWatch Logs\. CloudWatch Logs uses these metric filters to turn log data into numerical CloudWatch metrics that you can graph or set an alarm on\.

When you create a metric from a log filter, you can also choose to assign dimensions and a unit to the metric\. If you specify a unit, be sure to specify the correct one when you create the filter\. Changing the unit for the filter later will have no effect\.

You can use any type of CloudWatch statistic, including percentile statistics, when viewing these metrics or setting alarms\.

**Note**  
Percentile statistics are supported for a metric only if none of the metric's values are negative\. If you set up your metric filter so that it can report negative numbers, percentile statistics will not be available for that metric when it has negative numbers as values\. For more information, see [Percentiles](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html#Percentiles)\.

Filters do not retroactively filter data\. Filters only publish the metric data points for events that happen after the filter was created\. Filtered results return the first 50 lines, which will not be displayed if the timestamp on the filtered results is earlier than the metric creation time\.

**Topics**
+ [Concepts](#search-filter-concepts)
+ [Filter and pattern syntax](FilterAndPatternSyntax.md)
+ [Creating metric filters](MonitoringPolicyExamples.md)
+ [Listing metric filters](ListingMetricFilters.md)
+ [Deleting a metric filter](DeletingMetricFilter.md)

## Concepts<a name="search-filter-concepts"></a>

Each metric filter is made up of the following key elements:

**default value**  
The value reported to the metric filter during a period when logs are ingested but no matching logs are found\. By setting this to 0, you ensure that data is reported during every such period, preventing "spotty" metrics with periods of no matching data\. If no logs are ingested during a one\-minute period, then no value is reported\.  
If you assign dimensions to a metric created by a metric filter, you can't assign a default value for that metric\.

**dimensions**  
Dimensions are the key\-value pairs that further define a metric\. You can assign dimensions to the metric created from a metric filter\. Because dimensions are part of the unique identifier for a metric, whenever a unique name/value pair is extracted from your logs, you are creating a new variation of that metric\.

**filter pattern**  
A symbolic description of how CloudWatch Logs should interpret the data in each log event\. For example, a log entry may contain timestamps, IP addresses, strings, and so on\. You use the pattern to specify what to look for in the log file\.

**metric name**  
The name of the CloudWatch metric to which the monitored log information should be published\. For example, you may publish to a metric called ErrorCount\.

**metric namespace**  
The destination namespace of the new CloudWatch metric\.

**metric value**  
The numerical value to publish to the metric each time a matching log is found\. For example, if you're counting the occurrences of a particular term like "Error", the value will be "1" for each occurrence\. If you're counting the bytes transferred, you can increment by the actual number of bytes found in the log event\.