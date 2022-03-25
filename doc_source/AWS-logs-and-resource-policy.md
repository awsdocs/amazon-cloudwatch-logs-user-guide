# Enabling logging from certain AWS services<a name="AWS-logs-and-resource-policy"></a>

While many services publish logs only to CloudWatch Logs, some AWS services can publish logs directly to Amazon Simple Storage Service or Amazon Kinesis Data Firehose\. If your main requirement for logs is storage or processing in one of these services, you can easily have the service that produces the logs send them directly to Amazon S3 or Kinesis Data Firehose without additional setup\.

Even when logs are published directly to Amazon S3 or Kinesis Data Firehose, charges apply\. For more information, see *Vended Logs* on the **Logs** tab at [Amazon CloudWatch Pricing](https://aws.amazon.com/cloudwatch/pricing/)\.

**Permissions**

Some of these AWS services use a common infrastructure to send their logs to CloudWatch Logs, Amazon S3, or Kinesis Data Firehose\. To enable the AWS services listed in the following table to send their logs to these destinations, you must be logged in as a user that has certain permissions\.

Additionally, permissions must be granted to AWS to enable the logs to be sent\. AWS can automatically create those permissions when the logs are set up, or you can create them yourself first before you set up the logging\.

If you choose to have AWS automatically set up the necessary permissions and resource policies when you or someone in your organization first sets up the sending of logs, then the user who is setting up the sending of logs must have certain permissions, as explained later in this section\. Alternatively, you can create the resource policies yourself, and then the users who set up the sending of logs do not need as many permissions\.

The following table summarizes which types of logs and which log destinations that the information in this section applies to\.


| Log type | [CloudWatch Logs](#AWS-logs-infrastructure-CWL) | [Amazon S3](#AWS-logs-infrastructure-S3) | [Kinesis Data Firehose](#AWS-logs-infrastructure-Firehose) | 
| --- | --- | --- | --- | 
|  [ Amazon API Gateway access logs](https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-logging.html) |  ✓ |   |   | 
|  [ Amazon Chime media quality metric logs and SIP message logs](https://docs.aws.amazon.com/chime/latest/ag/monitoring-cloudwatch.html#cw-logs)  |  ✓ |   |   | 
|  [ CloudFront: access logs](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/AccessLogs.html) |   | ✓ |  | 
|  [ CloudWatch Evidently evaluation event logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Evidently-datastorage.html#CloudWatch-Evidently-datastorage-logformat) | ✓  | ✓ |  | 
|  [ Amazon ElastiCache for Redis logs](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/Log_Delivery.html) | ✓  |  | ✓ | 
|  [AWS Global Accelerator flow logs](https://docs.aws.amazon.com/global-accelerator/latest/dg/monitoring-global-accelerator.flow-logs.html) |   | ✓ |  | 
|  [ Amazon MSK broker logs](https://docs.aws.amazon.com/msk/latest/developerguide/msk-logging.html) |  ✓ | ✓ | ✓ | 
|  [ Amazon MSK Connect logs](https://docs.aws.amazon.com/msk/latest/developerguide/msk-connect-logging.html) |  ✓ | ✓ | ✓ | 
|  [AWS Network Firewall logs](https://docs.aws.amazon.com/network-firewall/latest/developerguide/firewall-logging.html) |  ✓ | ✓ | ✓ | 
|  [ Network Load Balancer access logs](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-access-logs.html) |   | ✓ |  | 
|  [ Amazon Route 53 resolver query logs](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-query-logs-choosing-target-resource.html) |  ✓ |  ✓ | ✓ | 
|  [ Amazon SageMaker events](https://docs.aws.amazon.com/sagemaker/latest/dg/logging-cloudwatch.html)  |  ✓ |   |   | 
|  [ Amazon SageMaker worker events](https://docs.aws.amazon.com/sagemaker/latest/dg/workteam-private-tracking.html)  |  ✓ |   |   | 
|  [ EC2 Spot Instance data feed files](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-data-feeds.html)  |   | ✓ |   | 
|  [AWS Step Functions Express Workflow and Standard Workflow logs](https://docs.aws.amazon.com/step-functions/latest/dg/cw-logs.html)  |  ✓ |   |   | 
|  [ Storage Gateway audit logs and health logs](https://docs.aws.amazon.com/storagegateway/latest/userguide/monitoring-file-gateway.html) |  ✓ |   |   | 
|  [ Amazon Virtual Private Cloud flow logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs-s3.html) |   | ✓ |   | 
|  [AWS WAF logs](https://docs.aws.amazon.com/waf/latest/developerguide/logging-destinations.html) | ✓ | ✓ | ✓ | 

The following sections provide more details for each of these destinations\.

## Logs sent to CloudWatch Logs<a name="AWS-logs-infrastructure-CWL"></a>

**Important**  
When you set up the log types in the following list to be sent to CloudWatch Logs, AWS creates or changes the resource policies associated with the log group receiving the logs, if needed\. Continue reading this section to see the details\.

This section applies when the types of logs listed in the table in the preceding section are sent to CloudWatch Logs:

**User permissions**

To be able to set up sending any of these types of logs to CloudWatch Logs for the first time, you must be logged into an account with the following permissions\.
+ `logs:CreateLogDelivery`
+ `logs:PutResourcePolicy`
+ `logs:DescribeResourcePolicies`
+ `logs:DescribeLogGroups`

If any of these types of logs is already being sent to a log group in CloudWatch Logs, then to set up the sending of another one of these types of logs to that same log group, you only need the `logs:CreateLogDelivery` permission\.

**Log group resource policy**

The log group where the logs are being sent must have a resource policy that includes certain permissions\. If the log group currently does not have a resource policy, and the user setting up the logging has the `logs:PutResourcePolicy`, `logs:DescribeResourcePolicies`, and `logs:DescribeLogGroups` permissions for the log group, then AWS automatically creates the following policy for it when you begin sending the logs to CloudWatch Logs\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AWSLogDeliveryWrite20150319",
      "Effect": "Allow",
      "Principal": {
        "Service": [
          "delivery.logs.amazonaws.com"
        ]
      },
      "Action": [
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": [
        "arn:aws:logs:us-east-1:0123456789:log-group:my-log-group:log-stream:*"
      ],
      "Condition": {
        "StringEquals": {
          "aws:SourceAccount": ["0123456789"]
        },
        "ArnLike": {
          "aws:SourceArn": ["arn:aws:logs:us-east-1:0123456789:*"]
        }
      }
    }
  ]
}
```

If the log group does have a resource policy but that policy doesn't contain the statement shown in the previous policy, and the user setting up the logging has the `logs:PutResourcePolicy`, `logs:DescribeResourcePolicies`, and `logs:DescribeLogGroups` permissions for the log group, that statement is appended to the log group's resource policy\.

**Log group resource policy size limit considerations**

These services must list each log group that they're sending logs to in the resource policy, and CloudWatch Logs resource policies are limited to 5120 characters\. A service that sends logs to a large number of log groups may run into this limit\.

To mitigate this, CloudWatch Logs monitors the size of resource policies used by the service that is sending logs, and when it detects that a policy approaches the size limit of 5120 characters, CloudWatch Logs automatically enables `/aws/vendedlogs/*` in the resource policy for that service\. You can then start using log groups with names that start with `/aws/vendedlogs/` as the destinations for logs from these services\.

## Logs sent to Amazon S3<a name="AWS-logs-infrastructure-S3"></a>

**Important**  
When you set up the log types in the following list to be sent to Amazon S3, AWS creates or changes the resource policies associated with the S3 bucket that is receiving the logs, if needed\. Continue reading this section to see the details\.

This section applies when the following types of logs are sent to Amazon S3:
+ CloudFront access logs and streaming access logs\. CloudFront uses a different permissions model than the other services in this list\. For more information, see [ Permissions required to configure standard logging and to access your log files](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/AccessLogs.html#AccessLogsBucketAndFileOwnership)\.
+ Amazon EC2 Spot Instance data feed
+ AWS Global Accelerator flow logs
+ Amazon Managed Streaming for Apache Kafka broker logs
+ Network Load Balancer access logs
+ AWS Network Firewall logs
+ Amazon Virtual Private Cloud flow logs

Logs published directly to Amazon S3 are published to an existing bucket that you specify\. One or more log files are created every five minutes in the specified bucket\.

When you deliver logs for the first time to an Amazon S3 bucket, the service that delivers logs records the owner of the bucket to ensure that the logs are delivered only to a bucket belonging to this account\. As a result, to change the Amazon S3 bucket owner, you must re\-create or update the log subscription in the originating service\.

**User permissions**

To be able to set up sending any of these types of logs to Amazon S3 for the first time, you must be logged into an account with the following permissions\.
+ `logs:CreateLogDelivery`
+ `S3:GetBucketPolicy`
+ `S3:PutBucketPolicy`

If any of these types of logs is already being sent to an Amazon S3 bucket, then to set up the sending of another one of these types of logs to the same bucket you only need to have the `logs:CreateLogDelivery` permission\.

**S3 bucket resource policy**

The S3 bucket where the logs are being sent must have a resource policy that includes certain permissions\. If the bucket currently does not have a resource policy and the user setting up the logging has the `S3:GetBucketPolicy` and `S3:PutBucketPolicy` permissions for the bucket, then AWS automatically creates the following policy for it when you begin sending the logs to Amazon S3\.

```
{
    "Version": "2012-10-17",
    "Id": "AWSLogDeliveryWrite20150319",
    "Statement": [
        {
            "Sid": "AWSLogDeliveryAclCheck",
            "Effect": "Allow",
            "Principal": {
                "Service": "delivery.logs.amazonaws.com"
                },
            "Action": "s3:GetBucketAcl",
            "Resource": "arn:aws:s3:::my-bucket",
            "Condition": {
                "StringEquals": {
                "aws:SourceAccount": ["0123456789"]
                },
                "ArnLike": {
                "aws:SourceArn": ["arn:aws:logs:us-east-1:0123456789:*"]
                }
            }
        },
        {
            "Sid": "AWSLogDeliveryWrite",
            "Effect": "Allow",
            "Principal": {
                "Service": "delivery.logs.amazonaws.com"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::my-bucket/AWSLogs/account-ID/*",
            "Condition": {
                "StringEquals": {
                    "s3:x-amz-acl": "bucket-owner-full-control",
                    "aws:SourceAccount": ["0123456789"]
                },
                "ArnLike": {
                    "aws:SourceArn": ["arn:aws:logs:us-east-1:0123456789:*"]
                }
            }
        }
    ]
}
```

In the previous policy, for `aws:SourceAccount`, specify the list of account IDS for which logs are being delivered to this bucket\. For `aws:SourceArn`, specify the list of ARNs of the resource that generates the logs, in the form `arn:aws:logs:source-region:source-account-id:*`\. 

If the bucket does have a resource policy but that policy doesn't contain the statement shown in the previous policy, and the user setting up the logging has the `S3:GetBucketPolicy` and `S3:PutBucketPolicy` permissions for the bucket, that statement is appended to the bucket's resource policy\.

### Amazon S3 bucket server\-side encryption<a name="AWS-logs-SSE-KMS-S3"></a>

You can protect the data in your Amazon S3 bucket by enabling either server\-side Encryption with Amazon S3\-managed keys \(SSE\-S3\) or server\-side encryption with a AWS KMS key stored in AWS Key Management Service \(SSE\-KMS\)\. For more information, see [ Protecting data using server\-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/serv-side-encryption.html)\. 

If you choose SSE\-S3, no additional configuration is required\. Amazon S3 handles the encryption key\.

If you choose SSE\-KMS, you must use a customer managed key, because using an AWS managed key is not supported for this scenario\. When you use a customer managed AWS KMS key, you can specify the Amazon Resource Name \(ARN\) of the customer managed key when you enable bucket encryption\. You must add the following to the key policy for your customer managed key \(not to the bucket policy for your S3 bucket\), so that the log delivery account can write to your S3 bucket\.

```
{
    "Sid": "Allow Logs Delivery to use the key", 
    "Effect": "Allow", 
    "Principal": {
        "Service": [ "delivery.logs.amazonaws.com" ] 
    }, 
    "Action": [ 
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*",
        "kms:DescribeKey"
    ],
    "Resource": "*",
    "Condition": {
        "StringEquals": {
            "aws:SourceAccount": ["0123456789"]
        },
        "ArnLike": {
            "aws:SourceArn": ["arn:aws:logs:us-east-1:0123456789:*"]
        }
        }
}
```

For `aws:SourceAccount`, specify the list of account IDS for which logs are being delivered to this bucket\. For `aws:SourceArn`, specify the list of ARNs of the resource that generates the logs, in the form `arn:aws:logs:source-region:source-account-id:*`\. 

## Logs sent to Kinesis Data Firehose<a name="AWS-logs-infrastructure-Firehose"></a>

This section applies when the types of logs listed in the table in the preceding section are sent to Kinesis Data Firehose:

**User permissions**

To be able to set up sending any of these types of logs to Kinesis Data Firehose for the first time, you must be logged into an account with the following permissions\.
+ `logs:CreateLogDelivery`
+ `firehose:TagDeliveryStream`
+ `iam:CreateServiceLinkedRole`

If any of these types of logs is already being sent to Kinesis Data Firehose, then to set up the sending of another one of these types of logs to Kinesis Data Firehose you need to have only the `logs:CreateLogDelivery` and `firehose:TagDeliveryStream` permissions\.

**IAM roles used for permissions**

Because Kinesis Data Firehose does not use resource policies, AWS uses IAM roles when setting up these logs to be sent to Kinesis Data Firehose\. AWS creates a service\-linked role named **AWSServiceRoleForLogDelivery**\. This service\-linked role includes the following permissions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "firehose:PutRecord",
                "firehose:PutRecordBatch",
                "firehose:ListTagsForDeliveryStream"
            ],
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "aws:ResourceTag/LogDeliveryEnabled": "true"
                }
            },
            "Effect": "Allow"
        }
    ]
}
```

This service\-linked role grants permission for all Kinesis Data Firehose delivery streams that have the `LogDeliveryEnabled` tag set to `true`\. AWS gives this tag to the destination delivery stream when you set up the logging\. 

This service\-linked role also has a trust policy that allows the `delivery.logs.amazonaws.com` service principal to assume the needed service\-linked role\. That trust policy is as follows:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "delivery.logs.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

## Cross\-service confused deputy prevention<a name="cross-service-confused-deputy-prevention"></a>

The confused deputy problem is a security issue where an entity that doesn't have permission to perform an action can coerce a more\-privileged entity to perform the action\. In AWS, cross\-service impersonation can result in the confused deputy problem\. Cross\-service impersonation can occur when one service \(the *calling service*\) calls another service \(the *called service*\)\. The calling service can be manipulated to use its permissions to act on another customer's resources in a way it should not otherwise have permission to access\. To prevent this, AWS provides tools that help you protect your data for all services with service principals that have been given access to resources in your account\. 

We recommend using the [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn) and [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount) global condition context keys in resource policies to limit the permissions that CloudWatch Logs and Amazon S3 give to the services that are generating logs\. If you use both global condition context keys, the `aws:SourceAccount` value and the account in the `aws:SourceArn` value must use the same account ID when used in the same policy statement\.

The values of `aws:SourceArn` must be the ARNs of the AWS resources that are generating logs\.

The most effective way to protect against the confused deputy problem is to use the `aws:SourceArn` global condition context key with the full ARN of the resource\. If you don't know the full ARN of the resource or if you are specifying multiple resources, use the `aws:SourceArn` global context condition key with wildcards \(`*`\) for the unknown portions of the ARN\.

The policies in the previous sections of this page show how you can use the `aws:SourceArn` and `aws:SourceAccount` global condition context keys to prevent the confused deputy problem\.

## CloudWatch Logs updates to AWS managed policies<a name="cwl-slrpolicy-updates"></a>



View details about updates to AWS managed policies for CloudWatch Logs since this service began tracking these changes\. For automatic alerts about changes to this page, subscribe to the RSS feed on the CloudWatch Logs Document history page\.




| Change | Description | Date | 
| --- | --- | --- | 
|  [ AWSServiceRoleForLogDelivery service\-linked role policy](#AWS-logs-infrastructure-Firehose) – Update to an existing policy  |  CloudWatch Logs changed the permissions in the IAM policy associated with the **AWSServiceRoleForLogDelivery** service\-linked role\. The following change was made: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AWS-logs-and-resource-policy.html)  | July 15, 2021 | 
|  CloudWatch Logs started tracking changes  |  CloudWatch Logs started tracking changes for its AWS managed policies\.  | June 10, 2021 | 