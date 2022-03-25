# Create a metric filter for a log group<a name="CreateMetricFilterProcedure"></a>

To create a metric filter for a log group, follow these steps\.

**To create a metric filter using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Log groups**\.

1. Choose the name of the log group\.

1. Choose `Actions`, **Create metric filter**\.

1. For **Filter pattern**, enter the filter pattern to use\. For more information, see [Filter and pattern syntax](FilterAndPatternSyntax.md)\.

1. \(Optional\) To test your filter pattern, under **Test Pattern**, enter one or more log events to use to test the pattern\. Each log event must be within one line, because line breaks are used to separate log events in the **Log event messages** box\.

1. Choose **Next**, and then enter a name for the filter\.

1. Under **Metric details**, for **Metric namespace**, enter a name for the CloudWatch namespace where the metric will be published\. If this namespace doesn't already exist, be sure that **Create new** is selected\.

1. For **Metric name**, enter a name for the new metric\.

1. For **Metric value**, if your metric filter is counting occurrences of the keywords in the filter, enter 1\. This increments the metric by 1 for each log event that includes one of the keywords\.

   Alternatively, enter a token such as **$size**\. This increments the metric by the value of the number in the `size` field for every log event that contains a `size` field\.

1. \(Optional\) For **Unit**, select a unit to assign to the metric\. If you do not specify a unit, the unit is set as `None`\.

1. \(Optional\) Enter the names and tokens for as many as three dimensions for the metric\.

   If you assign dimensions to a metric created by a metric filter, you can't assign a default value for that metric\. 

1. Choose **Create metric filter**\.