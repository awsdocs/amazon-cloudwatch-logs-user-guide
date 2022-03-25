# Use scheduled events to invoke a Lambda function<a name="example_cross_LambdaScheduledEvents_section"></a>

The following code examples show how to create an AWS Lambda function invoked by an Amazon EventBridge scheduled event\.

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
 This example shows how to register an AWS Lambda function as the target of a scheduled Amazon EventBridge event\. The Lambda handler writes a friendly message and the full event data to Amazon CloudWatch Logs for later retrieval\.   
+ Deploys a Lambda function\.
+ Creates an EventBridge scheduled event and makes the Lambda function the target\.
+ Grants permission to let EventBridge invoke the Lambda function\.
+ Prints the latest data from CloudWatch Logs to show the result of the scheduled invocations\.
+ Cleans up all resources created during the demo\.
 This example is best viewed on GitHub\. For complete source code and instructions on how to set up and run, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/lambda#readme)\.   

**Services used in this example**
+ CloudWatch Logs
+ EventBridge
+ Lambda

------

For a complete list of AWS SDK developer guides and code examples, including help getting started and information about previous versions, see [Using CloudWatch Logs with an AWS SDK](sdk-general-information-section.md)\.