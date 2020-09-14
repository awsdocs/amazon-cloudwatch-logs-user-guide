# Exporting Log Data to Amazon S3<a name="S3Export"></a>

You can export log data from your log groups to an Amazon S3 bucket and use this data in custom processing and analysis, or to load onto other systems\.

Exporting log data to Amazon S3 buckets that are encrypted by AWS KMS is not supported\.

To begin the export process, you must create an S3 bucket to store the exported log data\. You can store the exported files in your Amazon S3 bucket and define Amazon S3 lifecycle rules to archive or delete exported files automatically\.

Exporting to S3 buckets that are encrypted with AES\-256 is supported\. Exporting to S3 buckets encrypted with SSE\-KMS is not supported\. For more information, see [ How Do I Enable Default Encryption for an S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/default-bucket-encryption.html)\.

You can export logs from multiple log groups or multiple time ranges to the same S3 bucket\. To separate log data for each export task, you can specify a prefix that will be used as the Amazon S3 key prefix for all exported objects\.

Log data can take up to 12 hours to become available for export\. For near real\-time analysis of log data, see [Analyzing Log Data with CloudWatch Logs Insights](AnalyzingLogData.md) or [Real\-time Processing of Log Data with Subscriptions](Subscriptions.md) instead\.

**Note**  
Starting on February 15, 2019, the export to Amazon S3 feature requires callers to have `s3:PutObject` access to the destination bucket\.

**Topics**
+ [Concepts](#S3concepts)
+ [Export Log Data to Amazon S3 Using the Console](S3ExportTasksConsole.md)
+ [Export Log Data to Amazon S3 Using the AWS CLI](S3ExportTasks.md)

## Concepts<a name="S3concepts"></a>

Before you begin, become familiar with the following export concepts:

**log group name**  
The name of the log group associated with an export task\. The log data in this log group will be exported to the specified Amazon S3 bucket\.

**from \(timestamp\)**  
A required timestamp expressed as the number of milliseconds since Jan 1, 1970 00:00:00 UTC\. All log events in the log group that were ingested after this time will be exported\.

**to \(timestamp\)**  
A required timestamp expressed as the number of milliseconds since Jan 1, 1970 00:00:00 UTC\. All log events in the log group that were ingested before this time will be exported\.

**destination bucket**  
The name of the Amazon S3 bucket associated with an export task\. This bucket is used to export the log data from the specified log group\.

**destination prefix**  
An optional attribute that is used as the S3 key prefix for all exported objects\. This helps create a folder\-like organization in your bucket\.