# Tutorial: Run and modify a sample query<a name="CWL_AnalyzeLogData_RunSampleQuery"></a>

The following tutorial helps you get started with CloudWatch Logs Insights\. You run a sample query, and then see how to modify and rerun it\.

To run a query, you must already have logs stored in CloudWatch Logs\. If you are already using CloudWatch Logs and have log groups and log streams set up, you are ready to start\. You may also already have logs if you use services such as AWS CloudTrail, Amazon Route 53, or Amazon VPC and you have set up logs from those services to go to CloudWatch Logs\. For more information about sending logs to CloudWatch Logs, see [Getting started with CloudWatch Logs](CWL_GettingStarted.md)\.

Queries in CloudWatch Logs Insights return either a set of fields from log events or the result of a mathematical aggregation or other operation performed on log events\. This tutorial demonstrates a query that returns a list of log events\.

## Run a sample query<a name="CWL_AnalyzeLogData_RunQuerySample"></a>

**To run a CloudWatch Logs Insights sample query**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**, and then choose **Logs Insights**\.

   On the **Logs Insights** page, the query editor contains a default query that returns the 20 most recent log events\.

1. In the **Select log group\(s\)** drop down, choose one or more log groups to query\.

   You can type the name of log groups you want to query in the search bar\.

   When you select a log group, CloudWatch Logs Insights automatically detects data fields in the group\. To see discovered fields, select the **Fields** menu near the top right of the page\.

1. \(Optional\) Use the time interval selector to select a time period that you want to query\.

   You can choose between 5 and 30\-minute intervals; 1, 3, and 12\-hour intervals; or a custom time frame\.

1. Choose **Run** to view the results\.

   For this tutorial, the results include the 20 most recently added log events\.

   CloudWatch Logs displays a bar graph of log events in the log group over time\. The bar graph shows not only the events in the table, but also the distribution of events in the log group that match the query and timeframe\.

1. To see all fields for a returned log event, choose the triangular dropdown icon left of the numbered event\.

## Modify the sample query<a name="CWL_AnalyzeLogData_ModifySampleQuery"></a>

In this tutorial, you modify the sample query to show the 50 most recent log events\.

If you haven't already run the previous tutorial, do that now\. This tutorial starts where that previous tutorial ends\.

**Note**  
Some sample queries provided with CloudWatch Logs Insights use `head` or `tail` commands instead of `limit`\. These commands are being deprecated and have been replaced with `limit`\. Use `limit` instead of `head` or `tail` in all queries that you write\.

**To modify the CloudWatch Logs Insights sample query**

1. In the query editor, change **20** to **50**, and then choose **Run**\.

   The results of the new query appear\. Assuming there are enough data in the log group in the default time range, there are now 50 log events listed\.

1. \(Optional\) You can save queries that you have created\. To save this query, choose **Save**\. For more information, see [Saving and re\-running CloudWatch Logs Insights queries](CWL_Insights-Saving-Queries.md)\.

## Add a filter command to the sample query<a name="CWL_AnalyzeLogData_FilterQuery"></a>

This tutorial shows how to make a more powerful change to the query in the query editor\. In this tutorial, you filter the results of the previous query based on a field in the retrieved log events\.

If you haven't already run the previous tutorials, do that now\. This tutorial starts where that previous tutorial ends\.

**To add a filter command to the previous query**

1. Decide on a field to filter\. To see the most common fields that CloudWatch Logs has detected in the log events contained in the selected log groups in the past 15 minutes, and the percentage of those log events in which each field appears, select **Fields** on the right side of the page\.

   To see the fields contained in a particular log event, choose the icon to the left of that row\.

   The **awsRegion** field might appear in your log event, depending on which events are in your logs\. For the rest of this tutorial, we use **awsRegion** as the filter field, but you can use a different field if that field isn't available\.

1. In the query editor box, place your cursor after **50** and press Enter\.

1. On the new line, first enter \| \(the pipe character\) and a space\. Commands in a CloudWatch Logs Insights query must be separated by the pipe character\.

1. Enter **filter awsRegion="us\-east\-1"**\.

1. Choose **Run**\.

   The query runs again, and now displays the 50 most recent results that match the new filter\.

   If you filtered on a different field and got an error result, you might need to escape the field name\. If the field name includes non\-alphanumeric characters, you must put backtick characters \(`\) before and after the field name \(for example, **`error\-code`="102"**\)\.

   You must use the backtick characters for field names that contain non\-alphanumeric characters, but not for values\. Values are always contained in quotation marks \("\)\.

CloudWatch Logs Insights includes powerful query abilities, including several commands and support for regular expressions, mathematical, and statistical operations\. For more information, see [CloudWatch Logs Insights query syntax](CWL_QuerySyntax.md)\.
