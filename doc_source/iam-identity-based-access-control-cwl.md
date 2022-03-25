# Using identity\-based policies \(IAM policies\) for CloudWatch Logs<a name="iam-identity-based-access-control-cwl"></a>

This topic provides examples of identity\-based policies in which an account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\)\.

**Important**  
We recommend that you first review the introductory topics that explain the basic concepts and options available for you to manage access to your CloudWatch Logs resources\. For more information, see [Overview of managing access permissions to your CloudWatch Logs resources](iam-access-control-overview-cwl.md)\.

**Note**  
CloudWatch Logs doesn’t support IAM policies that prevent users from assigning specified tags to log groups using the `aws:Resource/key-name` or `aws:TagKeys` condition keys\. Additionally, you can’t control access to the `DescribeLogGroups` action by using the `aws:ResourceTag/key-name` condition key\. Other CloudWatch Logs actions do support the use of the `aws:ResourceTag/key-name` condition key to control access\. For more information about using tags to control access, see [Controlling access to Amazon Web Services resources using tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_tags.html)\.

This topic covers the following:
+ [Permissions required to use the CloudWatch console](#console-permissions-cwl)
+ [AWS managed \(predefined\) policies for CloudWatch Logs](#managed-policies-cwl)
+ [Customer managed policy examples](#customer-managed-policies-cwl)

The following is an example of a permissions policy:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents",
        "logs:DescribeLogStreams"
    ],
      "Resource": [
        "arn:aws:logs:*:*:*"
    ]
  }
 ]
}
```

This policy has one statement that grants permissions to create log groups and log streams, to upload log events to log streams, and to list details about log streams\.

The wildcard character \(\*\) at the end of the `Resource` value means that the statement allows permission for the `logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents`, and `logs:DescribeLogStreams` actions on any log group\. To limit this permission to a specific log group, replace the wildcard character \(\*\) in the resource ARN with the specific log group ARN\. For more information about the sections within an IAM policy statement, see [IAM Policy Elements Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/AccessPolicyLanguage_ElementDescriptions.html) in *IAM User Guide*\. For a list showing all of the CloudWatch Logs actions, see [CloudWatch Logs permissions reference](permissions-reference-cwl.md)\.

## Permissions required to use the CloudWatch console<a name="console-permissions-cwl"></a>

For a user to work with CloudWatch Logs in the CloudWatch console, that user must have a minimum set of permissions that allows the user to describe other AWS resources in their AWS account\. In order to use CloudWatch Logs in the CloudWatch console, you must have permissions from the following services:
+ CloudWatch
+ CloudWatch Logs
+ OpenSearch Service
+ IAM
+ Kinesis
+ Lambda
+ Amazon S3

If you create an IAM policy that is more restrictive than the minimum required permissions, the console won't function as intended for users with that IAM policy\. To ensure that those users can still use the CloudWatch console, also attach the `CloudWatchReadOnlyAccess` managed policy to the user, as described in [AWS managed \(predefined\) policies for CloudWatch Logs](#managed-policies-cwl)\.

You don't need to allow minimum console permissions for users that are making calls only to the AWS CLI or the CloudWatch Logs API\.

The full set of permissions required to work with the CloudWatch console for a user who is not using the console to manage log subscriptions are:
+ cloudwatch:getMetricData
+ cloudwatch:listMetrics
+ logs:cancelExportTask
+ logs:createExportTask
+ logs:createLogGroup
+ logs:createLogStream
+ logs:deleteLogGroup
+ logs:deleteLogStream
+ logs:deleteMetricFilter
+ logs:deleteQueryDefinition
+ logs:deleteRetentionPolicy
+ logs:deleteSubscriptionFilter
+ logs:describeExportTasks
+ logs:describeLogGroups
+ logs:describeLogStreams
+ logs:describeMetricFilters
+ logs:describeQueryDefinitions
+ logs:describeSubscriptionFilters
+ logs:filterLogEvents
+ logs:getLogEvents
+ logs:putMetricFilter
+ logs:putQueryDefinition
+ logs:putRetentionPolicy
+ logs:putSubscriptionFilter
+ logs:testMetricFilter

For a user who will also be using the console to manage log subscriptions, the following permissions are also required:
+ es:describeElasticsearchDomain
+ es:listDomainNames
+ iam:attachRolePolicy
+ iam:createRole
+ iam:getPolicy
+ iam:getPolicyVersion
+ iam:getRole
+ iam:listAttachedRolePolicies
+ iam:listRoles
+ kinesis:describeStreams
+ kinesis:listStreams
+ lambda:addPermission
+ lambda:createFunction
+ lambda:getFunctionConfiguration
+ lambda:listAliases
+ lambda:listFunctions
+ lambda:listVersionsByFunction
+ lambda:removePermission
+ s3:listBuckets

## AWS managed \(predefined\) policies for CloudWatch Logs<a name="managed-policies-cwl"></a>

AWS addresses many common use cases by providing standalone IAM policies that are created and administered by AWS\. Managed policies grant necessary permissions for common use cases so you can avoid having to investigate what permissions are needed\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

The following AWS managed policies, which you can attach to users and roles in your account, are specific to CloudWatch Logs:
+ **CloudWatchLogsFullAccess** – Grants full access to CloudWatch Logs\.
+ **CloudWatchLogsReadOnlyAccess** – Grants read\-only access to CloudWatch Logs\.

### CloudWatchLogsFullAccess<a name="managed-policies-cwl-CloudWatchLogsFullAccess"></a>

The **CloudWatchLogsFullAccess** policy grants full access to CloudWatch Logs\. The contents are as follows:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "logs:*"
            ],
            "Effect": "Allow",
            "Resource": "*"
        }
    ]
}
```

