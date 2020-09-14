# Real\-time Processing of Log Data with Subscriptions<a name="Subscriptions"></a>

You can use subscriptions to get access to a real\-time feed of log events from CloudWatch Logs and have it delivered to other services such as an Amazon Kinesis stream, an Amazon Kinesis Data Firehose stream, or AWS Lambda for custom processing, analysis, or loading to other systems\. When log events are sent to the receiving service, they are Base64 encoded and compressed with the gzip format\.

To begin subscribing to log events, create the receiving resource, such as a Kinesis stream, where the events will be delivered\. A subscription filter defines the filter pattern to use for filtering which log events get delivered to your AWS resource, as well as information about where to send matching log events to\.

Each log group can have only one subscription filter associated with it\.

**Note**  
If the destination service returns a retryable error such as a throttling exception or a retryable service exception \(HTTP 5xx for example\), CloudWatch Logs continues to retry delivery for up to 24 hours\. CloudWatch Logs does not try to re\-deliver if the error is a non\-retryable error, such as AccessDeniedException or ResourceNotFoundException\. 

CloudWatch Logs also produces CloudWatch metrics about the forwarding of log events to subscriptions\. For more information, see [Amazon CloudWatch Logs Metrics and Dimensions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cwl-metricscollected.html)\.

**Topics**
+ [Concepts](#subscription-concepts)
+ [Using CloudWatch Logs Subscription Filters](SubscriptionFilters.md)
+ [Cross\-Account Log Data Sharing with Subscriptions](CrossAccountSubscriptions.md)

## Concepts<a name="subscription-concepts"></a>

Each subscription filter is made up of the following key elements:

**log group name**  
The log group to associate the subscription filter with\. All log events uploaded to this log group would be subject to the subscription filter, and those that match the filter are delivered to the destination service that is receiving the matching log events\.

**filter pattern**  
A symbolic description of how CloudWatch Logs should interpret the data in each log event, along with filtering expressions that restrict what gets delivered to the destination AWS resource\. For more information about the filter pattern syntax, see [Filter and Pattern Syntax](FilterAndPatternSyntax.md)\.

**destination arn**  
The Amazon Resource Name \(ARN\) of the Kinesis stream, Kinesis Data Firehose stream, or Lambda function you want to use as the destination of the subscription feed\.

**role arn**  
An IAM role that grants CloudWatch Logs the necessary permissions to put data into the chosen destination\. This role is not needed for Lambda destinations because CloudWatch Logs can get the necessary permissions from access control settings on the Lambda function itself\.

**distribution**  
The method used to distribute log data to the destination, when the destination is an Amazon Kinesis stream\. By default, log data is grouped by log stream\. For a more even distribution, you can group log data randomly\.