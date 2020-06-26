# Streaming CloudWatch Logs Data to Amazon Elasticsearch Service<a name="CWL_ES_Stream"></a>

You can configure a CloudWatch Logs log group to stream data it receives to your Amazon Elasticsearch Service \(Amazon ES\) cluster in near real\-time through a CloudWatch Logs subscription\. For more information, see [Real\-time Processing of Log Data with Subscriptions](Subscriptions.md)\.

Depending on the amount of log data being streamed, you might want to set a function\-level concurrent execution limit on the function\. For more information, see [Function Level Concurrent Execution Limit](https://docs.aws.amazon.com/lambda/latest/dg/concurrent-executions.html#per-function-concurrency)\.

**Note**  
Streaming large amounts of CloudWatch Logs data to Amazon ES might result in high usage charges\. We recommend that you create a Budget in the Billing and Cost Management console\. For more information, see [Managing Your Costs with Budgets](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/budgets-managing-costs.html)\.

## Prerequisites<a name="CWL_ES_Domain"></a>

Before you begin, create an Amazon ES domain\. The Amazon ES domain can have either public access or VPC access, but you cannot then modify the type of access after the domain is created\. You might want to review your Amazon ES domain settings later, and modify your cluster configuration based on the amount of data your cluster will be processing\.

For more information about Amazon ES, see the [Amazon Elasticsearch Service Developer Guide](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/)\.

**To create an Amazon ES domain**  
At a command prompt, use the following [create\-elasticsearch\-domain](https://docs.aws.amazon.com/cli/latest/reference/es/create-elasticsearch-domain.html) command:

```
aws es create-elasticsearch-domain --domain-name my-domain
```

## Subscribe a Log Group to Amazon ES<a name="CWL_ES_LG"></a>

You can use the CloudWatch console to subscribe a log group to Amazon ES\.

**To subscribe a log group to Amazon ES**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Log groups**\.

1. Choose the name of the log group\.

1. Choose **Actions**, **Create Elastisearch subscription filter**\.

1. Choose whether you want to stream to a cluster in this account or another account\.

1. For **Amazon ES cluster**, choose the cluster you created in the previous step\.

1. Under **Lambda Function**, for **Lambda IAM Execution Role**, choose the IAM role that Lambda should use when executing calls to Amazon ES, and then choose **Next**\.

   The IAM role you choose must fulfill these requirements:
   + It must have `lambda.amazonaws.com` in the trust relationship\.
   + It must include the following policy:

     ```
     {
         "Version": "2012-10-17",
         "Statement": [
             {
                 "Action": [
                     "es:*"
                 ],
                 "Effect": "Allow",
                 "Resource": "arn:aws:es:region:account-id:domain/target-domain-name/*"
             }
         ]
     }
     ```
   + If the target Amazon ES domain uses VPC access, the role must have the ** AWSLambdaVPCAccessExecutionRole** policy attached\. This Amazon\-managed policy grants Lambda access to the customer's VPC, enabling Lambda to write to the Amazon ES endpoint in the VPC\. 

1. For **Log Format**, choose a log format\.

1. For **Subscription Filter Pattern**, type the terms or pattern to find in your log events\. This ensures that you send only the data you are interested in to your Amazon ES cluster\. For more information, see [Creating Metrics From Log Events Using Filters](MonitoringLogData.md)\.

1. \(Optional\) For **Select Log Data to Test**, select a log stream and then choose **Test Pattern** to verify that your search filter is returning the results you expect\.

1. Choose **Start Streaming**\.