### CloudWatchLogsReadOnlyAccess<a name="managed-policies-cwl-CloudWatchLogsReadOnlyAccess"></a>

The **CloudWatchLogsReadOnlylAccess** policy grants read\-only access to CloudWatch Logs\. The contents are as follows:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "logs:Describe*",
                "logs:Get*",
                "logs:List*",
                "logs:StartQuery",
                "logs:StopQuery",
                "logs:TestMetricFilter",
                "logs:FilterLogEvents"
            ],
            "Effect": "Allow",
            "Resource": "*"
        }
    ]
}
```

## Customer managed policy examples<a name="customer-managed-policies-cwl"></a>

You can create your own custom IAM policies to allow permissions for CloudWatch Logs actions and resources\. You can attach these custom policies to the IAM users or groups that require those permissions\.

In this section, you can find example user policies that grant permissions for various CloudWatch Logs actions\. These policies work when you are using the CloudWatch Logs API, AWS SDKs, or the AWS CLI\.

**Topics**
+ [Example 1: Allow full access to CloudWatch Logs](#w196aac36c16c15c25b9)
+ [Example 2: Allow read\-only access to CloudWatch Logs](#w196aac36c16c15c25c11)
+ [Example 3: Allow access to one log group](#w196aac36c16c15c25c13)

### Example 1: Allow full access to CloudWatch Logs<a name="w196aac36c16c15c25b9"></a>

The following policy allows a user to access all CloudWatch Logs actions\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "logs:*"
      ],
      "Effect": "Allow",
      "Resource": "*"
    }
  ]
}
```

### Example 2: Allow read\-only access to CloudWatch Logs<a name="w196aac36c16c15c25c11"></a>

AWS provides a **CloudWatchLogsReadOnlyAccess** policy that enables read\-only access to CloudWatch Logs data\. This policy includes the following permissions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "logs:Describe*",
                "logs:Get*",
                "logs:List*",
                "logs:StartQuery",
                "logs:StopQuery",
                "logs:TestMetricFilter",
                "logs:FilterLogEvents"
            ],
            "Effect": "Allow",
            "Resource": "*"
        }
    ]
}
```

### Example 3: Allow access to one log group<a name="w196aac36c16c15c25c13"></a>

The following policy allows a user to read and write log events in one specified log group\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
      "Action": [
        "logs:CreateLogStream",
        "logs:DescribeLogStreams",
        "logs:PutLogEvents",
        "logs:GetLogEvents"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:logs:us-west-2:123456789012:log-group:SampleLogGroupName:*"
      }
   ]
}
```

## Use tagging and IAM policies for control at the log group level<a name="cwl-IAM-policy-tagging"></a>

You can grant users access to certain log groups while preventing them from accessing other log groups\. To do so, tag your log groups and use IAM policies that refer to those tags\.

For more information about tagging log groups, see [Tag log groups in Amazon CloudWatch Logs](Working-with-log-groups-and-streams.md#log-group-tagging)\.

When you tag log groups, you can then grant an IAM policy to a user to allow access to only the log groups with a particular tag\. For example, the following policy statement grants access to only log groups with the value of `Green` for the tag key `Team`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "logs:*"
            ],
            "Effect": "Allow",
            "Resource": "*",
            "Condition": {
                "StringLike": {
                    "aws:ResourceTag/Team": "Green"
                }
            }
        }
    ]
}
```

For more information about using IAM policy statements, see [Controlling Access Using Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_controlling.html) in the *IAM User Guide*\.