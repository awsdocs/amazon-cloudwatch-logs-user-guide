# Enabling Logging from Certain AWS Services<a name="AWS-logs-and-resource-policy"></a>

If you want to send logs from some AWS services to CloudWatch Logs, you must use or create a CloudWatch Logs resource policy that grants the service permission to send their logs to CloudWatch Logs\. This issue can affect Amazon API Gateway, AWS Step Functions, and Amazon Managed Streaming for Apache Kafka\.

These services must list each log group that they are sending in the resource policy, and CloudWatch Logs resource policies are limited to 5120 characters\. So, a service that sends logs to a large number of log groups may run into this limit\. 

To mitigate this, CloudWatch Logs monitors the size of resource policies used by other AWS services, and when it detects that a policy approaches the size limit of 5120 characters, CloudWatch Logs automatically enables `/aws/vendedlogs/*` in the resource policy for that service\. You can then start creating log subscriptions using log groups with names that start with `/aws/vendedlogs/` for these services\.