# Tutorial: Run and Modify a Sample Query<a name="CWL_AnalyzeLogData_RunSampleQuery"></a>

The following tutorial helps you get started with CloudWatch Logs Insights\. You run a sample query, and then see how to modify and re\-run it\.

To run a query, you must already have logs stored in CloudWatch Logs\. If you are already using CloudWatch Logs and have log groups and log streams set up, you are ready to start\. You may also already have logs if you use services such as AWS CloudTrail, Amazon Route 53, or Amazon VPC and you have set up logs from those services to go to CloudWatch Logs\. For more information about sending logs to CloudWatch Logs, see [Getting Started with CloudWatch Logs](CWL_GettingStarted.md)\.

Queries in CloudWatch Logs Insights return either a set of fields from log events, or the result of a mathematical aggregation or other operation performed on log events\. This tutorial demonstrates a query that returns a list of log events\.

## Run a Sample Query<a name="CWL_AnalyzeLogData_RunQuerySample"></a>

Start by running a sample query\.

**To run a CloudWatch Logs Insights sample query**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Insights**\.

   Near the top of the screen is the query editor\. When you first open CloudWatch Logs Insights, this box contains a default query that returns the 20 most recent log events\.

1. Select a log group to query, above the query editor\.

   When you select a log group, CloudWatch Logs Insights automatically detects fields in the data in the log group and displays them in **Discovered fields** in the right pane\. It also displays a bar graph of log events in this log group over time\. This bar graph shows the distribution of events in the log group that matches your query and time range, not just the events displayed in the table

1. Choose **Run query**\.

   The results of the query appear\. In this example, the results are the most recent 20 log events of any type\.

1. To see all fields of one of the returned log events, choose the arrow to the left of that log event\.

## Modify the Sample Query<a name="CWL_AnalyzeLogData_ModifySampleQuery"></a>

In this tutorial, you modify the sample query to show the 50 most recent log events\.

If you have not already run the previous tutorial, do that now\. This tutorial starts where that previous tutorial ends\.

**Note**  
Some sample queries provided with CloudWatch Logs Insights use `head` or `tail` commands instead of `limit`\. These commands are being deprecated and have been replaced with `limit`\. Use `limit` instead of `head` or `tail in all queries you write.`

**To modify the CloudWatch Logs Insights sample query**
+ In the query editor, change **20** to **50**\. Choose **Run query**\.

  The results of the new query appear\. Assuming there is enough data in the log group in the default time range, there are now 50 log events listed\.

## Add a Filter Command to the Sample Query<a name="CWL_AnalyzeLogData_FilterQuery"></a>

This tutorial shows how to make a more powerful change to the query in the query editor\. In this tutorial, you filter the results of the previous query based on a field in the retrieved log events\.

If you have not already run the previous tutorials, do that now\. This tutorial starts where that previous tutorial ends\.

**To add a filter command to the previous query**

1. Decide on a field to filter\. To see the fields contained in a particular log event, choose the arrow to the left of that row\. The **Discovered fields** area shows the most common fields that CloudWatch Logs has detected in the log events received by this log group in the past 15 minutes, and the percentage of those log events in which each field appears\. If you do not see **Discovered fields**, choose the left arrow near the top right of the screen to open the right\-side panel\.

   The **awsRegion** field may appear in your log event, depending on what events are in your logs\. For the rest of this tutorial, you use **awsRegion** as the filter field, but you can use a different field if that field is not available\.

1. In the query editor box, place your cursor after **50** and press Enter\.

1. On the new line, first type \| \(the pipe character\) and a space\. Commands in a CloudWatch Logs Insights query must be separated by the pipe character\.

1. Type **filter awsRegion="us\-east\-1"**\.

1. Choose **Run query**\.

   The query runs again, and now displays the 50 most recent results that match the new filter\.

   If you filtered on a different field and got an error result, you may need to escape the field name\. If the field name includes non\-alphanumeric characters, you must put back\-tick characters \(`\) before and after the field name\. For example, **`error\-code`="102"**\.

   Using the ` characters is necessary for field names containing non\-alphanumeric characters, but not for values\. Values are always contained in double quote marks \("\)\.

CloudWatch Logs Insights includes powerful query abilities, including several commands and support for regular expressions, mathematical, and statistical operations\. For more information, see [CloudWatch Logs Insights Query Syntax](CWL_QuerySyntax.md)\.