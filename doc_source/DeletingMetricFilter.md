# Deleting a metric filter<a name="DeletingMetricFilter"></a>

A policy is identified by its name and the log group it belongs to\.

**To delete a metric filter using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Log groups**\.

1. In the contents pane, in the **Metric Filter** column, choose the number of metric filters for the log group\.

1. Under **Metric Filters** screen, select the check box to the right of the name of the filter that you want to delete\. Then choose **Delete**\.

1. When prompted for confirmation, choose **Delete**\.

**To delete a metric filter using the AWS CLI**  
At a command prompt, run the following command:

```
aws logs delete-metric-filter --log-group-name MyApp/access.log \
 --filter-name MyFilterName
```