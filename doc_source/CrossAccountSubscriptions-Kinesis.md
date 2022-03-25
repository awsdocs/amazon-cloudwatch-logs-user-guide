# Cross\-account log data sharing using Kinesis<a name="CrossAccountSubscriptions-Kinesis"></a>

When you create a cross\-account subscription, you can specify a single account or an organization to be the sender\. If you specify an organization, then this procedure enables all accounts in the organization to send logs to the receiver account\.

To share log data across accounts, you need to establish a log data sender and receiver:
+ **Log data sender**—gets the destination information from the recipient and lets CloudWatch Logs know that it's ready to send its log events to the specified destination\. In the procedures in the rest of this section, the log data sender is shown with a fictional AWS account number of 111111111111\.

  If you're going to have multiple accounts in one organization send logs to one recipient account, you can create a policy that grants all accounts in the organization the permission to send logs to the recipient account\. You still have to set up separate subscription filters for each sender account\.
+ **Log data recipient**—sets up a destination that encapsulates a Kinesis stream and lets CloudWatch Logs know that the recipient wants to receive log data\. The recipient then shares the information about this destination with the sender\. In the procedures in the rest of this section, the log data recipient is shown with a fictional AWS account number of 999999999999\.

To start receiving log events from cross\-account users, the log data recipient first creates a CloudWatch Logs destination\. Each destination consists of the following key elements:

**Destination name**  
The name of the destination you want to create\.

**Target ARN**  
The Amazon Resource Name \(ARN\) of the AWS resource that you want to use as the destination of the subscription feed\.

**Role ARN**  
An AWS Identity and Access Management \(IAM\) role that grants CloudWatch Logs the necessary permissions to put data into the chosen Kinesis stream\.

**Access policy**  
An IAM policy document \(in JSON format, written using IAM policy grammar\) that governs the set of users that are allowed to write to your destination\.

The log group and the destination must be in the same AWS Region\. However, the AWS resource that the destination points to can be located in a different Region\. In the examples in the following sections, all Region\-specific resources are created in US East \(N\. Virginia\)\.

**Topics**
+ [Setting up a new cross\-account subscription](Cross-Account-Log_Subscription-New.md)
+ [Updating an existing cross\-account subscription](Cross-Account-Log_Subscription-Update.md)