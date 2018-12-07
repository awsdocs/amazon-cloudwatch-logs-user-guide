# Tutorial: Run a Query That Produces a Visualization<a name="CWL_AnalyzeLogData_VisualizationQuery"></a>

When you run a query that uses the `bin()` function to group the returned results by a time period, you can view the results as a line or stacked area graph\. This helps you quickly visualize trends in log events over time\.

**To run a query for visualization**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Insights**\.

1. Choose a log group\.

1. In the query editor, delete the current contents, then type the following and choose **Run query**\.

   ```
   stats count(*) by bin(30s)
   ```

   The results show the number of log events in the log group that were received by CloudWatch Logs for each 30\-second period\.

1. Choose the **Visualization** tab\.

   The results are shown as a line graph\. To switch to a stacked area chart, choose **Stacked area** at the upper right of the graph\.