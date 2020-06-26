# Example: Count Occurrences of a Term<a name="CountOccurrencesExample"></a>

Log events frequently include important messages that you want to count, maybe about the success or failure of operations\. For example, an error may occur and be recorded to a log file if a given operation fails\. You may want to monitor these entries to understand the trend of your errors\.

In the example below, a metric filter is created to monitor for the term Error\. The policy has been created and added to the log group **MyApp/message\.log**\. CloudWatch Logs publishes a data point to the CloudWatch custom metric ErrorCount in the **MyApp/message\.log** namespace with a value of "1" for every event containing Error\. If no event contains the word Error, then a value of 0 is published\. When graphing this data in the CloudWatch console, be sure to use the sum statistic\.

After you create a metric filter, you can view the metric in the CloudWatch console\. When you are selecting the metric to view, select the metric namespace that matches the log group name\. For more information, see [ Viewing Available Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/viewing_metrics_with_cloudwatch.html)\.

**To create a metric filter using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Log groups**\.

1. Choose the name of the log group\.

1. Choose **Actions**, **Create metric filter**\.

1. For **Filter Pattern**, enter **Error**\.
**Note**  
All entries in **Filter Pattern** are case\-sensitive\.

1. To test your filter pattern, choose **Test Pattern**\.

1. Choose **Next**, and then on the **Assign metric** page, for **Filter Name**, type **MyAppErrorCount**\.

1. Under **Metric Details**, for **Metric Namespace**, type **MyNameSpace**\.

1. For **Metric Name**, type **ErrorCount**\.

1. Confirm that **Metric Value** is 1\. This specifies that the count is incremented by 1 for every log event containing "Error"\.

1. For **Default Value** type 0, and then choose **Next**\. 

1. Choose **Create metric filter**\.

**To create a metric filter using the AWS CLI**  
At a command prompt, run the following command:

```
aws logs put-metric-filter \
  --log-group-name MyApp/message.log \
  --filter-name MyAppErrorCount \
  --filter-pattern 'Error' \
  --metric-transformations \
      metricName=ErrorCount,metricNamespace=MyNamespace,metricValue=1,defaultValue=0
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