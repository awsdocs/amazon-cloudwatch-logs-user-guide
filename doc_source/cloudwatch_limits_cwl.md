# CloudWatch Logs quotas<a name="cloudwatch_limits_cwl"></a>

The following tables provide the default service quotas, also referred to as limits, for CloudWatch Logs for an AWS account\. Most of these service quotas, but not all, are listed under the Amazon CloudWatch Logs namespace in the Service Quotas console\. To request a quota increase for those quotas, see the procedure later in this section\.


| Resource | Default quota | 
| --- | --- | 
|  Batch size  |  1 MB \(maximum\)\. This quota can't be changed\.  | 
|  Data archiving  |  Up to 5 GB of data archiving for free\. This quota can't be changed\.  | 
| [CreateLogGroup](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogGroup.html)  |  5 transactions per second \(TPS/account/Region\), after which transactions are throttled\. You can request a quota increase\.  | 
| [CreateLogStream](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogStream.html)  |  50 transactions per second \(TPS/account/Region\), after which transactions are throttled\. You can request a quota increase\.  | 
| [DeleteLogGroup](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DeleteLogGroup.html)  |  5 transactions per second \(TPS/account/Region\), after which transactions are throttled\. You can request a quota increase\.  | 
|  [DescribeLogGroups](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeLogGroups.html)  |  5 transactions per second \(TPS/account/Region\)\. You can request a quota increase\.  | 
|  [DescribeLogStreams](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeLogStreams.html)  |  5 transactions per second \(TPS/account/Region\)\. You can request a quota increase\.  | 
|  Discovered log fields  |  CloudWatch Logs Insights can discover a maximum of 1000 log event fields in a log group\. This quota can't be changed\. For more information, see [Supported logs and discovered fields](CWL_AnalyzeLogData-discoverable-fields.md)\.  | 
|  Extracted log fields in JSON logs  |  CloudWatch Logs Insights can extract a maximum of 100 log event fields from a JSON log\. This quota can't be changed\. For more information, see [Supported logs and discovered fields](CWL_AnalyzeLogData-discoverable-fields.md)\.  | 
|  Event size  |  256 KB \(maximum\)\. This quota can't be changed\.  | 
|  Export task  |  One active \(running or pending\) export task at a time, per account\. This quota can't be changed\.  | 
|  [FilterLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_FilterLogEvents.html)  |  5 transactions per second \(TPS\)/account/Region\. This quota can't be changed\.  | 
|  [GetLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_GetLogEvents.html)  |  10 requests per second per account per Region\. This quota can't be changed\. We recommend subscriptions if you are continuously processing new data\. If you need historical data, we recommend exporting your data to Amazon S3\.  | 
|  Incoming data  |  Up to 5 GB of incoming data for free\. This quota can't be changed\.  | 
|  Log groups  |  1,000,000 log groups per account per Region\. You can request a quota increase\. There is no quota on the number of log streams that can belong to one log group\.  | 
|  Metrics filters  |  100 per log group\. This quota can't be changed\.  | 
|  Embedded metric format metrics  |  100 metrics per log event and 9 dimensions per metric\. For more information about the embedded metric format, see [Specification: Embedded Metric Format](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Embedded_Metric_Format_Specification.html) in the Amazon CloudWatch User Guide\.  | 
|  [PutLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutLogEvents.html)  |  5 requests per second per log stream\. Additional requests are throttled\. This quota can't be changed\. The maximum batch size of a PutLogEvents request is 1MB\. 800 transactions per second per account per Region, except for the following Regions where the quota is 1500 transactions per second per account per Region: US East \(N\. Virginia\), US West \(Oregon\), and Europe \(Ireland\)\. You can request a quota increase\.  | 
|  Query execution timeout  |  Queries in CloudWatch Logs Insights time out after 15 minutes\. This time limit can't be changed\.  | 
|  Queried log groups  |  A maximum of 20 log groups can be queried in a single CloudWatch Logs Insights query\. This quota can't be changed\.  | 
|  Query concurrency  |  A maximum of 10 concurrent CloudWatch Logs Insights queries, including queries that have been added to dashboards\. This quota is not managed through Service Quotas\. You can request a quota increase by [ creating a support case](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-cloudwatch-logs)\.  | 
|  Query results availability  |  Results from a query are retrievable for 7 days\. This availability time can't be changed\.   | 
|  Query results displayed in console  |  By default, up to 1000 rows of query results are displayed on the console\. You can use the **limit** command in a query to increase this to as many as 10,000 rows\. For more information, see [CloudWatch Logs Insights query syntax](CWL_QuerySyntax.md)\.   | 
|  Resource policies  |  Up to 10 CloudWatch Logs resource policies per Region per account\. This quota can't be changed\.   | 
|  Saved queries  |  You can save as many as 1000 CloudWatch Logs Insights queries, per Region per account\. This quota can't be changed\.  | 
|  Subscription filters  |  2 per log group\. This quota can't be changed\.  | 

## Managing your CloudWatch Logs service quotas<a name="service-quotas-manage"></a>

CloudWatch Logs has integrated with Service Quotas, an AWS service that enables you to view and manage your quotas from a central location\. For more information, see [What Is Service Quotas?](https://docs.aws.amazon.com/servicequotas/latest/userguide/intro.html) in the *Service Quotas User Guide*\.

Service Quotas makes it easy to look up the value of your CloudWatch Logs service quotas\.

------
#### [ AWS Management Console ]

**To view CloudWatch Logs service quotas using the console**

1. Open the Service Quotas console at [https://console\.aws\.amazon\.com/servicequotas/](https://console.aws.amazon.com/servicequotas/)\.

1. In the navigation pane, choose **AWS services**\.

1. From the **AWS services** list, search for and select **Amazon CloudWatch Logs**\.

   In the **Service quotas** list, you can see the service quota name, applied value \(if it is available\), AWS default quota, and whether the quota value is adjustable\.

1. To view additional information about a service quota, such as the description, choose the quota name\.

1. \(Optional\) To request a quota increase, select the quota that you want to increase, select **Request quota increase**, enter or select the required information, and select **Request**\.

To work more with service quotas using the console see the [Service Quotas User Guide](https://docs.aws.amazon.com/servicequotas/latest/userguide/intro.html)\. To request a quota increase, see [Requesting a quota increase](https://docs.aws.amazon.com/servicequotas/latest/userguide/request-quota-increase.html) in the *Service Quotas User Guide*\.

------
#### [ AWS CLI ]

**To view CloudWatch Logs service quotas using the AWS CLI**  
Run the following command to view the default CloudWatch Logs quotas\.

```
aws service-quotas list-aws-default-service-quotas \
    --query 'Quotas[*].{Adjustable:Adjustable,Name:QuotaName,Value:Value,Code:QuotaCode}' \
    --service-code logs \
    --output table
```

To work more with service quotas using the AWS CLI, see the [Service Quotas AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/service-quotas/index.html#cli-aws-service-quotas)\. To request a quota increase, see the [https://docs.aws.amazon.com/cli/latest/reference/service-quotas/request-service-quota-increase.html](https://docs.aws.amazon.com/cli/latest/reference/service-quotas/request-service-quota-increase.html) command in the [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/service-quotas/index.html#cli-aws-service-quotas)\.

------