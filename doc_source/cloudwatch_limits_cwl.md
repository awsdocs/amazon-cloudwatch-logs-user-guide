# CloudWatch Logs quotas<a name="cloudwatch_limits_cwl"></a>

CloudWatch Logs has the following quotas:


| Resource | Default quota | 
| --- | --- | 
|  Batch size  |  1 MB \(maximum\)\. This quota can't be changed\.  | 
|  Data archiving  |  Up to 5 GB of data archiving for free\. This quota can't be changed\.  | 
| [CreateLogStream](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogStream.html)  |  50 transactions per second \(TPS/account/Region\), after which transactions are throttled\. You can [request a quota increase](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-cloudwatch-logs)\.  | 
|  [DescribeLogGroups](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeLogGroups.html)  |  5 transactions per second \(TPS/account/Region\)\. You can [request a quota increase](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-cloudwatch-logs)\.  | 
|  [DescribeLogStreams](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeLogStreams.html)  |  5 transactions per second \(TPS/account/Region\)\. You can [request a quota increase](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-cloudwatch-logs)\.  | 
|  Discovered log fields  |  CloudWatch Logs Insights can discover a maximum of 1000 log event fields in a log group\. This quota can't be changed\. For more information, see [Supported Logs and Discovered Fields](CWL_AnalyzeLogData-discoverable-fields.md)\.  | 
|  Extracted log fields in JSON logs  |  CloudWatch Logs Insights can extract a maximum of 100 log event fields from a JSON log\. This quota can't be changed\. For more information, see [Supported Logs and Discovered Fields](CWL_AnalyzeLogData-discoverable-fields.md)\.  | 
|  Event size  |  256 KB \(maximum\)\. This quota can't be changed\.  | 
|  Export task  |  One active \(running or pending\) export task at a time, per account\. This quota can't be changed\.  | 
|  [FilterLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_FilterLogEvents.html)  |  5 transactions per second \(TPS\)/account/Region\. This quota can't be changed\.  | 
|  [GetLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_GetLogEvents.html)  |  10 requests per second per account per Region\. This quota can't be changed\. We recommend subscriptions if you are continuously processing new data\. If you need historical data, we recommend exporting your data to Amazon S3\.  | 
|  Incoming data  |  Up to 5 GB of incoming data for free\. This quota can't be changed\.  | 
|  Log groups  |  20,000 log groups per account per Region\. You can [request a quota increase](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-cloudwatch-logs)\. There is no quota on the number of log streams that can belong to one log group\.  | 
|  Metrics filters  |  100 per log group\. This quota can't be changed\.  | 
|  Embedded metric format metrics  |  100 metrics per log event and 9 dimensions per metric\. For more information about the embedded metric format, see [Specification: Embedded Metric Format](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Embedded_Metric_Format_Specification.html) in the Amazon CloudWatch User Guide\.  | 
|  [PutLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutLogEvents.html)  |  5 requests per second per log stream\. Additional requests are throttled\. This quota can't be changed\. The maximum batch size of a PutLogEvents request is 1MB\. 800 transactions per second per account per Region, except for the following Regions where the quota is 1500 transactions per second per account per Region: US East \(N\. Virginia\), US West \(Oregon\), and Europe \(Ireland\)\. You can [request a quota increase](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-cloudwatch-logs)\.  | 
|  Query execution timeout  |  Queries in CloudWatch Logs Insights time out after 15 minutes\. This time limit can't be changed\.  | 
|  Queried log groups  |  A maximum of 20 log groups can be queried in a single CloudWatch Logs Insights query\. This quota can't be changed\.  | 
|  Query concurrency  |  A maximum of 4 concurrent CloudWatch Logs Insights queries, including queries that have been added to dashboards\. You can [request a quota increase](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-cloudwatch-logs)\.  | 
|  Query results availability  |  Results from a query are retrievable for 7 days\. This availability time can't be changed\.   | 
|  Query results displayed in console  |  By default, up to 1000 rows of query results are displayed on the console\. You can use the **limit** command in a query to increase this to as many as 10,000 rows\. For more information, see [CloudWatch Logs Insights Query Syntax](CWL_QuerySyntax.md)\.   | 
|  Saved queries  |  You can save as many as 1000 CloudWatch Logs Insights queries, per Region per account\. This quota can't be changed\.  | 
|  Subscription filters  |  1 per log group\. This quota can't be changed\.  | 