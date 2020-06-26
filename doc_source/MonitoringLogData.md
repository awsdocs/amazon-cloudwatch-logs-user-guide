# Creating Metrics From Log Events Using Filters<a name="MonitoringLogData"></a>

After the CloudWatch Logs agent begins publishing log data to Amazon CloudWatch, you can begin searching and filtering the log data by creating one or more metric filters\. Metric filters define the terms and patterns to look for in log data as it is sent to CloudWatch Logs\. CloudWatch Logs uses these metric filters to turn log data into numerical CloudWatch metrics that you can graph or set an alarm on\. You can use any type of CloudWatch statistic, including percentile statistics, when viewing these metrics or setting alarms\.

**Note**  
Percentile statistics are supported for a metric only if none of the metric's values are negative\. If you set up your metric filter so that it can report negative numbers, percentile statistics will not be available for that metric when it has negative numbers as values\. For more information, see [Percentiles](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html#Percentiles)\.

Filters do not retroactively filter data\. Filters only publish the metric data points for events that happen after the filter was created\. Filtered results return the first 50 lines, which will not be displayed if the timestamp on the filtered results is earlier than the metric creation time\.

**Topics**
+ [Concepts](#search-filter-concepts)
+ [Filter and Pattern Syntax](FilterAndPatternSyntax.md)
+ [Creating Metric Filters](MonitoringPolicyExamples.md)
+ [Listing Metric Filters](ListingMetricFilters.md)
+ [Deleting a Metric Filter](DeletingMetricFilter.md)

## Concepts<a name="search-filter-concepts"></a>

Each metric filter is made up of the following key elements:

**filter pattern**  
A symbolic description of how CloudWatch Logs should interpret the data in each log event\. For example, a log entry may contain timestamps, IP addresses, strings, and so on\. You use the pattern to specify what to look for in the log file\.

**metric name**  
The name of the CloudWatch metric to which the monitored log information should be published\. For example, you may publish to a metric called ErrorCount\.

**metric namespace**  
The destination namespace of the new CloudWatch metric\.

**metric value**  
The numerical value to publish to the metric each time a matching log is found\. For example, if you're counting the occurrences of a particular term like "Error", the value will be "1" for each occurrence\. If you're counting the bytes transferred, you can increment by the actual number of bytes found in the log event\.

**default value**  
The value reported to the metric filter during a period when no matching logs are found\. By setting this to 0, you ensure that data is reported during every period, preventing "spotty" metrics with periods of no data\.