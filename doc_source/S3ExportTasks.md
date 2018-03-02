# Export Log Data to Amazon S3 Using the AWS CLI<a name="S3ExportTasks"></a>

In the following example, you'll use an export task to export all data from a CloudWatch Logs log group named "my\-log\-group" to an Amazon S3 bucket named "my\-exported\-logs\." This example assumes that you have already created a log group called “my\-log\-group”\.

## Step 1: Create an Amazon S3 Bucket<a name="CreateS3Bucket"></a>

We recommend that you use a bucket that was created specifically for CloudWatch Logs\. However, if you want to use an existing bucket, you can skip to step 2\.

**Note**  
The Amazon S3 bucket must reside in the same region as the log data to export\. CloudWatch Logs does not support exporting data to Amazon S3 buckets in a different region\.

**To create an Amazon S3 bucket using the AWS CLI**  
At a command prompt, run the following [create\-bucket](http://docs.aws.amazon.com/cli/latest/reference/s3api/create-bucket.html) command, where `LocationConstraint` is the region where you are exporting log data:

```
aws s3api create-bucket --bucket my-exported-logs --create-bucket-configuration LocationConstraint=us-east-2
```

The following is example output:

```
{
    "Location": "/my-exported-logs"
}
```

## Step 2: Set Permissions on an Amazon S3 Bucket<a name="S3Permissions"></a>

By default, all Amazon S3 buckets and objects are private\. Only the resource owner, the AWS account that created the bucket, can access the bucket and any objects it contains\. However, the resource owner can choose to grant access permissions to other resources and users by writing an access policy\.

**To set permissions on an Amazon S3 bucket**

1. Create a file named policy\.json and add the following access policy, changing `Resource` to the name of your S3 bucket and `Principal` to the endpoint of the region where you are exporting log data\. Use a text editor to create this policy file\. Do not use the IAM console\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Action": "s3:GetBucketAcl",
               "Effect": "Allow",
               "Resource": "arn:aws:s3:::my-exported-logs",
               "Principal": { "Service": "logs.us-west-2.amazonaws.com" }
           },
           {
               "Action": "s3:PutObject" ,
               "Effect": "Allow",
               "Resource": "arn:aws:s3:::my-exported-logs/*",
               "Condition": { "StringEquals": { "s3:x-amz-acl": "bucket-owner-full-control" } },
               "Principal": { "Service": "logs.us-west-2.amazonaws.com" }
           }
       ]
   }
   ```

1. Set the policy that you just added as the access policy on your bucket using the [put\-bucket\-policy](http://docs.aws.amazon.com/cli/latest/reference/s3api/put-bucket-policy.html) command\. This policy enables CloudWatch Logs to export log data to your Amazon S3 bucket\. The bucket owner will have full permissions on all of the exported objects\.

   ```
   aws s3api put-bucket-policy --bucket my-exported-logs --policy file://policy.json
   ```
**Warning**  
If the existing bucket already has one or more policies attached to it, add the statements for CloudWatch Logs access to that policy or policies\. We recommend that you evaluate the resulting set of permissions to be sure that they are appropriate for the users who will access the bucket\.

## Step 3: Create an Export Task<a name="CreateExportTask"></a>

After you create the export task for exporting logs from a log group, the export task might take anywhere from a few seconds to a few hours, depending on the size of the data to export\.

**To create an export task using the AWS CLI**  
At a command prompt, use the following [create\-export\-task](http://docs.aws.amazon.com/cli/latest/reference/logs/create-export-task.html) command to create the export task:

```
aws logs create-export-task --task-name "my-log-group-09-10-2015" --log-group-name "my-log-group" --from 1441490400000 --to 1441494000000 --destination "my-exported-logs" --destination-prefix "export-task-output"
```

The following is example output:

```
{
    "task-id": "cda45419-90ea-4db5-9833-aade86253e66"
}
```

## Step 4: Describe Export Tasks<a name="DescribeExportTasks"></a>

After you create an export task, you can get the current status of the task\.

**To describe export tasks using the AWS CLI**  
At a command prompt, use the following [describe\-export\-tasks](http://docs.aws.amazon.com/cli/latest/reference/logs/describe-export-tasks.html) command:

```
aws logs describe-export-tasks --task-id "cda45419-90ea-4db5-9833-aade86253e66"
```

The following is example output:

```
{
   "ExportTasks": [
   {
      "Destination": "my-exported-logs",
      "DestinationPrefix": "export-task-output",
      "ExecutionInfo": {
         "CreationTime": 1441495400000
      },
      "From": 1441490400000,
      "LogGroupName": "my-log-group",
      "Status": {
         "Code": "RUNNING",
         "Message": "Started Successfully"
      },
      "TaskId": "cda45419-90ea-4db5-9833-aade86253e66",
      "TaskName": "my-log-group-09-10-2015",
      "To": 1441494000000
   }]
}
```

You can use the `describe-export-tasks` command in three different ways:

+ Without any filters: Lists all of your export tasks, in reverse order of creation\.

+ Filter on task ID: Lists the export task, if one exists, with the specified ID\.

+ Filter on task status: Lists the export tasks with the specified status\.

For example, use the following command to filter on the FAILED status:

```
aws logs describe-export-tasks --status-code "FAILED"
```

The following is example output:

```
{
   "ExportTasks": [
   {
      "Destination": "my-exported-logs",
      "DestinationPrefix": "export-task-output",
      "ExecutionInfo": {
         "CompletionTime": 1441498600000
         "CreationTime": 1441495400000
      },
      "From": 1441490400000,
      "LogGroupName": "my-log-group",
      "Status": {
         "Code": "FAILED",
         "Message": "FAILED"
      },
      "TaskId": "cda45419-90ea-4db5-9833-aade86253e66",
      "TaskName": "my-log-group-09-10-2015",
      "To": 1441494000000
   }]
}
```

## Step 5: Cancel an Export Task<a name="CancelExportTask"></a>

You can cancel an export task if it is in either the PENDING or the RUNNING state\.

**To cancel an export task using the AWS CLI**  
At a command prompt, use the following [cancel\-export\-task](http://docs.aws.amazon.com/cli/latest/reference/logs/cancel-export-task.html) command:

```
aws logs cancel-export-task --task-id "cda45419-90ea-4db5-9833-aade86253e66"
```

Note that you can use the [describe\-export\-tasks](http://docs.aws.amazon.com/cli/latest/reference/logs/describe-export-tasks.html) command to verify that the task was canceled successfully\.