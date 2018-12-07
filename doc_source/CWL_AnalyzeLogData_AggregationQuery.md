# Tutorial: Run a Query with an Aggregation Function<a name="CWL_AnalyzeLogData_AggregationQuery"></a>

In this tutorial, you run a query that returns the results of executing aggregate functions on log records\.

**To run an aggregation query**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Insights**\.

1. Choose a log group\.

1. In the query editor, delete the query that is currently shown, then type the following and choose **Run query**\. Replace *fieldname* with the name of a field that appears in the **Discovered fields** area on the right of the screen\.

   ```
   stats count(*) by fieldname
   ```

   The results show the number of log events in the log group that were received by CloudWatch Logs that contain each different value for the field name you chose\.