# Deleting a Metric Filter<a name="DeletingMetricFilter"></a>

A policy is identified by its name and the log group it belongs to\.

**To delete a metric filter using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Log groups**\.

1. In the contents pane, in the **Metric Filter** column, choose the metric filter\.

1. On the **Logs Metric Filters** screen, in the metric filter, choose **Delete Filter**\.

1. When prompted for confirmation, choose **Yes, Delete**\.

**To delete a metric filter using the AWS CLI**  
At a command prompt, run the following command:

```
aws logs delete-metric-filter --log-group-name MyApp/access.log \
 --filter-name MyFilterName
```