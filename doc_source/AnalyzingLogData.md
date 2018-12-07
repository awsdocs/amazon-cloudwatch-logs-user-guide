# Analyze Log Data with CloudWatch Logs Insights<a name="AnalyzingLogData"></a>

CloudWatch Logs Insights enables you to interactively search and analyze your log data in Amazon CloudWatch Logs\. You can perform queries to help you quickly and effectively respond to operational issues\. If an issue occurs, you can use CloudWatch Logs Insights to identify potential causes and validate deployed fixes\.

CloudWatch Logs Insights includes a purpose\-built query language with a few simple but powerful commands\. CloudWatch Logs Insights provides sample queries, command descriptions, query autocompletion, and log field discovery to help you get started quickly\. Sample queries are included for several types of AWS service logs\.

CloudWatch Logs Insights automatically discovers fields in logs from AWS services such as Amazon Route 53, AWS Lambda, AWS CloudTrail, and Amazon VPC, and any application or custom log that emits log events as JSON\. 

You can use CloudWatch Logs Insights to search log data that was sent to CloudWatch Logs on November 5, 2018 or later\. 

**Important**  
If your network security team does not allow the use of web sockets, you cannot currently access the CloudWatch Logs Insights console\. You can use the CloudWatch Logs Insights query capabilities using APIs\. For more information, see [StartQuery](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_StartQuery.html) in the *Amazon CloudWatch Logs API Reference*\.

**Topics**
+ [Supported Logs and Discovered Fields](CWL_AnalyzeLogData-discoverable-fields.md)
+ [Tutorial: Run and Modify a Sample Query](CWL_AnalyzeLogData_RunSampleQuery.md)
+ [Tutorial: Run a Query with an Aggregation Function](CWL_AnalyzeLogData_AggregationQuery.md)
+ [Tutorial: Run a Query That Produces a Visualization](CWL_AnalyzeLogData_VisualizationQuery.md)
+ [CloudWatch Logs Insights Query Syntax](CWL_QuerySyntax.md)
+ [Visualizing Time Series Data](CWL_Insights-Visualizing-TimeSeries.md)
+ [Sample Queries](CWL_QuerySyntax-examples.md)
+ [Add Query to Dashboard or Export Query Results](CWL_ExportQueryResults.md)
+ [View Running Queries or Query History](CloudWatchLogs-Insights-Query-History.md)