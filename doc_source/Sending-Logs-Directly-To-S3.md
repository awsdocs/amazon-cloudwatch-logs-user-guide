# Sending Logs Directly to Amazon S3 or Kinesis Data Firehose<a name="Sending-Logs-Directly-To-S3"></a>

Some AWS services can publish logs directly to Amazon S3 or Kinesis Data Firehose\. This way, if your main requirement for logs is storage or processing in one of these services, you can easily have the service producing the logs send them directly to Amazon S3 or Kinesis Data Firehose without setting up additional infrastructure\.

Logs published to Amazon S3 are published to an existing bucket that you specify\. One or more log files are created every five minutes in the specified bucket\.

Even when logs are published directly to Amazon S3 or Kinesis Data Firehose, CloudWatch Logs charges apply\. For more information, see *Vended Logs* on the **Logs** tab [Amazon CloudWatch Pricing](https://aws.amazon.com/cloudwatch/pricing/)\.

The following logs can be published directly to Amazon S3:
+ VPC flow logs\. For more information, see [Publishing Flow Logs to Amazon S3](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs-s3.html) in the *Amazon VPC User Guide*\.
+ AWS Global Accelerator flow logs\. For more information, see [ Publishing Flow Logs to Amazon S3](https://docs.aws.amazon.com/global-accelerator/latest/dg/monitoring-global-accelerator.flow-logs.html#monitoring-global-accelerator.flow-logs-publishing-S3) in the *AWS Global Accelerator Developer Guide*\.

The following logs can be published directly to Kinesis Data Firehose:
+ Amazon Managed Streaming for Apache Kafka logs\. For more information, see [ Logging](https://docs.aws.amazon.com/msk/latest/developerguide/msk-logging.html) in the *Amazon Managed Streaming for Apache Kafka Developer Guide*\.