# Confused deputy prevention<a name="Subscriptions-confused-deputy"></a>

The confused deputy problem is a security issue where an entity that doesn't have permission to perform an action can coerce a more\-privileged entity to perform the action\. In AWS, cross\-service impersonation can result in the confused deputy problem\. Cross\-service impersonation can occur when one service \(the calling service\) calls another service \(the called service\)\. The calling service can be manipulated to use its permissions to act on another customer's resources in a way it should not otherwise have permission to access\. To prevent this, AWS provides tools that help you protect your data for all services with service principals that have been given access to resources in your account\.

We recommend using the `aws:SourceArn` or `aws:SourceAccount` global condition context keys in resource policies to limit the scope of the permissions that you grant to CloudWatch Logs to write data to Kinesis and Kinesis Data Firehose\. 

The value of `aws:SourceArn` must limit the permissions to only the accounts that are writing and receiving data\.

The most effective way to protect against the confused deputy problem is to use the `aws:SourceArn` global condition context key with the full ARN of the resource\. If you don't know the full ARN of the resource or if you are specifying multiple resources, use the aws:SourceArn global context condition key with wildcards \(\*\) for the unknown portions of the ARN\. For example, `arn:aws:servicename::123456789012:*`\.

The policies documented for granting access to CloudWatch Logs to write data to Kinesis and Kinesis Data Firehose in [Create a destination](CreateDestination.md) and [Step 2: Create a destination](CreateFirehoseStreamDestination.md) show how you can use the aws:SourceArn global condition context key to help prevent the confused deputy problem\. 