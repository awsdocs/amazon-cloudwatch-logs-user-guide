# Real\-time Processing of Log Data with Subscriptions<a name="Subscriptions"></a>

You can use subscriptions to get access to a real\-time feed of log events from CloudWatch Logs and have it delivered to other services such as an Amazon Kinesis stream, Amazon Kinesis Data Firehose stream, or AWS Lambda for custom processing, analysis, or loading to other systems\. To begin subscribing to log events, create the receiving source, such as a Kinesis stream, where the events will be delivered\. A subscription filter defines the filter pattern to use for filtering which log events get delivered to your AWS resource, as well as information about where to send matching log events to\.

CloudWatch Logs also produces CloudWatch metrics about the forwarding of log events to subscriptions\. For more information, see [Amazon CloudWatch Logs Metrics and Dimensions](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cwl-metricscollected.html)\.

**Topics**
+ [Concepts](#subscription-concepts)
+ [Using CloudWatch Logs Subscription Filters](SubscriptionFilters.md)
+ [Cross\-Account Log Data Sharing with Subscriptions](CrossAccountSubscriptions.md)

## Concepts<a name="subscription-concepts"></a>

Each subscription filter is made up of the following key elements:

**log group name**  
The log group to associate the subscription filter with\. All log events uploaded to this log group would be subject to the subscription filter and would be delivered to the chosen Kinesis stream if the filter pattern matches with the log events\.

**filter pattern**  
A symbolic description of how CloudWatch Logs should interpret the data in each log event, along with filtering expressions that restrict what gets delivered to the destination AWS resource\. For more information about the filter pattern syntax, see [Filter and Pattern Syntax](FilterAndPatternSyntax.md)\.

**destination arn**  
The Amazon Resource Name \(ARN\) of the Kinesis stream, Kinesis Data Firehose stream, or Lambda function you want to use as the destination of the subscription feed\.

**role arn**  
An IAM role that grants CloudWatch Logs the necessary permissions to put data into the chosen Kinesis stream\. This role is not needed for Lambda destinations because CloudWatch Logs can get the necessary permissions from access control settings on the Lambda function itself\.

**distribution**  
The method used to distribute log data to the destination, when the destination is an Amazon Kinesis stream\. By default, log data is grouped by log stream\. For a more even distribution, you can group log data randomly\.