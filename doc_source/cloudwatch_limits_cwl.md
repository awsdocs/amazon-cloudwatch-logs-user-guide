# CloudWatch Logs Limits<a name="cloudwatch_limits_cwl"></a>

CloudWatch Logs has the following limits:


| Resource | Default Limit | 
| --- | --- | 
|  Batch size  |  1 MB \(maximum\)\. This limit cannot be changed\.  | 
|  Data archiving  |  Up to 5 GB of data archiving for free\. This limit cannot be changed\.  | 
|  [DescribeLogStreams](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeLogStreams.html)  |  5 transactions per second \(TPS/account/Region\)\. If you experience frequent throttling, you can [request a limit increase](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-cloudwatch-logs)\.  | 
|  Discovered log fields  |  CloudWatch Logs Insights can discover a maximum of 1000 log event fields in a log group\. This limit cannot be changed\.  | 
|  Event size  |  256 KB \(maximum\)\. This limit cannot be changed\.  | 
|  Export task  |  One active \(running or pending\) export task at a time, per account\. This limit cannot be changed\.  | 
|  [FilterLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_FilterLogEvents.html)  |  5 transactions per second \(TPS\)/account/Region\. This limit can be changed only in special circumstances\. If you experience frequent throttling, contact AWS Support\. For instructions, see [AWS Service Limits](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html)\.  | 
|  [GetLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_GetLogEvents.html)  |  10 requests per second per account per Region\. We recommend subscriptions if you are continuously processing new data\. If you need historical data, we recommend exporting your data to Amazon S3\. This limit can be changed only in special circumstances\. If you experience frequent throttling, contact AWS Support\. For instructions, see [AWS Service Limits](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html)\.  | 
|  Incoming data  |  Up to 5 GB of incoming data for free\. This limit cannot be changed\.  | 
|  Log groups  |  5000 log groups per account per Region\. You can [request a limit increase](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-cloudwatch-logs)\. There is no limit on the number of log streams that can belong to one log group\.  | 
|  Metrics filters  |  100 per log group\. This limit cannot be changed\.  | 
|  [PutLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutLogEvents.html)  |  5 requests per second per log stream\. Additional requests are throttled\. This limit cannot be changed\. The maximum batch size of a PutLogEvents request is 1MB\. 1500 transactions per second per account per Region, except for the following Regions where the limit is 800 transactions per second per account per Region: ap\-south\-1, ap\-northeast\-1, ap\-northeast\-2, ap\-southeast\-1, ap\-southeast\-2, eu\-central\-1, eu\-west\-2, sa\-east\-1, us\-east\-2, and us\-west\-1\. You can [request a limit increase](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-cloudwatch-logs)\.  | 
|  Query concurrency  |  A maximum of 4 concurrent CloudWatch Logs Insights queries, including queries that have been added to dashboards\. You can [request a limit increase](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-cloudwatch-logs)\.  | 
|  Query results displayed in console  |  In CloudWatch Logs Insights query results, a maximum of 1000 log events are displayed on the console\. This limit cannot be changed\.  | 
|  Subscription filters  |  1 per log group\. This limit cannot be changed\.  | 