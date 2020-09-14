# Using Service\-Linked Roles for CloudWatch Logs<a name="using-service-linked-roles-cwl"></a>

Amazon CloudWatch Logs uses AWS Identity and Access Management \(IAM\)[ service\-linked roles](IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role)\. A service\-linked role is a unique type of IAM role that is linked directly to CloudWatch Logs\. Service\-linked roles are predefined by CloudWatch Logs and include all the permissions that the service requires to call other AWS services on your behalf\. 

A service\-linked role makes setting up CloudWatch Logs more efficient because you aren't required to manually add the necessary permissions\. CloudWatch Logs defines the permissions of its service\-linked roles, and unless defined otherwise, only CloudWatch Logs can assume those roles\. The defined permissions include the trust policy and the permissions policy\. That permissions policy cannot be attached to any other IAM entity\.

For information about other services that support service\-linked roles, see [AWS Services That Work with IAM](IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html)\. Look for the services that have **Yes **in the **Service\-Linked Role** column\. Choose a **Yes** with a link to view the service\-linked role documentation for that service\.

## Service\-Linked Role Permissions for CloudWatch Logs<a name="slr-permissions"></a>

CloudWatch Logs uses the service\-linked role named **AWSServiceRoleForLogDelivery**\. CloudWatch Logs uses this service\-linked role to write logs directly to Kinesis Data Firehose\. For more information, see [Sending Logs Directly to Amazon S3 or Kinesis Data Firehose](Sending-Logs-Directly-To-S3.md)\.

The **AWSServiceRoleForLogDelivery** service\-linked role trusts the following services to assume the role:
+ `CloudWatch Logs`

The role permissions policy allows CloudWatch Logs to complete the following actions on the specified resources:
+ Action: `firehose:PutRecord` and `firehose:PutRecordBatch` on all Kinesis Data Firehose streams that have a tag with a `LogDeliveryEnabled` key with a value of `True`\. This tag is automatically attached to an Kinesis Data Firehose stream when you create a subscription to deliver the logs to Kinesis Data Firehose\.

You must configure permissions to allow an IAM entity to create, edit, or delete a service\-linked role\. This entity could be a user, group, or role\. For more information, see [Service\-Linked Role Permissions](IAM/latest/UserGuide/using-service-linked-roles.html#service-linked-role-permissions) in the *IAM User Guide*\.

## Creating a Service\-Linked Role for CloudWatch Logs<a name="create-slr"></a>

You aren't required to manually create a service\-linked role\. When you set up logs to be sent directly to a Kinesis Data Firehose stream in the AWS Management Console, the AWS CLI, or the AWS API, CloudWatch Logs creates the service\-linked role for you\. 

If you delete this service\-linked role, and then need to create it again, you can use the same process to recreate the role in your account\. When you again set up logs to be sent directly to a Kinesis Data Firehose stream, CloudWatch Logs creates the service\-linked role for you again\. 

## Editing a Service\-Linked Role for CloudWatch Logs<a name="edit-slr"></a>

CloudWatch Logs does not allow you to edit **AWSServiceRoleForLogDelivery**, or any other service\-linked role, after you create it\. You cannot change the name of the role because various entities might reference the role\. However, you can edit the description of the role using IAM\. For more information, see [Editing a Service\-Linked Role](IAM/latest/UserGuide/using-service-linked-roles.html#edit-service-linked-role) in the *IAM User Guide*\.

## Deleting a Service\-Linked Role for CloudWatch Logs<a name="delete-slr"></a>

If you no longer need to use a feature or service that requires a service\-linked role, we recommend that you delete that role\. That way you don’t have an unused entity that is not actively monitored or maintained\. However, you must clean up the resources for your service\-linked role before you can manually delete it\.

**Note**  
If the CloudWatch Logs service is using the role when you try to delete the resources, then the deletion might fail\. If that happens, wait for a few minutes and try the operation again\.

**To delete CloudWatch Logs resources used by the **AWSServiceRoleForLogDelivery** service\-linked role**
+ Stop sending logs directly to Kinesis Data Firehose streams\.

**To manually delete the service\-linked role using IAM**

Use the IAM console, the AWS CLI, or the AWS API to delete the **AWSServiceRoleForLogDelivery** service\-linked role\. For more information, see [Deleting a Service\-Linked Role](IAM/latest/UserGuide/using-service-linked-roles.html#delete-service-linked-role)

### Supported Regions for CloudWatch Logs Service\-Linked Roles<a name="slr-regions"></a>

CloudWatch Logs supports using service\-linked roles in all of the AWS Regions where the service is available\. For more information, see [CloudWatch Logs Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#cwl_region)\.