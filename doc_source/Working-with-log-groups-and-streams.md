# Working with Log Groups and Log Streams<a name="Working-with-log-groups-and-streams"></a>

A log stream is a sequence of log events that share the same source\. Each separate source of logs into CloudWatch Logs makes up a separate log stream\.

A log group is a group of log streams that share the same retention, monitoring, and access control settings\. You can define log groups and specify which streams to put into each group\. There is no limit on the number of log streams that can belong to one log group\.

Use the procedures in this section to work with log groups and log streams\.

## Create a Log Group in CloudWatch Logs<a name="Create-Log-Group"></a>

When you install the CloudWatch Logs agent on an Amazon EC2 instance using the steps in previous sections of the Amazon CloudWatch Logs User Guide, the log group is created as part of that process\. You can also create a log group directly in the CloudWatch console\.

**To create a log group**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**\.

1. Choose **Actions**, **Create log group**\. 

1. Type a name for the log group, and choose **Create log group**\.

## View Log Data Sent to CloudWatch Logs<a name="ViewingLogData"></a>

You can view and scroll through log data on a stream\-by\-stream basis as sent to CloudWatch Logs by the CloudWatch Logs agent\. You can specify the time range for the log data to view\.

**To view log data**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**\.

1. For **Log Groups**, choose the log group to view the streams\.

1. For **Log Streams**, choose the log stream name to view the log data\.

1. To change how the log data is displayed, do one of the following:

   + To expand all log events, above the list of log events, choose **Expand all**\.

   + To expand all log events and view them as plain text, above the list of log events, choose **Text**\.

   + To filter the log events, type the desired search filter in the search field\. For more information, see [Searching and Filtering Log Data](MonitoringLogData.md)\.

   + To view log data for a specified date and time range, above the list of log events, choose **custom**\. You can choose **Absolute** to specify a date and time range or **Relative** to choose a predefined number of minutes, hours, days, or weeks\. You can also switch between **UTC** and **Local timezone**\.

## Change Log Data Retention in CloudWatch Logs<a name="SettingLogRetention"></a>

By default, log data is stored in CloudWatch Logs indefinitely\. However, you can configure how long to store log data in a log group\. Any data older than the current retention setting is automatically deleted\. You can change the log retention for each log group at any time\.

**To change the logs retention setting**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**\.

1. Find the log group to update\.

1. In the **Expire Events After** column for that log group, choose the current retention setting, such as **Never Expire**\.

1. In the **Edit Retention** dialog box, for **Retention**, choose a log retention value, and then choose **Ok**\.

## Tag Log Groups in Amazon CloudWatch Logs<a name="log-group-tagging"></a>

You can assign your own metadata to the log groups you create in Amazon CloudWatch Logs in the form of *tags*\. A tag is a key\-value pair that you define for a log group\. Using tags is a simple yet powerful way to manage AWS resources and organize data, including billing data\.


+ [Tag Basics](#tagging-basics)
+ [Tracking Costs Using Tagging](#tagging-billing)
+ [Tag Restrictions](#tagging-restrictions)
+ [Tagging Log Groups Using the AWS CLI](#log-group-tagging-cli)
+ [Tagging Log Groups Using the CloudWatch Logs API](#log-group-tagging-api)

### Tag Basics<a name="tagging-basics"></a>

You use the AWS CLI or CloudWatch Logs API to complete the following tasks:

+ Add tags to a log group when you create it

+ Add tags to an existing log group

+ List the tags for a log group

+ Remove tags from a log group

You can use tags to categorize your log groups\. For example, you can categorize them by purpose, owner, or environment\. Because you define the key and value for each tag, you can create a custom set of categories to meet your specific needs\. For example, you might define a set of tags that helps you track log groups by owner and associated application\. Here are several examples of tags:

+ Project: Project name

+ Owner: Name

+ Purpose: Load testing

+ Application: Application name

+ Environment: Production

### Tracking Costs Using Tagging<a name="tagging-billing"></a>

You can use tags to categorize and track your AWS costs\. When you apply tags to your AWS resources, including log groups, your AWS cost allocation report includes usage and costs aggregated by tags\. You can apply tags that represent business categories \(such as cost centers, application names, or owners\) to organize your costs across multiple services\. For more information, see [Use Cost Allocation Tags for Custom Billing Reports](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing and Cost Management User Guide*\.

### Tag Restrictions<a name="tagging-restrictions"></a>

The following restrictions apply to tags\.

**Basic restrictions**

+ The maximum number of tags per log group is 50\.

+ Tag keys and values are case\-sensitive\.

+ You can't change or edit tags for a deleted log group\.

**Tag key restrictions**

+ Each tag key must be unique\. If you add a tag with a key that's already in use, your new tag overwrites the existing key\-value pair\.

+ You can't start a tag key with `aws:` because this prefix is reserved for use by AWS\. AWS creates tags that begin with this prefix on your behalf, but you can't edit or delete them\.

+ Tag keys must be between 1 and 128 Unicode characters in length\.

+ Tag keys must consist of the following characters: Unicode letters, digits, white space, and the following special characters: `_ . / = + - @`\.

**Tag value restrictions**

+ Tag values must be between 0 and 255 Unicode characters in length\.

+ Tag values can be blank\. Otherwise, they must consist of the following characters: Unicode letters, digits, white space, and any of the following special characters: `_ . / = + - @`\.

### Tagging Log Groups Using the AWS CLI<a name="log-group-tagging-cli"></a>

You can add, list, and remove tags using the AWS CLI\. For examples, see the following documentation:

[create\-log\-group](http://docs.aws.amazon.com/cli/latest/reference/logs/create-log-group.html)  
Creates a log group\. You can optionally add tags when you create the log group\.

[tag\-log\-group](http://docs.aws.amazon.com/cli/latest/reference/logs/tag-log-group.html)  
Adds or updates tags for the specified log group\.

[list\-tags\-log\-group](http://docs.aws.amazon.com/cli/latest/reference/logs/list-tags-log-group.html)  
Lists the tags for the specified log group\.

[untag\-log\-group](http://docs.aws.amazon.com/cli/latest/reference/logs/untag-log-group.html)  
Removes tags from the specified log group\.

### Tagging Log Groups Using the CloudWatch Logs API<a name="log-group-tagging-api"></a>

You can add, list, and remove tags using the CloudWatch Logs API\. For examples, see the following documentation:

[CreateLogGroup](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogGroup.html)  
Creates a log group\. You can optionally add tags when you create the log group\.

[TagLogGroup](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_TagLogGroup.html)  
Adds or updates tags for the specified log group\.

[ListTagsLogGroup](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_ListTagsLogGroup.html)  
Lists the tags for the specified log group\.

[UntagLogGroup](http://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_UntagLogGroup.html)  
Removes tags from the specified log group\.