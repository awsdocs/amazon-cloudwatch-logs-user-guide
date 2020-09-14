# Using Identity\-Based Policies \(IAM Policies\) for CloudWatch Logs<a name="iam-identity-based-access-control-cwl"></a>

This topic provides examples of identity\-based policies in which an account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\)\.

**Important**  
We recommend that you first review the introductory topics that explain the basic concepts and options available for you to manage access to your CloudWatch Logs resources\. For more information, see [Overview of Managing Access Permissions to Your CloudWatch Logs Resources](iam-access-control-overview-cwl.md)\.

This topic covers the following:
+ [Permissions Required to Use the CloudWatch Console](#console-permissions-cwl)
+ [AWS Managed \(Predefined\) Policies for CloudWatch Logs](#managed-policies-cwl)
+ [Customer Managed Policy Examples](#customer-managed-policies-cwl)

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

The wildcard character \(\*\) at the end of the `Resource` value means that the statement allows permission for the `logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents`, and `logs:DescribeLogStreams` actions on any log group\. To limit this permission to a specific log group, replace the wildcard character \(\*\) in the resource ARN with the specific log group ARN\. For more information about the sections within an IAM policy statement, see [IAM Policy Elements Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/AccessPolicyLanguage_ElementDescriptions.html) in *IAM User Guide*\. For a list showing all of the CloudWatch Logs actions, see [CloudWatch Logs Permissions Reference](permissions-reference-cwl.md)\.

## Permissions Required to Use the CloudWatch Console<a name="console-permissions-cwl"></a>

For a user to work with CloudWatch Logs in the CloudWatch console, that user must have a minimum set of permissions that allows the user to describe other AWS resources in their AWS account\. In order to use CloudWatch Logs in the CloudWatch console, you must have permissions from the following services:
+ CloudWatch
+ CloudWatch Logs
+ Amazon ES
+ IAM
+ Kinesis
+ Lambda
+ Amazon S3

If you create an IAM policy that is more restrictive than the minimum required permissions, the console won't function as intended for users with that IAM policy\. To ensure that those users can still use the CloudWatch console, also attach the `CloudWatchReadOnlyAccess` managed policy to the user, as described in [AWS Managed \(Predefined\) Policies for CloudWatch Logs](#managed-policies-cwl)\.

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

## AWS Managed \(Predefined\) Policies for CloudWatch Logs<a name="managed-policies-cwl"></a>

AWS addresses many common use cases by providing standalone IAM policies that are created and administered by AWS\. Managed policies grant necessary permissions for common use cases so you can avoid having to investigate what permissions are needed\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

The following AWS managed policies, which you can attach to users in your account, are specific to CloudWatch Logs:
+ **CloudWatchLogsFullAccess** – Grants full access to CloudWatch Logs\.
+ **CloudWatchLogsReadOnlyAccess** – Grants read\-only access to CloudWatch Logs\.

**Note**  
You can review these permissions policies by signing in to the IAM console and searching for specific policies there\.

You can also create your own custom IAM policies to allow permissions for CloudWatch Logs actions and resources\. You can attach these custom policies to the IAM users or groups that require those permissions\.

## Customer Managed Policy Examples<a name="customer-managed-policies-cwl"></a>

In this section, you can find example user policies that grant permissions for various CloudWatch Logs actions\. These policies work when you are using the CloudWatch Logs API, AWS SDKs, or the AWS CLI\.

**Topics**
+ [Example 1: Allow Full Access to CloudWatch Logs](#w28aac27c15c15c23b7)
+ [Example 2: Allow Read\-Only Access to CloudWatch Logs](#w28aac27c15c15c23b9)
+ [Example 3: Allow Access to One Log Group](#w28aac27c15c15c23c11)

### Example 1: Allow Full Access to CloudWatch Logs<a name="w28aac27c15c15c23b7"></a>

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

### Example 2: Allow Read\-Only Access to CloudWatch Logs<a name="w28aac27c15c15c23b9"></a>

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

### Example 3: Allow Access to One Log Group<a name="w28aac27c15c15c23c11"></a>

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

## Use Tagging and IAM Policies for Control at the Log Group Level<a name="cwl-IAM-policy-tagging"></a>

You can grant users access to certain log groups while preventing them from accessing other log groups\. To do so, tag your log groups and use IAM policies that refer to those tags\.

For more information about tagging log groups, see [Tag Log Groups in Amazon CloudWatch Logs](Working-with-log-groups-and-streams.md#log-group-tagging)\.

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
                    "logs:ResourceTag/Team": "Green"
                }
            }
        }
    ]
}
```

For more information about using IAM policy statements, see [Controlling Access Using Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_controlling.html) in the *IAM User Guide*\.