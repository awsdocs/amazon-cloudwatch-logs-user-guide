# Tutorial: Run a Query That Produces a Visualization Grouped by Log Fields<a name="CWL_AnalyzeLogData_VisualizationFieldQuery"></a>

When you run a query that uses the `stats` function to group the returned results by the values of one or more fields in the log entries, you can view the results as a bar chart, pie chart, line graph or stacked area graph\. This helps you more efficiently visualize trends in your logs\.

**To run a query for visualization**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Insights**\.

1. Select one or more log groups to query\.

1. In the query editor, delete the current contents, enter the following `stats` function, and then choose **Run query**\.

   ```
   stats count(*) by @logStream 
       | limit 100
   ```

   The results show the number of log events in the log group for each log stream\. The results are limited to only 100 rows\.

1. Choose the **Visualization** tab\.

1. Select the arrow next to **Line**, and then choose **Bar**\.

   The bar chart appears, showing a bar for each log stream in the log group\.