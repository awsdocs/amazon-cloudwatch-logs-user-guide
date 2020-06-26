# Cross\-Account Log Data Sharing with Subscriptions<a name="CrossAccountSubscriptions"></a>

You can collaborate with an owner of a different AWS account and receive their log events on your AWS resources, such as a Amazon Kinesis stream \(this is known as cross\-account data sharing\)\. For example, this log event data can be read from a centralized Amazon Kinesis stream to perform custom processing and analysis\. Custom processing is especially useful when you collaborate and analyze data across many accounts\. For example, a company's information security group might want to analyze data for real\-time intrusion detection or anomalous behaviors so it could conduct an audit of accounts in all divisions in the company by collecting their federated production logs for central processing\. A real\-time stream of event data across those accounts can be assembled and delivered to the information security groups who can use Kinesis to attach the data to their existing security analytic systems\.

Kinesis streams are currently the only resource supported as a destination for cross\-account subscriptions\.

To share log data across accounts, you need to establish a log data sender and receiver:
+ **Log data sender**—gets the destination information from the recipient and lets CloudWatch Logs know that it is ready to send its log events to the specified destination\. In the procedures in the rest of this section, the log data sender is shown with a fictional AWS account number of 111111111111\.
+ **Log data recipient**—sets up a destination that encapsulates a Kinesis stream and lets CloudWatch Logs know that the recipient wants to receive log data\. The recipient then shares the information about his destination with the sender\. In the procedures in the rest of this section, the log data recipient is shown with a fictional AWS account number of 999999999999\.

To start receiving log events from cross\-account users, the log data recipient first creates a CloudWatch Logs destination\. Each destination consists of the following key elements:

**Destination name**  
The name of the destination you want to create\.

**Target ARN**  
The Amazon Resource Name \(ARN\) of the AWS resource that you want to use as the destination of the subscription feed\.

**Role ARN**  
An AWS Identity and Access Management \(IAM\) role that grants CloudWatch Logs the necessary permissions to put data into the chosen Kinesis stream\.

**Access policy**  
An IAM policy document \(in JSON format, written using IAM policy grammar\) that governs the set of users that are allowed to write to your destination\.

The log group and the destination must be in the same AWS region\. However, the AWS resource that the destination points to can be located in a different region\.

**Topics**
+ [Create a Destination](CreateDestination.md)
+ [Create a Subscription Filter](CreateSubscriptionFilter.md)
+ [Validating the Flow of Log Events](ValidateLogEventFlow.md)
+ [Modifying Destination Membership at Runtime](ModifyDestinationMembership.md)