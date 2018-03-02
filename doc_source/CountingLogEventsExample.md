# Example: Count Log Events<a name="CountingLogEventsExample"></a>

The simplest type of log event monitoring is to count the number of log events that occur\. You might want to do this to keep a count of all events, to create a "heartbeat" style monitor or just to practice creating metric filters\.

In the following CLI example, a metric filter called MyAppAccessCount is applied to the log group MyApp/access\.log to create the metric EventCount in the CloudWatch namespace MyNamespace\. The filter is configured to match any log event content and to increment the metric by "1"\.

**To create a metric filter using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**\.

1. In the contents pane, select a log group, and then choose **Create Metric Filter**\.

1. On the **Define Logs Metric Filter** screen, leave **Filter Pattern** blank\.

1. Choose **Assign Metric**, and then on the **Create Metric Filter and Assign a Metric** screen, for **Filter Name**, type **EventCount**\.

1. Under **Metric Details**, for **Metric Namespace**, type **MyNameSpace**\.

1. For **Metric Name**, type **MyAppEventCount**\.

1. Choose **Show advanced metric settings** and confirm that **Metric Value** is 1\. This specifies that the count is incremented by 1 for every log event\.

1. For **Default Value** type 0, and then choose **Create Filter**\. Specifying a default value ensures that data is reported even during periods when no log events occur, preventing spotty metrics where data sometimes does not exist\.

**To create a metric filter using the AWS CLI**  
At a command prompt, run the following command:

```
aws logs put-metric-filter \
  --log-group-name MyApp/access.log \
  --filter-name EventCount \
  --filter-pattern "" \
  --metric-transformations \
  metricName=MyAppEventCount,metricNamespace=MyNamespace,metricValue=1,defaultValue=0
```

You can test this new policy by posting any event data\. You should see data points published to the metric MyAppAccessEventCount\.

**To post event data using the AWS CLI**  
At a command prompt, run the following command:

```
aws logs put-log-events \
  --log-group-name MyApp/access.log --log-stream-name TestStream1 \
  --log-events \
    timestamp=1394793518000,message="Test event 1" \
    timestamp=1394793518000,message="Test event 2" \
    timestamp=1394793528000,message="This message also contains an Error"
```