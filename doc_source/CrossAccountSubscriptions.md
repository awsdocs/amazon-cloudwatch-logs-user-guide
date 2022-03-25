# Cross\-account log data sharing with subscriptions<a name="CrossAccountSubscriptions"></a>

You can collaborate with an owner of a different AWS account and receive their log events on your AWS resources, such as an Amazon Kinesis or Amazon Kinesis Data Firehose stream \(this is known as cross\-account data sharing\)\. For example, this log event data can be read from a centralized Kinesis or Kinesis Data Firehose stream to perform custom processing and analysis\. Custom processing is especially useful when you collaborate and analyze data across many accounts\.

For example, a company's information security group might want to analyze data for real\-time intrusion detection or anomalous behaviors so it could conduct an audit of accounts in all divisions in the company by collecting their federated production logs for central processing\. A real\-time stream of event data across those accounts can be assembled and delivered to the information security groups, who can use Kinesis to attach the data to their existing security analytic systems\.

**Note**  
Kinesis Data Firehose is not available in Asia Pacific \(Osaka\)\.

**Topics**
+ [Cross\-account log data sharing using Kinesis](CrossAccountSubscriptions-Kinesis.md)
+ [Cross\-account log data sharing using Kinesis Data Firehose](CrossAccountSubscriptions-Firehose.md)