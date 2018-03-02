# Example: Count Occurrences of a Term<a name="CountOccurrencesExample"></a>

Log events frequently include important messages that you want to count, maybe about the success or failure of operations\. For example, an error may occur and be recorded to a log file if a given operation fails\. You may want to monitor these entries to understand the trend of your errors\.

In the example below, a metric filter is created to monitor for the term Error\. The policy has been created and added to the log group MyApp/message\.log\. CloudWatch Logs publishes a data point to the CloudWatch custom metric ErrorCount in the MyApp/message\.log namespace with a value of "1" for every event containing Error\. If no event contains the word Error, then a value of 0 is published\. When graphing this data in the CloudWatch console, be sure to use the sum statistic\.

**To create a metric filter using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**\.

1. In the contents pane, select a log group, and then choose **Create Metric Filter**\.

1. On the **Define Logs Metric Filter** screen, for **Filter Pattern**, type **Error**\.
**Note**  
All entries in **Filter Pattern** are case\-sensitive\.

1. To test your filter pattern, for **Select Log Data to Test**, select the log group to test the metric filter against, and then choose **Test Pattern**\.

1. Under **Results**, CloudWatch Logs displays a message showing how many occurrences of the filter pattern were found in the log file\.

   To see detailed results, choose **Show test results**\.

1. Choose **Assign Metric**, and then on the **Create Metric Filter and Assign a Metric** screen, for **Filter Name**, type **MyAppErrorCount**\.

1. Under **Metric Details**, for **Metric Namespace**, type **MyNameSpace**\.

1. For **Metric Name**, type **ErrorCount**\.

1. Choose **Show advanced metric settings** and confirm that **Metric Value** is 1\. This specifies that the count is incremented by 1 for every log event containing "Error"\.

1. For **Default Value** type 0, and then choose **Create Filter**\. 

**To create a metric filter using the AWS CLI**  
At a command prompt, run the following command:

```
aws logs put-metric-filter \
  --log-group-name MyApp/message.log \
  --filter-name MyAppErrorCount \
  --filter-pattern 'Error' \
  --metric-transformations \
      metricName=EventCount,metricNamespace=MyNamespace,metricValue=1,defaultValue=0
```

You can test this new policy by posting events containing the word "Error" in the message\.

**To post events using the AWS CLI**  
At a command prompt, run the following command\. Note that patterns are case\-sensitive\.

```
aws logs put-log-events \
  --log-group-name MyApp/access.log --log-stream-name TestStream1 \
  --log-events \
    timestamp=1394793518000,message="This message contains an Error" \
    timestamp=1394793528000,message="This message also contains an Error"
```