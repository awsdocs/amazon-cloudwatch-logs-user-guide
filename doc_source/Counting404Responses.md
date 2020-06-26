# Example: Count HTTP 404 Codes<a name="Counting404Responses"></a>

Using CloudWatch Logs, you can monitor how many times your Apache servers return a HTTP 404 response, which is the response code for page not found\. You might want to monitor this to understand how often your site visitors do not find the resource they are looking for\. Assume that your log records are structured to include the following information for each log event \(site visit\):
+ Requestor IP Address
+ RFC 1413 Identity
+ Username
+ Timestamp
+ Request method with requested resource and protocol
+ HTTP response code to request
+ Bytes transferred in request

An example of this might look like the following:

```
127.0.0.1 - frank [10/Oct/2000:13:55:36 -0700] "GET /apache_pb.gif HTTP/1.0" 404 2326
```

You could specify a rule which attempts to match events of that structure for HTTP 404 errors, as shown in the following example:

**To create a metric filter using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Log groups**\.

1. Choose `Actions`, **Create metric filter**\.

1. For **Filter Pattern**, type **\[IP, UserInfo, User, Timestamp, RequestInfo, StatusCode=404, Bytes\]**\.

1. To test your filter pattern, choose **Test Pattern**\.

1. Choose **Next**, and then for **Filter Name**, type **HTTP404Errors**\.

1. Under **Metric Details**, for **Metric Namespace**, enter **MyNameSpace**\.

1. For **Metric Name**, enter **ApacheNotFoundErrorCount**\.

1. Confirm that **Metric Value** is 1\. This specifies that the count is incremented by 1 for every 404 Error event\.

1. For **Default Value** enter 0, and then choose **Next**\.

1. Choose **Create metric filter**\.

**To create a metric filter using the AWS CLI**  
At a command prompt, run the following command:

```
aws logs put-metric-filter \
  --log-group-name MyApp/access.log \
  --filter-name HTTP404Errors \
  --filter-pattern '[ip, id, user, timestamp, request, status_code=404, size]' \
  --metric-transformations \
      metricName=ApacheNotFoundErrorCount,metricNamespace=MyNamespace,metricValue=1
```

In this example, literal characters such as the left and right square brackets, double quotes and character string 404 were used\. The pattern needs to match with the entire log event message for the log event to be considered for monitoring\.

You can verify the creation of the metric filter by using the describe\-metric\-filters command\. You should see output that looks like this:

```
aws logs describe-metric-filters --log-group-name MyApp/access.log

{
    "metricFilters": [
        {
            "filterName": "HTTP404Errors", 
            "metricTransformations": [
                {
                    "metricValue": "1", 
                    "metricNamespace": "MyNamespace", 
                    "metricName": "ApacheNotFoundErrorCount"
                }
            ], 
            "creationTime": 1399277571078, 
            "filterPattern": "[ip, id, user, timestamp, request, status_code=404, size]"
        }
    ]
}
```

Now you can post a few events manually:

```
aws logs put-log-events \
--log-group-name MyApp/access.log --log-stream-name hostname \
--log-events \
timestamp=1394793518000,message="127.0.0.1 - bob [10/Oct/2000:13:55:36 -0700] \"GET /apache_pb.gif HTTP/1.0\" 404 2326" \
timestamp=1394793528000,message="127.0.0.1 - bob [10/Oct/2000:13:55:36 -0700] \"GET /apache_pb2.gif HTTP/1.0\" 200 2326"
```

Soon after putting these sample log events, you can retrieve the metric named in the CloudWatch console as ApacheNotFoundErrorCount\.