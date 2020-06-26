# Tutorial: Run a Query with an Aggregation Function<a name="CWL_AnalyzeLogData_AggregationQuery"></a>

In this tutorial, you run a query that returns the results of executing aggregate functions on log records\.

**To run an aggregation query**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Insights**\.

1. Select one or more log groups above the query editor\. To help find your log groups, enter text in the search bar and CloudWatch Logs displays matching log groups in the search bar\.

1. In the query editor, delete the query that is currently shown, enter the following, and then choose **Run**\. Replace *fieldname* with the name of a field that appears in the **Fields** area on the right of the page\.

   ```
   stats count(*) by fieldname
   ```

   The results show the number of log events in the log group that were received by CloudWatch Logs that contain each different value for the field name that you chose\.