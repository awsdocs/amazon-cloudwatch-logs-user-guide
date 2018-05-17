# Export Log Data to Amazon S3 Using the Console<a name="S3ExportTasksConsole"></a>

In the following example, you'll use the Amazon CloudWatch console to export all data from an Amazon CloudWatch Logs log group named "my\-log\-group" to an Amazon S3 bucket named "my\-exported\-logs\."

## Step 1: Create an Amazon S3 Bucket<a name="CreateS3BucketConsole"></a>

We recommend that you use a bucket that was created specifically for CloudWatch Logs\. However, if you want to use an existing bucket, you can skip to step 2\.

**Note**  
The Amazon S3 bucket must reside in the same region as the log data to export\. CloudWatch Logs does not support exporting data to Amazon S3 buckets in a different region\.

**To create an Amazon S3 bucket**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. If necessary, change the region\. From the navigation bar, choose the region where your CloudWatch Logs reside\.

1. Choose **Create Bucket**\.

1. For **Bucket Name**, type a name for the bucket\.

1. For **Region**, select the region where your CloudWatch Logs data resides\.

1. Choose **Create**\.

## Step 2: Set Permissions on an Amazon S3 Bucket<a name="S3PermissionsConsole"></a>

By default, all Amazon S3 buckets and objects are private\. Only the resource owner, the AWS account that created the bucket, can access the bucket and any objects it contains\. However, the resource owner can choose to grant access permissions to other resources and users by writing an access policy\.

When you set the policy, we recommend you include a randomly\-generated string as the prefix for the bucket, so that only intended log streams are exported to the bucket\.

**To set permissions on an Amazon S3 bucket**

1. In the Amazon S3 console, choose the bucket that you created in Step 1\.

1. Choose **Permissions**, **Bucket policy**\.

1. In the **Bucket Policy Editor** dialog box, add the following policy, changing `my-exported-logs` to the name of your S3 bucket and `random-string` to a randomly\-generated string of characters\. Be sure to specify the correct region endpoint for **Principal**\.

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
             "Resource": "arn:aws:s3:::my-exported-logs/random-string/*",
             "Condition": { "StringEquals": { "s3:x-amz-acl": "bucket-owner-full-control" } },
             "Principal": { "Service": "logs.us-west-2.amazonaws.com" }
         }
       ]
   }
   ```

1. Choose **Save** to set the policy that you just added as the access policy on your bucket\. This policy enables CloudWatch Logs to export log data to your Amazon S3 bucket\. The bucket owner has full permissions on all of the exported objects\.
**Warning**  
If the existing bucket already has one or more policies attached to it, add the statements for CloudWatch Logs access to that policy or policies\. We recommend that you evaluate the resulting set of permissions to be sure that they are appropriate for the users who will access the bucket\.

## Step 3: Create an Export Task<a name="CreateExportTaskConsole"></a>

In this step you create the export task for exporting logs from a log group\.

**To export data to Amazon S3 using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**\.

1. On the **Log Groups** screen, select the button next to a log group, and then choose **Actions**, **Export data to Amazon S3**\.

1. On the **Export data to Amazon S3** screen, under **Define data to export**, set the time range for the data to export using **From** and **To**\.

1. If your log group has multiple log streams, you can provide a log stream prefix to limit the log group data to a specific stream\. Choose **Advanced**, and then for **Stream prefix**, type the log stream prefix\.

1. Under **Choose S3 bucket**, choose the account associated with the Amazon S3 bucket\.

1. For **S3 bucket name**, choose an Amazon S3 bucket\.

1. Choose **Advanced**, and then for **S3 Bucket prefix**, type the randomly\-generated string you specified in the bucket policy\.

1. Choose **Export data** to export your log data to Amazon S3\.

1. To view the status of the log data that you exported to Amazon S3, choose **Actions**, **View all exports to Amazon S3**\.