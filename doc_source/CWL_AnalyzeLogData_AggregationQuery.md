# Tutorial: Run a query with an aggregation function<a name="CWL_AnalyzeLogData_AggregationQuery"></a>

You can use aggregation functions with the `stats` command and as arguments for other functions\. In this tutorial, you run a query command that counts the number of log events containing a specified field\. The query command returns a total count that's grouped by the specified field's value or values\. For more information about aggregation functions, see [Supported operations and functions](https://docs.aws.amazon.com/en_us/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html#CWL_QuerySyntax-operations-functions) in the *Amazon CloudWatch Logs User Guide*\.

**To run a query with an aggregation function**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**, and then choose **Logs Insights**\.

1. In the **Select log group\(s\)** drop down, choose one or more log groups to query\.

   You can enter the name of log groups that you want to query in the search bar\. When you select a log group, CloudWatch Logs Insights automatically detects the data fields in the log group\. 

1. Delete the default query in the query editor, and enter the following command:

   ```
   stats count(*) by fieldName
   ```

1. Replace *fieldName* with a discovered field from the **Fields** menu\.

   The **Fields** menu is located at the top right of the page and displays all of the discovered fields that CloudWatch Logs Insights detects in your log group\.

1. Choose **Run** to view the query results\.

   The query results show the number of records in your log group that match the query command and the total count that's grouped by the specified field's value or values\.