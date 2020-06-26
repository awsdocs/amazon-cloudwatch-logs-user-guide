# CloudWatch Logs Permissions Reference<a name="permissions-reference-cwl"></a>

When you are setting up [Access Control](auth-and-access-control-cwl.md#access-control-cwl) and writing permissions policies that you can attach to an IAM identity \(identity\-based policies\), you can use the following table as a reference\. The table lists each CloudWatch Logs API operation and the corresponding actions for which you can grant permissions to perform the action\. You specify the actions in the policy's `Action` field\. For the `Resource` field, you can specify the ARN of a log group or log stream, or specify `*` to represent all CloudWatch Logs resources\.

You can use AWS\-wide condition keys in your CloudWatch Logs policies to express conditions\. For a complete list of AWS\-wide keys, see [AWS Global and IAM Condition Context Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html) in the *IAM User Guide*\.

**Note**  
To specify an action, use the `logs:` prefix followed by the API operation name\. For example: `logs:CreateLogGroup`, `logs:CreateLogStream`, or `logs:*` \(for all CloudWatch Logs actions\)\.


**CloudWatch Logs API Operations and Required Permissions for Actions**  

| CloudWatch Logs API Operations | Required Permissions \(API Actions\) | 
| --- | --- | 
|  [CancelExportTask](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CancelExportTask.html)  |  `logs:CancelExportTask` Required to cancel a pending or running export task\.  | 
|  [CreateExportTask](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateExportTask.html)  |  `logs:CreateExportTask` Required to export data from a log group to an Amazon S3 bucket\.  | 
|  [CreateLogGroup](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogGroup.html)  |  `logs:CreateLogGroup` Required to create a new log group\.  | 
|  [CreateLogStream](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogStream.html)  |  `logs:CreateLogStream` Required to create a new log stream in a log group\.  | 
|  [DeleteDestination](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DeleteDestination.html)  |  `logs:DeleteDestination` Required to delete a log destination and disables any subscription filters to it\.  | 
|  [DeleteLogGroup](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DeleteLogGroup.html)  |  `logs:DeleteLogGroup` Required to delete a log group and any associated archived log events\.  | 
|  [DeleteLogStream](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DeleteLogStream.html)  |  `logs:DeleteLogStream` Required to delete a log stream and any associated archived log events\.  | 
|  [DeleteMetricFilter](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DeleteMetricFilter.html)  |  `logs:DeleteMetricFilter` Required to delete a metric filter associated with a log group\.  | 
|  [DeleteQueryDefinition](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DeleteQueryDefinition.html)  |  `logs:DeleteQueryDefinition` Required to delete a saved query definition in CloudWatch Logs Insights\.  | 
|  [DeleteResourcePolicy](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DeleteResourcePolicy.html)  |  `logs:DeleteResourcePolicy` Required to delete a CloudWatch Logs resource policy\.  | 
|  [DeleteRetentionPolicy](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DeleteRetentionPolicy.html)  |  `logs:DeleteRetentionPolicy` Required to delete a log group's retention policy\.  | 
|  [DeleteSubscriptionFilter](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DeleteSubscriptionFilter.html)  |  `logs:DeleteSubscriptionFilter` Required to delete the subscription filter associated with a log group\.  | 
|  [DescribeDestinations](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeDestinations.html)  |  `logs:DescribeDestinations` Required to view all destinations associated with the account\.  | 
|  [DescribeExportTasks](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeExportTasks.html)  |  `logs:DescribeExportTasks` Required to view all export tasks associated with the account\.  | 
|  [DescribeLogGroups](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeLogGroups.html)  |  `logs:DescribeLogGroups` Required to view all log groups associated with the account\.  | 
|  [DescribeLogStreams](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeLogStreams.html)  |  `logs:DescribeLogStreams` Required to view all log streams associated with a log group\.  | 
|  [DescribeMetricFilters](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeMetricFilters.html)  |  `logs:DescribeMetricFilters` Required to view all metrics associated with a log group\.  | 
|  [DescribeQueryDefinitions](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeQueryDefinitions.html)  |  `logs:DescribeQueryDefinitions` Required to see the list of saved query definitions in CloudWatch Logs Insights\.  | 
|  [DescribeQueries](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeQueries.html)  |  `logs:DescribeQueries` Required to see the list of CloudWatch Logs Insights queries that are scheduled, executing, or have recently excecuted\.  | 
|  [DescribeResourcePolicies](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeResourcePolicies.html)  |  `logs:DescribeResourcePolicies` Required to view a list of CloudWatch Logs resource policies\.  | 
|  [DescribeSubscriptionFilters](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_DescribeSubscriptionFilters.html)  |  `logs:DescribeSubscriptionFilters` Required to view all subscription filters associated with a log group\.  | 
|  [FilterLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_FilterLogEvents.html)  |  `logs:FilterLogEvents` Required to sort log events by log group filter pattern\.  | 
|  [GetLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_GetLogEvents.html)  |  `logs:GetLogEvents` Required to retrieve log events from a log stream\.  | 
|  [GetLogGroupFields](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_GetLogGroupFields.html)  |  `logs:GetLogGroupFields` Required to retrieve the list of fields that are included in the log events in a log group\.  | 
|  [GetLogRecord](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_GetLogRecord.html)  |  `logs:GetLogRecord` Required to retrieve the details from a single log event\.  | 
|  [GetQueryResults](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_GetQueryResults.html)  |  `logs:GetQueryResults` Required to retrieve the results of CloudWatch Logs Insights queries\.  | 
|  [ListTagsLogGroup](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_ListTagsLogGroup.html)  |  `logs:ListTagsLogGroup` Required to list the tags associated with a log group\.  | 
|  [PutDestination](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutDestination.html)  |  `logs:PutDestination` Required to create or update a destination log stream \(such as an Kinesis stream\)\.  | 
|  [PutDestinationPolicy](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutDestinationPolicy.html)  |  `logs:PutDestinationPolicy` Required to create or update an access policy associated with an existing log destination\.  | 
|  [PutLogEvents](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutLogEvents.html)  |  `logs:PutLogEvents` Required to upload a batch of log events to a log stream\.  | 
|  [PutMetricFilter](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutMetricFilter.html)  |  `logs:PutMetricFilter` Required to create or update a metric filter and associate it with a log group\.  | 
|  [PutQueryDefinition](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutQueryDefinition.html)  |  `logs:PutQueryDefinition` Required to save a query in CloudWatch Logs Insights\.  | 
|  [PutResourcePolicy](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutResourcePolicy.html)  |  `logs:PutResourcePolicy` Required to create a CloudWatch Logs resource policy\.  | 
|  [PutRetentionPolicy](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutRetentionPolicy.html)  |  `logs:PutRetentionPolicy` Required to set the number of days to keep log events \(retention\) in a log group\.  | 
|  [PutSubscriptionFilter](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutSubscriptionFilter.html)  |  `logs:PutSubscriptionFilter` Required to create or update a subscription filter and associate it with a log group\.  | 
|  [StartQuery](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_StartQuery.html)  |  `logs:StartQuery` Required to start CloudWatch Logs Insights queries\.  | 
|  [StopQuery](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_StopQuery.html)  |  `logs:StopQuery` Required to stop a CloudWatch Logs Insights query that is in progress\.  | 
|  [TagLogGroup](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_TagLogGroup.html)  |  `logs:TagLogGroup` Required to add or update log group tags\.  | 
|  [TestMetricFilter](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_TestMetricFilter.html)  |  `logs:TestMetricFilter` Required to test a filter pattern against a sampling of log event messages\.  | 