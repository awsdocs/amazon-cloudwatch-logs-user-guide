# Sending Logs Directly to Amazon S3<a name="Sending-Logs-Directly-To-S3"></a>

Some AWS services can publish logs directly to Amazon S3\. This way, if your main requirement for logs is storage in Amazon S3, you can easily have the service producing the logs send them directly to Amazon S3 without setting up additional infrastructure\.

Logs published to Amazon S3 are published to an existing bucket that you specify\. One or more log files are created every five minutes in the specified bucket\.

Even when logs are published directly to an S3 bucket, CloudWatch Logs charges apply\. For more information, see [Amazon CloudWatch Pricing](https://aws.amazon.com/cloudwatch/pricing/)\.

The following logs can be published directly to Amazon S3:
+ VPC flow logs\. For more information, see [Publishing Flow Logs to Amazon S3](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/flow-logs-s3.html) in the *Amazon VPC User Guide*\.