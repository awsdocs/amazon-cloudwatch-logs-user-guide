# Create a CloudWatch Logs log group using an AWS SDK<a name="example_cloudwatch-logs_CreateLogGroup_section"></a>

The following code example shows how to create a new CloudWatch Logs log group\.

------
#### [ \.NET ]

**AWS SDK for \.NET**  
  

```
        public static async Task Main()
        {
            // This client object will be associated with the same AWS Region
            // as the default user on this system. If you need to use a
            // different AWS Region, pass it as a parameter to the client
            // constructor.
            var client = new AmazonCloudWatchLogsClient();

            string logGroupName = "cloudwatchlogs-example-loggroup";

            var request = new CreateLogGroupRequest
            {
                LogGroupName = logGroupName,
            };

            var response = await client.CreateLogGroupAsync(request);

            if (response.HttpStatusCode == System.Net.HttpStatusCode.OK)
            {
                Console.WriteLine($"Successfully create log group with ID: {logGroupName}.");
            }
            else
            {
                Console.WriteLine("Could not create log group.");
            }
        }
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/dotnetv3/CloudWatchLogs#code-examples)\. 
+  For API details, see [CreateLogGroup](https://docs.aws.amazon.com/goto/DotNetSDKV3/logs-2014-03-28/CreateLogGroup) in *AWS SDK for \.NET API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, including help getting started and information about previous versions, see [Using CloudWatch Logs with an AWS SDK](sdk-general-information-section.md)\.