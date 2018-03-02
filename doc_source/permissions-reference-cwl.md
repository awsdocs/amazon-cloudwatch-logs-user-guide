# CloudWatch Logs Permissions Reference<a name="permissions-reference-cwl"></a>

When you are setting up [Access Control](auth-and-access-control-cwl.md#access-control-cwl) and writing permissions policies that you can attach to an IAM identity \(identity\-based policies\), you can use the following table as a reference\. The table lists each CloudWatch Logs API operation and the corresponding actions for which you can grant permissions to perform the action\. You specify the actions in the policy's `Action` field, and you specify a wildcard character \(\*\) as the resource value in the policy's `Resource` field\.

You can use AWS\-wide condition keys in your CloudWatch Logs policies to express conditions\. For a complete list of AWS\-wide keys, see [AWS Global and IAM Condition Context Keys](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html) in the *IAM User Guide*\.

**Note**  
To specify an action, use the `logs:` prefix followed by the API operation name\. For example: `logs:CreateLogGroup`, `logs:CreateLogStream`, or `logs:*` \(for all CloudWatch Logs actions\)\.


**CloudWatch Logs API Operations and Required Permissions for Actions**  

| CloudWatch Logs API Operations | Required Permissions \(API Actions\) | 
| --- | --- | 
|  [CancelExportTask](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CancelExportTask.html)  |  `logs:CancelExportTask` Required to cancel a pending or running export task\.  | 
|  [CreateExportTask](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateExportTask.html)  |  `logs:CreateExportTask` Required to export data from a log group to an Amazon S3 bucket\.  | 
|  [CreateLogGroup](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogGroup.html)  |  `logs:CreateLogGroup` Required to create a new log group\.  | 
|  [CreateLogStream](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogStream.html)  |  `logs:CreateLogStream` Required to create a new log stream in a log group\.  | 
|  [DeleteDestination](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DeleteDestination.html)  |  `logs:DeleteDestination` Required to delete a log destination and disables any subscription filters to it\.  | 
|  [DeleteLogGroup](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DeleteLogGroup.html)  |  `logs:DeleteLogGroup` Required to delete a log group and any associated archived log events\.  | 
|  [DeleteLogStream](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DeleteLogStream.html)  |  `logs:DeleteLogStream` Required to delete a log stream and any associated archived log events\.  | 
|  [DeleteMetricFilter](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DeleteMetricFilter.html)  |  `logs:DeleteMetricFilter` Required to delete a metric filter associated with a log group\.  | 
|  [DeleteRetentionPolicy](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DeleteRetentionPolicy.html)  |  `logs:DeleteRetentionPolicy` Required to delete a log group's retention policy\.  | 
|  [DeleteSubscriptionFilter](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DeleteSubscriptionFilter.html)  |  `logs:DeleteSubscriptionFilter` Required to delete the subscription filter associated with a log group\.  | 
|  [DescribeDestinations](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeDestinations.html)  |  `logs:DescribeDestinations` Required to view all destinations associated with the account\.  | 
|  [DescribeExportTasks](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeExportTasks.html)  |  `logs:DescribeExportTasks` Required to view all export tasks associated with the account\.  | 
|  [DescribeLogGroups](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeLogGroups.html)  |  `logs:DescribeLogGroups` Required to view all log groups associated with the account\.  | 
|  [DescribeLogStreams](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeLogStreams.html)  |  `logs:DescribeLogStreams` Required to view all log streams associated with a log group\.  | 
|  [DescribeMetricFilters](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeMetricFilters.html)  |  `logs:DescribeMetricFilters` Required to view all metrics associated with a log group\.  | 
|  [DescribeSubscriptionFilters](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeSubscriptionFilters.html)  |  `logs:DescribeSubscriptionFilters` Required to view all subscription filters associated with a log group\.  | 
|  [FilterLogEvents](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_FilterLogEvents.html)  |  `logs:FilterLogEvents` Required to sort log events by log group filter pattern\.  | 
|  [GetLogEvents](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_GetLogEvents.html)  |  `logs:GetLogEvents` Required to retrieve log events from a log stream\.  | 
|  [ListTagsLogGroup](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_ListTagsLogGroup.html)  |  `logs:ListTagsLogGroup` Required to list the tags associated with a log group\.  | 
|  [PutDestination](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutDestination.html)  |  `logs:PutDestination` Required to create or update a destination log stream \(such as an Kinesis stream\)\.  | 
|  [PutDestinationPolicy](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutDestinationPolicy.html)  |  `logs:PutDestinationPolicy` Required to create or update an access policy associated with an existing log destination\.  | 
|  [PutLogEvents](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutLogEvents.html)  |  `logs:PutLogEvents` Required to upload a batch of log events to a log stream\.  | 
|  [PutMetricFilter](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutMetricFilter.html)  |  `logs:PutMetricFilter` Required to create or update a metric filter and associate it with a log group\.  | 
|  [PutRetentionPolicy](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutRetentionPolicy.html)  |  `logs:PutRetentionPolicy` Required to set the number of days to keep log events \(retention\) in a log group\.  | 
|  [PutSubscriptionFilter](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutSubscriptionFilter.html)  |  `logs:PutSubscriptionFilter` Required to create or update a subscription filter and associate it with a log group\.  | 
|  [TagLogGroup](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_TagLogGroup.html)  |  `logs:TagLogGroup` Required to add or update log group tags\.  | 
|  [TestMetricFilter](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_TestMetricFilter.html)  |  `logs:TestMetricFilter` Required to test a filter pattern against a sampling of log event messages\.  | 