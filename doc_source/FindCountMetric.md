# Example: Count HTTP 4xx Codes<a name="FindCountMetric"></a>

As in the previous example, you might want to monitor your web service access logs and monitor the HTTP response code levels\. For example, you might want to monitor all of the HTTP 400\-level errors\. However, you might not want to specify a new metric filter for every return code\.

The following example demonstrates how to create a metric that includes all 400\-level HTTP code responses from an access log using the Apache access log format from the [Example: Count HTTP 404 Codes](Counting404Responses.md) example\.

**To create a metric filter using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**\.

1. In the contents pane, select a log group, and then choose **Create Metric Filter**\.

1. On the **Define Logs Metric Filter** screen, for **Filter Pattern**, type **\[ip, id, user, timestamp, request, status\_code=4\*, size\]**\.

1. To test your filter pattern, for **Select Log Data to Test**, select the log group to test the metric filter against, and then choose **Test Pattern**\.

1. Under **Results**, CloudWatch Logs displays a message showing how many occurrences of the filter pattern were found in the log file\.

   To see detailed results, click **Show test results**\.

1. Choose **Assign Metric**, and then on the **Create Metric Filter and Assign a Metric** screen, for **Filter Name**, type **HTTP4xxErrors**\.

1. Under **Metric Details**, for **Metric Namespace**, type **MyNameSpace**\.

1. For **Metric Name**, type **HTTP4xxErrors**\.

1. Choose **Show advanced metric settings** and confirm that **Metric Value** is 1\. This specifies that the count is incremented by 1 for every log containing a 4xx error\.

1. For **Default Value** type 0, and then choose **Create Filter**\. 

**To create a metric filter using the AWS CLI**  
At a command prompt, run the following command:

```
aws logs put-metric-filter \
  --log-group-name MyApp/access.log \
  --filter-name HTTP4xxErrors \
  --filter-pattern '[ip, id, user, timestamp, request, status_code=4*, size]' \
  --metric-transformations \
  metricName=HTTP4xxErrors,metricNamespace=MyNamespace,metricValue=1,defaultValue=0
```

You can use the following data in put\-event calls to test this rule\. If you did not remove the monitoring rule in the previous example, you will generate two different metrics\.

```
127.0.0.1 - - [24/Sep/2013:11:49:52 -0700] "GET /index.html HTTP/1.1" 404 287
127.0.0.1 - - [24/Sep/2013:11:49:52 -0700] "GET /index.html HTTP/1.1" 404 287
127.0.0.1 - - [24/Sep/2013:11:50:51 -0700] "GET /~test/ HTTP/1.1" 200 3
127.0.0.1 - - [24/Sep/2013:11:50:51 -0700] "GET /favicon.ico HTTP/1.1" 404 308
127.0.0.1 - - [24/Sep/2013:11:50:51 -0700] "GET /favicon.ico HTTP/1.1" 404 308
127.0.0.1 - - [24/Sep/2013:11:51:34 -0700] "GET /~test/index.html HTTP/1.1" 200 3
```