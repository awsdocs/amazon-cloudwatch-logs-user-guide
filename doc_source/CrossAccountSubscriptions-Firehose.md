# Cross\-account log data sharing using Kinesis Data Firehose<a name="CrossAccountSubscriptions-Firehose"></a>

To share log data across accounts, you need to establish a log data sender and receiver:
+ **Log data sender**—gets the destination information from the recipient and lets CloudWatch Logs know that it is ready to send its log events to the specified destination\. In the procedures in the rest of this section, the log data sender is shown with a fictional AWS account number of 111111111111\.
+ **Log data recipient**—sets up a destination that encapsulates a Kinesis stream and lets CloudWatch Logs know that the recipient wants to receive log data\. The recipient then shares the information about this destination with the sender\. In the procedures in the rest of this section, the log data recipient is shown with a fictional AWS account number of 222222222222\.

The example in this section uses a Kinesis Data Firehose delivery stream with Amazon S3 storage\. You can also set up Kinesis Data Firehose delivery streams with different settings\. For more information, see [ Creating a Kinesis Data Firehose Delivery Stream](https://docs.aws.amazon.com/firehose/latest/dev/basic-create.html)\.

The log group and the destination must be in the same AWS Region\. However, the AWS resource that the destination points to can be located in a different Region\.

**Note**  
This feature is not available in Asia Pacific \(Osaka\) because Kinesis Data Firehose is not available in that Region\.

**Topics**
+ [Step 1: Create a Kinesis Data Firehose delivery stream](CreateFirehoseStream.md)
+ [Step 2: Create a destination](CreateFirehoseStreamDestination.md)
+ [Step 3: Create a subscription filter](CreateSubscriptionFilterFirehose.md)
+ [Validating the flow of log events](ValidateLogEventFlowFirehose.md)
+ [Modifying destination membership at runtime](ModifyDestinationMembershipFirehose.md)