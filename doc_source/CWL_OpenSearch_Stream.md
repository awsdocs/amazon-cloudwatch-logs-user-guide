# Streaming CloudWatch Logs data to Amazon OpenSearch Service<a name="CWL_OpenSearch_Stream"></a>

You can configure a CloudWatch Logs log group to stream data it receives to your Amazon OpenSearch Service cluster in near real\-time through a CloudWatch Logs subscription\. For more information, see [Real\-time processing of log data with subscriptions](Subscriptions.md)\.

Depending on the amount of log data being streamed, you might want to set a function\-level concurrent execution limit on the function\. For more information, see [Lambda function scaling](https://docs.aws.amazon.com/lambda/latest/dg/concurrent-executions.html#per-function-concurrency)\.

**Note**  
Streaming large amounts of CloudWatch Logs data to OpenSearch might result in high usage charges\. We recommend that you create a Budget in the Billing and Cost Management console\. For more information, see [Managing your costs with AWS Budgets](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/budgets-managing-costs.html)\.

## Prerequisites<a name="CWL_OpenSearch_Domain"></a>

Before you begin, create an OpenSearch domain\. The domain can have either public access or VPC access, but you can't then modify the type of access after the domain is created\. You might want to review your OpenSearch domain settings later, and modify your cluster configuration based on the amount of data your cluster will be processing\. For instructions to create a domain, see [Creating OpenSearch Service domains](https://docs.aws.amazon.com/opensearch-service/latest/developerguide/createupdatedomains.html#createdomains)\.

For more information about OpenSearch, see the [Amazon OpenSearch Service Developer Guide](https://docs.aws.amazon.com/opensearch-service/latest/developerguide/)\.

## Subscribe a log group to OpenSearch<a name="CWL_OpenSearch_LG"></a>

You can use the CloudWatch console to subscribe a log group to OpenSearch\.

**To subscribe a log group to OpenSearch**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Log groups**\.

1. Select the name of the log group\.

1. Choose **Actions**, **Create OpenSearch subscription filter**\.

1. Choose whether you want to stream to a cluster in this account or another account\.
   + If you chose this account, select the domain you created in the previous step\.
   + If you chose another account, provide the domain ARN and endpoint\.

1. For **Lambda IAM Execution Role**, choose the IAM role that Lambda should use when executing calls to OpenSearch\.

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
   + If the target OpenSearch domain uses VPC access, the role must have the ** AWSLambdaVPCAccessExecutionRole** policy attached\. This Amazon\-managed policy grants Lambda access to the customer's VPC, enabling Lambda to write to the OpenSearch endpoint in the VPC\. 

1. For **Log format**, choose a log format\.

1. For **Subscription filter pattern**, type the terms or pattern to find in your log events\. This ensures that you send only the data you're interested in to your OpenSearch cluster\. For more information, see [Creating metrics from log events using filters](MonitoringLogData.md)\.

1. \(Optional\) For **Select log data to test**, select a log stream and then choose **Test pattern** to verify that your search filter is returning the results you expect\.

1. Choose **Start streaming**\.