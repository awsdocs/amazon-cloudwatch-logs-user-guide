# Describe CloudWatch Logs log groups using an AWS SDK<a name="example_cloudwatch-logs_DescribeLogGroups_section"></a>

The following code example shows how to describe CloudWatch Logs log groups\.

------
#### [ \.NET ]

**AWS SDK for \.NET**  
  

```
        public static async Task Main()
        {
            // Creates a CloudWatch Logs client using the default
            // user. If you need to work with resources in another
            // AWS Region than the one defined for the default user,
            // pass the AWS Region as a parameter to the client constructor.
            var client = new AmazonCloudWatchLogsClient();

            var request = new DescribeLogGroupsRequest
            {
                Limit = 5,
            };

            var response = await client.DescribeLogGroupsAsync(request);

            if (response.LogGroups.Count > 0)
            {
                do
                {
                    response.LogGroups.ForEach(lg =>
                    {
                        Console.WriteLine($"{lg.LogGroupName} is associated with the key: {lg.KmsKeyId}.");
                        Console.WriteLine($"Created on: {lg.CreationTime.Date.Date}");
                        Console.WriteLine($"Date for this group will be stored for: {lg.RetentionInDays} days.\n");
                    });
                }
                while (response.NextToken is not null);
            }
        }
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/dotnetv3/CloudWatchLogs#code-examples)\. 
+  For API details, see [DescribeLogGroups](https://docs.aws.amazon.com/goto/DotNetSDKV3/logs-2014-03-28/DescribeLogGroups) in *AWS SDK for \.NET API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, including help getting started and information about previous versions, see [Using CloudWatch Logs with an AWS SDK](sdk-general-information-section.md)\.