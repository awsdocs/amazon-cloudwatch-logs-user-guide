# Overview of Managing Access Permissions to Your CloudWatch Logs Resources<a name="iam-access-control-overview-cwl"></a>

Every AWS resource is owned by an AWS account, and permissions to create or access a resource are governed by permissions policies\. An account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\), and some services \(such as AWS Lambda\) also support attaching permissions policies to resources\. 

**Note**  
An *account administrator* \(or administrator IAM user\) is a user with administrator privileges\. For more information, see [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

When granting permissions, you decide who is getting the permissions, the resources they get permissions for, and the specific actions that you want to allow on those resources\.

**Topics**
+ [CloudWatch Logs Resources and Operations](#CWL_ARN_Format)
+ [Understanding Resource Ownership](#understanding-resource-ownership-cwl)
+ [Managing Access to Resources](#managing-access-resources-cwl)
+ [Specifying Policy Elements: Actions, Effects, and Principals](#actions-effects-principals-cwl)
+ [Specifying Conditions in a Policy](#policy-conditions-cwl)

## CloudWatch Logs Resources and Operations<a name="CWL_ARN_Format"></a>

In CloudWatch Logs the primary resources are log groups, log streams and destinations\. CloudWatch Logs does not support subresources \(other resources for use with the primary resource\)\.

These resources and subresources have unique Amazon Resource Names \(ARNs\) associated with them as shown in the following table\.


| Resource Type | ARN Format | 
| --- | --- | 
|  Log group  |  arn:aws:logs:*region*:*account\-id*:log\-group:*log\_group\_name*  | 
|  Log stream  |  arn:aws:logs:*region*:*account\-id*:log\-group:*log\_group\_name*:log\-stream:*log\-stream\-name*  | 
|  Destination  |  arn:aws:logs:*region*:*account\-id*:destination:*destination\_name*  | 

For more information about ARNs, see [ARNs](https://docs.aws.amazon.com/IAM/latest/UserGuide/Using_Identifiers.html#Identifiers_ARNs) in *IAM User Guide*\. For information about CloudWatch Logs ARNs, see [Amazon Resource Names \(ARNs\) and AWS Service Namespaces](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-cloudwatch-logs) in *Amazon Web Services General Reference*\. For an example of a policy that covers CloudWatch Logs, see [Using Identity\-Based Policies \(IAM Policies\) for CloudWatch Logs](iam-identity-based-access-control-cwl.md)\.

CloudWatch Logs provides a set of operations to work with the CloudWatch Logs resources\. For a list of available operations, see [CloudWatch Logs Permissions Reference](permissions-reference-cwl.md)\.

## Understanding Resource Ownership<a name="understanding-resource-ownership-cwl"></a>

The AWS account owns the resources that are created in the account, regardless of who created the resources\. Specifically, the resource owner is the AWS account of the [principal entity](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html) \(that is, the root account, an IAM user, or an IAM role\) that authenticates the resource creation request\. The following examples illustrate how this works:
+ If you use the root account credentials of your AWS account to create a log group, your AWS account is the owner of the CloudWatch Logs resource\.
+ If you create an IAM user in your AWS account and grant permissions to create CloudWatch Logs resources to that user, the user can create CloudWatch Logs resources\. However, your AWS account, to which the user belongs, owns the CloudWatch Logs resources\.
+ If you create an IAM role in your AWS account with permissions to create CloudWatch Logs resources, anyone who can assume the role can create CloudWatch Logs resources\. Your AWS account, to which the role belongs, owns the CloudWatch Logs resources\.

## Managing Access to Resources<a name="managing-access-resources-cwl"></a>

A *permissions policy* describes who has access to what\. The following section explains the available options for creating permissions policies\.

**Note**  
This section discusses using IAM in the context of CloudWatch Logs\. It doesn't provide detailed information about the IAM service\. For complete IAM documentation, see [What Is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*\. For information about IAM policy syntax and descriptions, see [IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

Policies attached to an IAM identity are referred to as identity\-based policies \(IAM polices\) and policies attached to a resource are referred to as resource\-based policies\. CloudWatch Logs supports identity\-based policies, and resource\-based policies for destinations, which are used to enable cross account subscriptions\. For more information, see [Cross\-Account Log Data Sharing with Subscriptions](CrossAccountSubscriptions.md)\.

**Topics**
+ [Log Group Permissions and Contributor Insights](#cloudwatch-logs-permissions-and-contributor-insights)
+ [Identity\-Based Policies \(IAM Policies\)](#identity-based-policies-cwl)
+ [Resource\-Based Policies](#resource-based-policies-cwl)

### Log Group Permissions and Contributor Insights<a name="cloudwatch-logs-permissions-and-contributor-insights"></a>

Contributor Insights is a feature of CloudWatch that enables you to analyze data from log groups and create time series that display contributor data\. You can see metrics about the top\-N contributors, the total number of unique contributors, and their usage\. For more information, see [ Using Contributor Insights to Analyze High\-Cardinality Data](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContributorInsights.html)\.

When you grant a user the `cloudwatch:PutInsightRule` and `cloudwatch:GetInsightRuleReport` permissions, that user can create a rule that evaluates any log group in CloudWatch Logs and then see the results\. The results can contain contributor data for those log groups\. Be sure to grant these permissions only to users who should be able to view this data\.

### Identity\-Based Policies \(IAM Policies\)<a name="identity-based-policies-cwl"></a>

You can attach policies to IAM identities\. For example, you can do the following: 
+ **Attach a permissions policy to a user or a group in your account** – To grant a user permissions to view logs in the CloudWatch Logs console, you can attach a permissions policy to a user or group that the user belongs to\.
+ **Attach a permissions policy to a role \(grant cross\-account permissions\)** – You can attach an identity\-based permissions policy to an IAM role to grant cross\-account permissions\. For example, the administrator in Account A can create a role to grant cross\-account permissions to another AWS account \(for example, Account B\) or an AWS service as follows:

  1. Account A administrator creates an IAM role and attaches a permissions policy to the role that grants permissions on resources in Account A\.

  1. Account A administrator attaches a trust policy to the role identifying Account B as the principal who can assume the role\. 

  1. Account B administrator can then delegate permissions to assume the role to any users in Account B\. Doing this allows users in Account B to create or access resources in Account A\. The principal in the trust policy an also be an AWS service principal if you want to grant an AWS service permissions to assume the role\.

  For more information about using IAM to delegate permissions, see [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) in the *IAM User Guide*\.

The following is an example policy that grants permissions for the `logs:PutLogEvents`, `logs:CreateLogGroup`, and `logs:CreateLogStream` actions on all resources in us\-east\-1\. For log groups, CloudWatch Logs supports identifying specific resources using the resource ARNs \(also referred to as resource\-level permissions\) for some of the API actions\. If you want to include all log groups, you must specify the wildcard character \(\*\)\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Sid":"",
         "Effect":"Allow",
         "Action":[
            "logs:PutLogEvents",
            "logs:CreateLogGroup",
            "logs:CreateLogStream"
         ],
         "Resource":"arn:aws:logs:us-east-1:*:*"
      }
   ]
}
```

For more information about using identity\-based policies with CloudWatch Logs, see [Using Identity\-Based Policies \(IAM Policies\) for CloudWatch Logs](iam-identity-based-access-control-cwl.md)\. For more information about users, groups, roles, and permissions, see [Identities \(Users, Groups, and Roles\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html) in the *IAM User Guide*\.

### Resource\-Based Policies<a name="resource-based-policies-cwl"></a>

CloudWatch Logs supports resource\-based policies for destinations, which you can use to enable cross account subscriptions\. For more information, see [Create a Destination](CreateDestination.md)\. Destinations can be created using the [PutDestination](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutDestination.html) API, and you can add a resource policy to the destination using the [PutDestination](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/PutDestinationPolicy.html) API\. The following example allows another AWS account with the account ID 111122223333 to subscribe their log groups to the destination `arn:aws:logs:us-east-1:123456789012:destination:testDestination`\.

```
{
  "Version" : "2012-10-17",
  "Statement" : [
    {
      "Sid" : "",
      "Effect" : "Allow",
      "Principal" : {
        "AWS" : "111122223333"
      },
      "Action" : "logs:PutSubscriptionFilter",
      "Resource" : "arn:aws:logs:us-east-1:123456789012:destination:testDestination"
    }
  ]
}
```

## Specifying Policy Elements: Actions, Effects, and Principals<a name="actions-effects-principals-cwl"></a>

 For each CloudWatch Logs resource, the service defines a set of API operations\. To grant permissions for these API operations, CloudWatch Logs defines a set of actions that you can specify in a policy\. Some API operations can require permissions for more than one action in order to perform the API operation\. For more information about resources and API operations, see [CloudWatch Logs Resources and Operations](#CWL_ARN_Format) and [CloudWatch Logs Permissions Reference](permissions-reference-cwl.md)\.

The following are the basic policy elements:
+ **Resource** – You use an Amazon Resource Name \(ARN\) to identify the resource that the policy applies to\. For more information, see [CloudWatch Logs Resources and Operations](#CWL_ARN_Format)\.
+ **Action** – You use action keywords to identify resource operations that you want to allow or deny\. For example, the `logs.DescribeLogGroups` permission allows the user permissions to perform the `DescribeLogGroups` operation\.
+ **Effect** – You specify the effect, either allow or deny, when the user requests the specific action\. If you don't explicitly grant access to \(allow\) a resource, access is implicitly denied\. You can also explicitly deny access to a resource, which you might do to make sure that a user cannot access it, even if a different policy grants access\.
+ **Principal** – In identity\-based policies \(IAM policies\), the user that the policy is attached to is the implicit principal\. For resource\-based policies, you specify the user, account, service, or other entity that you want to receive permissions \(applies to resource\-based policies only\)\. CloudWatch Logs supports resource\-based policies for destinations\.

To learn more about IAM policy syntax and descriptions, see [AWS IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

For a table showing all of the CloudWatch Logs API actions and the resources that they apply to, see [CloudWatch Logs Permissions Reference](permissions-reference-cwl.md)\.

## Specifying Conditions in a Policy<a name="policy-conditions-cwl"></a>

When you grant permissions, you can use the access policy language to specify the conditions when a policy should take effect\. For example, you might want a policy to be applied only after a specific date\. For more information about specifying conditions in a policy language, see [Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) in the *IAM User Guide*\.

To express conditions, you use predefined condition keys\. For a list of context keys supported by each AWS service and a list of AWS\-wide policy keys, see [AWS Service Actions and Condition Context Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_actionsconditions.html) and [Global and IAM Condition Context Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html) in the *IAM User Guide*\.