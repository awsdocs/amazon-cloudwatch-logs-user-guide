# Listing Metric Filters<a name="ListingMetricFilters"></a>

You can list all metric filters in a log group\.

**To list metric filters using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Log groups**\.

1. In the contents pane, in the list of log groups, in the **Metric Filters** column, choose the number of filters\.

   The **Log Groups > Filters for** screen lists all metric filters associated with the log group\.

**To list metric filters using the AWS CLI**  
At a command prompt, run the following command:

```
aws logs describe-metric-filters --log-group-name MyApp/access.log
```

The following is example output:

```
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