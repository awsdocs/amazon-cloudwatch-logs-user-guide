# Example: Extract Fields from an Apache Log<a name="ExtractBytesExample"></a>

Sometimes, instead of counting, it is helpful to use values within individual log events for metric values\. This example shows how you can create an extraction rule to create a metric that measures the bytes transferred by an Apache webserver\.

This extraction rule matches the seven fields of the log event\. The metric value is the value of the seventh matched token\. You can see the reference to the token as "$7" in the `metricValue` field of the extraction rule\.

**To create a metric filter using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**\.

1. In the contents pane, select a log group, and then choose **Create Metric Filter**\.

1. On the **Define Logs Metric Filter** screen, for **Filter Pattern**, type **\[ip, id, user, timestamp, request, status\_code, size\]**\.

1. To test your filter pattern, for **Select Log Data to Test**, select the log group to test the metric filter against, and then choose **Test Pattern**\.

1. Under **Results**, CloudWatch Logs displays a message showing how many occurrences of the filter pattern were found in the log file\.

   To see detailed results, click **Show test results**\.

1. Choose **Assign Metric**, and then on the **Create Metric Filter and Assign a Metric** screen, for **Filter Name**, type **size**\.

1. Under **Metric Details**, for **Metric Namespace**, type **MyNameSpace**\.

1. For **Metric Name**, type **BytesTransferred**

1. Choose **Show advanced metric settings** and for **Metric Value** type **$size**\.

1. For **Default Value** type 0, and then choose **Create Filter**\. 

**To create a metric filter using the AWS CLI**  
At a command prompt, run the following command

```
aws logs put-metric-filter \
--log-group-name MyApp/access.log \
--filter-name BytesTransferred \
--filter-pattern '[ip, id, user, timestamp, request, status_code, size]' \
--metric-transformations \
metricName=BytesTransferred,metricNamespace=MyNamespace,metricValue=$size,defaultValue=0
```

You can use the following data in put\-log\-event calls to test this rule\. This generates two different metrics if you did not remove monitoring rule in the previous example\.

```
127.0.0.1 - - [24/Sep/2013:11:49:52 -0700] "GET /index.html HTTP/1.1" 404 287
127.0.0.1 - - [24/Sep/2013:11:49:52 -0700] "GET /index.html HTTP/1.1" 404 287
127.0.0.1 - - [24/Sep/2013:11:50:51 -0700] "GET /~test/ HTTP/1.1" 200 3
127.0.0.1 - - [24/Sep/2013:11:50:51 -0700] "GET /favicon.ico HTTP/1.1" 404 308
127.0.0.1 - - [24/Sep/2013:11:50:51 -0700] "GET /favicon.ico HTTP/1.1" 404 308
127.0.0.1 - - [24/Sep/2013:11:51:34 -0700] "GET /~test/index.html HTTP/1.1" 200 3
```
