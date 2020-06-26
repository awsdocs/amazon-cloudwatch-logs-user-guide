# Analyzing Log Data with CloudWatch Logs Insights<a name="AnalyzingLogData"></a>

CloudWatch Logs Insights enables you to interactively search and analyze your log data in Amazon CloudWatch Logs\. You can perform queries to help you more efficiently and effectively respond to operational issues\. If an issue occurs, you can use CloudWatch Logs Insights to identify potential causes and validate deployed fixes\.

CloudWatch Logs Insights includes a purpose\-built query language with a few simple but powerful commands\. CloudWatch Logs Insights provides sample queries, command descriptions, query autocompletion, and log field discovery to help you get started\. Sample queries are included for several types of AWS service logs\.

CloudWatch Logs Insights automatically discovers fields in logs from AWS services such as Amazon Route 53, AWS Lambda, AWS CloudTrail, and Amazon VPC, and any application or custom log that emits log events as JSON\. 

You can use CloudWatch Logs Insights to search log data that was sent to CloudWatch Logs on November 5, 2018 or later\. 

A single request can query up to 20 log groups\. Queries time out after 15 minutes, if they have not completed\. Query results are available for 7 days\.

You can save queries that you have created\. This can help you run complex queries when you need, without having to re\-create them each time that you want to run them\.

**Important**  
If your network security team doesn't allow the use of web sockets, you can't currently access the CloudWatch Logs Insights portion of the CloudWatch console\. You can use the CloudWatch Logs Insights query capabilities using APIs\. For more information, see [StartQuery](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_StartQuery.html) in the *Amazon CloudWatch Logs API Reference*\.

**Topics**
+ [Supported Logs and Discovered Fields](CWL_AnalyzeLogData-discoverable-fields.md)
+ [Tutorial: Run and Modify a Sample Query](CWL_AnalyzeLogData_RunSampleQuery.md)
+ [Tutorial: Run a Query with an Aggregation Function](CWL_AnalyzeLogData_AggregationQuery.md)
+ [Tutorial: Run a Query That Produces a Visualization Grouped by Log Fields](CWL_AnalyzeLogData_VisualizationFieldQuery.md)
+ [Tutorial: Run a Query That Produces a Time Series Visualization](CWL_AnalyzeLogData_VisualizationQuery.md)
+ [CloudWatch Logs Insights Query Syntax](CWL_QuerySyntax.md)
+ [Visualizing Log Data in Graphs](CWL_Insights-Visualizing-Log-Data.md)
+ [Saving and Re\-running CloudWatch Logs Insights Queries](CWL_Insights-Saving-Queries.md)
+ [Sample Queries](CWL_QuerySyntax-examples.md)
+ [Add Query to Dashboard or Export Query Results](CWL_ExportQueryResults.md)
+ [View Running Queries or Query History](CloudWatchLogs-Insights-Query-History.md)