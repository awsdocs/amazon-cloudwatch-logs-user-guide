# Monitoring with CloudWatch metrics<a name="CloudWatch-Logs-Monitoring-CloudWatch-Metrics"></a>

CloudWatch Logs sends metrics to Amazon CloudWatch every minute\.

## CloudWatch Logs metrics<a name="cwl-metrics"></a>

The `AWS/Logs` namespace includes the following metrics\.


| Metric | Description | 
| --- | --- | 
|  `CallCount`  |  The number of specified API operations performed in your account\. `CallCount` is a CloudWatch Logs service usage metric\. For more information, see [CloudWatch Logs service usage metrics](#CloudWatchLogs-Usage-Metrics)\.  Valid Dimensions: Class, Resource, Service, Type Valid Statistic: Sum Units: None | 
|  `DeliveryErrors`  |  The number of log events for which CloudWatch Logs received an error when forwarding data to the subscription destination\. Valid Dimensions: LogGroupName, DestinationType, FilterName Valid Statistic: Sum Units: None  | 
|  `DeliveryThrottling`  |  The number of log events for which CloudWatch Logs was throttled when forwarding data to the subscription destination\. Valid Dimensions: LogGroupName, DestinationType, FilterName Valid Statistic: Sum Units: None  | 
|  `ErrorCount`  |  The number of API operations performed in your account that resulted in errors\. `ErrorCount` is a CloudWatch Logs service usage metric\. For more information, see [CloudWatch Logs service usage metrics](#CloudWatchLogs-Usage-Metrics)\.  Valid Dimensions: Class, Resource, Service, Type Valid Statistic: Sum Units: None | 
|  `ForwardedBytes`  |  The volume of log events in compressed bytes forwarded to the subscription destination\. Valid Dimensions: LogGroupName, DestinationType, FilterName  Valid Statistic: Sum Units: Bytes  | 
|  `ForwardedLogEvents`  |  The number of log events forwarded to the subscription destination\. Valid Dimensions: LogGroupName, DestinationType, FilterName Valid Statistic: Sum Units: None  | 
|  `IncomingBytes`  |  The volume of log events in uncompressed bytes uploaded to CloudWatch Logs\. When used with the `LogGroupName` dimension, this is the volume of log events in uncompressed bytes uploaded to the log group\. Valid Dimensions: LogGroupName Valid Statistic: Sum Units: Bytes  | 
|  `IncomingLogEvents`  |  The number of log events uploaded to CloudWatch Logs\. When used with the `LogGroupName` dimension, this is the number of log events uploaded to the log group\.  Valid Dimensions: LogGroupName Valid Statistic: Sum Units: None  | 
|  `ThrottleCount`  |  The number of API operations performed in your account that were throttled because of usage quotas\. `ThrottleCount` is a CloudWatch Logs service usage metric\. For more information, see [CloudWatch Logs service usage metrics](#CloudWatchLogs-Usage-Metrics)\.  Valid Dimensions: Class, Resource, Service, Type Valid Statistic: Sum Units: None | 

## Dimensions for CloudWatch Logs metrics<a name="cwl-dimensions"></a>

The dimensions that you can use with CloudWatch Logs metrics are listed below\.


|  Dimension  |  Description  | 
| --- | --- | 
|  LogGroupName  |  The name of the CloudWatch Logs log group for which to display metrics\.  | 
|  DestinationType  |  The subscription destination for the CloudWatch Logs data, which can be AWS Lambda, Amazon Kinesis Data Streams, or Amazon Kinesis Data Firehose\.  | 
|  FilterName  |  The name of the subscription filter that is forwarding data from the log group to the destination\. The subscription filter name is automatically converted by CloudWatch to ASCII and any unsupported characters get replaced with a question mark \(?\)\.  | 

## CloudWatch Logs service usage metrics<a name="CloudWatchLogs-Usage-Metrics"></a>

CloudWatch Logs sends metrics to CloudWatch that track the usage CloudWatch Logs API operations\. These metrics correspond to AWS service quotas\. Tracking these metrics can help you proactively manage your quotas\. For more information, see [ Service Quotas Integration and Usage Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Service-Quota-Integration.html)\.

For example, you could track the `ThrottleCount` metric or set an alarm on that metric\. If the value of this metric rises, you should consider requesting a quota increase for the API operation that is getting throttled\. For more information about CloudWatch Logs service quotas, see [CloudWatch Logs quotas](cloudwatch_limits_cwl.md)\.

CloudWatch Logs publishes service quota usage metrics every minute in both the `AWS/Usage` and `AWS/Logs` namespaces\.

The following table lists the service usage metrics published by CloudWatch Logs\. These metrics do not have a specified unit\. The most useful statistic for these metrics is `SUM`, which represents the total operation count for the 1\-minute period\.

Each of these metrics is published with values for all of the `Service`, `Class`, `Type`, and `Resource` dimensions\. They are also published with a single dimension called `Account Metrics`\. Use the `Account Metrics` dimension to see the sum of metrics for all API operations in your account\. Use the other dimensions and specify the name of an API operation for the `Resource` dimension to find the metrics for that particular API\. 

**Metrics**


| Metric | Description | 
| --- | --- | 
|  `CallCount`  |  The number of specified operations performed in your account\. `CallCount` is published in both the `AWS/Usage` and `AWS/Logs` namespaces\.   | 
|  `ErrorCount`  |  The number of API operations performed in your account that resulted in errors\. `ErrorCount` is published in only the `AWS/Logs`\.  | 
|  `ThrottleCount`  |  The number of API operations performed in your account that were throttled because of usage quotas\. `ThrottleCount` is published in only the `AWS/Logs`\.   | 

**Dimensions**


| Dimension | Description | 
| --- | --- | 
|  `Account metrics`  |  Use this dimension to get a sum of the metric across all of the CloudWatch Logs APIs\. If you want to see the metrics for one particular API, use the other dimensions listed in this table and specify the API name as the value of `Resource`\.  | 
|  `Service`  |  The name of the AWS service containing the resource\. For CloudWatch Logs usage metrics, the value for this dimension is `Logs`\.  | 
|  `Class`  |  The class of resource being tracked\. CloudWatch Logs API usage metrics use this dimension with a value of `None`\.  | 
|  `Type`  |  The type of resource being tracked\. Currently, when the `Service` dimension is `Logs`, the only valid value for `Type` is `API`\.  | 
|  `Resource`  |  The name of the API operation\. Valid values include all of the API operation names that are listed in [ Actions](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_Operations.html)\. For example, `PutLogEvents`  | 