# Example: Count HTTP 4xx Codes<a name="FindCountMetric"></a>

As in the previous example, you might want to monitor your web service access logs and monitor the HTTP response code levels\. For example, you might want to monitor all of the HTTP 400\-level errors\. However, you might not want to specify a new metric filter for every return code\.

The following example demonstrates how to create a metric that includes all 400\-level HTTP code responses from an access log using the Apache access log format from the [Example: Count HTTP 404 Codes](Counting404Responses.md) example\.

**To create a metric filter using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Log groups**\.

1. Choose the name of the log group for the Apache server\.

1. Choose `Actions`, **Create metric filter**\.

1. For **Filter name**, enter **HTTP4xxErrors**\.

1. For **Filter pattern**, enter **\[ip, id, user, timestamp, request, status\_code=4\*, size\]**\.

1. To test your filter pattern, choose **Test Pattern**\.

1. Choose **Next**, and then for **Filter name**, type **HTTP4xxErrors**\.

1. Under **Metric details**, for **Metric namespace**, enter **MyNameSpace**\.

1. For **Metric name**, enter **HTTP4xxErrors**\.

1. For **Metric value**, enter 1\. This specifies that the count is incremented by 1 for every log containing a 4xx error\.

1. For **Default value** enter 0, and then choose **Next**\. 

1. Choose **Create metric filter**\.

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