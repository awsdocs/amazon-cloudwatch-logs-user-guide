# Using CloudWatch Logs Subscription Filters<a name="SubscriptionFilters"></a>

You can use a subscription filter with Kinesis, Lambda, or Kinesis Data Firehose\. Logs that are sent to a receiving service through a subscription filter are Base64 encoded and compressed with the gzip format\.

**Topics**
+ [Example 1: Subscription Filters with Kinesis](#DestinationKinesisExample)
+ [Example 2: Subscription Filters with AWS Lambda](#LambdaFunctionExample)
+ [Example 3: Subscription Filters with Amazon Kinesis Data Firehose](#FirehoseExample)

## Example 1: Subscription Filters with Kinesis<a name="DestinationKinesisExample"></a>

The following example associates a subscription filter with a log group containing AWS CloudTrail events to have every logged activity made by "Root" AWS credentials delivered to a Kinesis stream called "RootAccess\." For more information about how to send AWS CloudTrail events to CloudWatch Logs, see [Sending CloudTrail Events to CloudWatch Logs](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cw_send_ct_events.html) in the *AWS CloudTrail User Guide*\.

**Note**  
Before you create the Kinesis stream, calculate the volume of log data that will be generated\. Be sure to create a Kinesis stream with enough shards to handle this volume\. If the stream does not have enough shards, the log stream will be throttled\. For more information about Kinesis stream volume limits, see [Amazon Kinesis Data Streams Limits](https://docs.aws.amazon.com/streams/latest/dev/service-sizes-and-limits.html)\. 

**To create a subscription filter for Kinesis**

1. Create a destination Kinesis stream using the following command: 

   ```
   $ C:\>  aws kinesis create-stream --stream-name "RootAccess" --shard-count 1
   ```

1. Wait until the Kinesis stream becomes Active \(this might take a minute or two\)\. You can use the following Kinesis [describe\-stream](https://docs.aws.amazon.com/cli/latest/reference/kinesis/describe-stream.html) command to check the **StreamDescription\.StreamStatus** property\. In addition, note the **StreamDescription\.StreamARN** value, as you will need it in a later step:

   ```
   aws kinesis describe-stream --stream-name "RootAccess"
   ```

   The following is example output:

   ```
   {
       "StreamDescription": {
           "StreamStatus": "ACTIVE",
           "StreamName": "RootAccess",
           "StreamARN": "arn:aws:kinesis:us-east-1:123456789012:stream/RootAccess",
           "Shards": [
               {
                   "ShardId": "shardId-000000000000",
                   "HashKeyRange": {
                       "EndingHashKey": "340282366920938463463374607431768211455",
                       "StartingHashKey": "0"
                   },
                   "SequenceNumberRange": {
                       "StartingSequenceNumber":
                       "49551135218688818456679503831981458784591352702181572610"
                   }
               }
           ]
       }
   }
   ```

1. Create the IAM role that will grant CloudWatch Logs permission to put data into your Kinesis stream\. First, you'll need to create a trust policy in a file \(for example, `~/TrustPolicyForCWL.json`\)\. Use a text editor to create this policy\. Do not use the IAM console to create it\.

   ```
   {
     "Statement": {
       "Effect": "Allow",
       "Principal": { "Service": "logs.region.amazonaws.com" },
       "Action": "sts:AssumeRole"
     }
   }
   ```

1. Use the **create\-role** command to create the IAM role, specifying the trust policy file\. Note the returned **Role\.Arn** value, as you will also need it for a later step:

   ```
   aws iam create-role --role-name CWLtoKinesisRole --assume-role-policy-document file://~/TrustPolicyForCWL.json
   ```

   The following is an example of the output\.

   ```
   {
       "Role": {
           "AssumeRolePolicyDocument": {
               "Statement": {
                   "Action": "sts:AssumeRole",
                   "Effect": "Allow",
                   "Principal": {
                       "Service": "logs.region.amazonaws.com"
                   }
               }
           },
           "RoleId": "AAOIIAH450GAB4HC5F431",
           "CreateDate": "2015-05-29T13:46:29.431Z",
           "RoleName": "CWLtoKinesisRole",
           "Path": "/",
           "Arn": "arn:aws:iam::123456789012:role/CWLtoKinesisRole"
       }
   }
   ```

1. Create a permissions policy to define what actions CloudWatch Logs can do on your account\. First, you'll create a permissions policy in a file \(for example, `~/PermissionsForCWL.json`\)\. Use a text editor to create this policy\. Do not use the IAM console to create it\.

   ```
   {
     "Statement": [
       {
         "Effect": "Allow",
         "Action": "kinesis:PutRecord",
         "Resource": "arn:aws:kinesis:region:123456789012:stream/RootAccess"
       }
     ]
   }
   ```

1. Associate the permissions policy with the role using the following [put\-role\-policy](https://docs.aws.amazon.com/cli/latest/reference/iam/put-role-policy.html) command:

   ```
   aws iam put-role-policy  --role-name CWLtoKinesisRole  --policy-name Permissions-Policy-For-CWL  --policy-document file://~/PermissionsForCWL.json
   ```

1. After the Kinesis stream is in **Active** state and you have created the IAM role, you can create the CloudWatch Logs subscription filter\. The subscription filter immediately starts the flow of real\-time log data from the chosen log group to your Kinesis stream:

   ```
   aws logs put-subscription-filter \
       --log-group-name "CloudTrail" \
       --filter-name "RootAccess" \
       --filter-pattern "{$.userIdentity.type = Root}" \
       --destination-arn "arn:aws:kinesis:region:123456789012:stream/RootAccess" \
       --role-arn "arn:aws:iam::123456789012:role/CWLtoKinesisRole"
   ```

1. After you set up the subscription filter, CloudWatch Logs forwards all the incoming log events that match the filter pattern to your Kinesis stream\. You can verify that this is happening by grabbing a Kinesis shard iterator and using the Kinesis get\-records command to fetch some Kinesis records:

   ```
   aws kinesis get-shard-iterator --stream-name RootAccess --shard-id shardId-000000000000 --shard-iterator-type TRIM_HORIZON
   ```

   ```
   {
       "ShardIterator":
       "AAAAAAAAAAFGU/kLvNggvndHq2UIFOw5PZc6F01s3e3afsSscRM70JSbjIefg2ub07nk1y6CDxYR1UoGHJNP4m4NFUetzfL+wev+e2P4djJg4L9wmXKvQYoE+rMUiFq+p4Cn3IgvqOb5dRA0yybNdRcdzvnC35KQANoHzzahKdRGb9v4scv+3vaq+f+OIK8zM5My8ID+g6rMo7UKWeI4+IWiK2OSh0uP"
   }
   ```

   ```
   aws kinesis get-records --limit 10 --shard-iterator "AAAAAAAAAAFGU/kLvNggvndHq2UIFOw5PZc6F01s3e3afsSscRM70JSbjIefg2ub07nk1y6CDxYR1UoGHJNP4m4NFUetzfL+wev+e2P4djJg4L9wmXKvQYoE+rMUiFq+p4Cn3IgvqOb5dRA0yybNdRcdzvnC35KQANoHzzahKdRGb9v4scv+3vaq+f+OIK8zM5My8ID+g6rMo7UKWeI4+IWiK2OSh0uP"
   ```

   Note that you might need to make this call a few times before Kinesis starts to return data\.

   You should expect to see a response with an array of records\. The **Data** attribute in a Kinesis record is Base64 encoded and compressed with the gzip format\. You can examine the raw data from the command line using the following Unix commands:

   ```
   echo -n "<Content of Data>" | base64 -d | zcat
   ```

   The Base64 decoded and decompressed data is formatted as JSON with the following structure:

   ```
   {
       "owner": "111111111111",
       "logGroup": "CloudTrail",
       "logStream": "111111111111_CloudTrail_us-east-1",
       "subscriptionFilters": [
           "Destination"
       ],
       "messageType": "DATA_MESSAGE",
       "logEvents": [
           {
               "id": "31953106606966983378809025079804211143289615424298221568",
               "timestamp": 1432826855000,
               "message": "{\"eventVersion\":\"1.03\",\"userIdentity\":{\"type\":\"Root\"}"
           },
           {
               "id": "31953106606966983378809025079804211143289615424298221569",
               "timestamp": 1432826855000,
               "message": "{\"eventVersion\":\"1.03\",\"userIdentity\":{\"type\":\"Root\"}"
           },
           {
               "id": "31953106606966983378809025079804211143289615424298221570",
               "timestamp": 1432826855000,
               "message": "{\"eventVersion\":\"1.03\",\"userIdentity\":{\"type\":\"Root\"}"
           }
       ]
   }
   ```

   The key elements in the above data structure are the following:  
**owner**  
The AWS Account ID of the originating log data\.  
**logGroup**  
The log group name of the originating log data\.  
**logStream**  
The log stream name of the originating log data\.  
**subscriptionFilters**  
The list of subscription filter names that matched with the originating log data\.  
**messageType**  
Data messages will use the "DATA\_MESSAGE" type\. Sometimes CloudWatch Logs may emit Kinesis records with a "CONTROL\_MESSAGE" type, mainly for checking if the destination is reachable\.  
**logEvents**  
The actual log data, represented as an array of log event records\. The "id" property is a unique identifier for every log event\.

## Example 2: Subscription Filters with AWS Lambda<a name="LambdaFunctionExample"></a>

In this example, you'll create a CloudWatch Logs subscription filter that sends log data to your AWS Lambda function\.

**Note**  
Before you create the Lambda function, calculate the volume of log data that will be generated\. Be sure to create a function that can handle this volume\. If the function does not have enough volume, the log stream will be throttled\. For more information about Lambda limits, see [AWS Lambda Limits](https://docs.aws.amazon.com/lambda/latest/dg/limits.html)\. 

**To create a subscription filter for Lambda**

1. Create the AWS Lambda function\.

   Ensure that you have set up the Lambda execution role\. For more information, see [Step 2\.2: Create an IAM Role \(execution role\)](https://docs.aws.amazon.com/lambda/latest/dg/walkthrough-custom-events-create-test-function.html) in the *AWS Lambda Developer Guide*\.

1. Open a text editor and create a file named `helloWorld.js` with the following contents:

   ```
   var zlib = require('zlib');
   exports.handler = function(input, context) {
       var payload = Buffer.from(input.awslogs.data, 'base64');
       zlib.gunzip(payload, function(e, result) {
           if (e) { 
               context.fail(e);
           } else {
               result = JSON.parse(result.toString('ascii'));
               console.log("Event Data:", JSON.stringify(result, null, 2));
               context.succeed();
           }
       });
   };
   ```

1. Zip the file helloWorld\.js and save it with the name `helloWorld.zip`\.

1. Use the following command, where the role is the Lambda execution role you set up in the first step:

   ```
   aws lambda create-function \
       --function-name helloworld \
       --zip-file fileb://file-path/helloWorld.zip \
       --role lambda-execution-role-arn \
       --handler helloWorld.handler \
       --runtime nodejs12.x
   ```

1. Grant CloudWatch Logs the permission to execute your function\. Use the following command, replacing the placeholder account with your own account and the placeholder log group with the log group to process:

   ```
   aws lambda add-permission \
       --function-name "helloworld" \
       --statement-id "helloworld" \
       --principal "logs.region.amazonaws.com" \
       --action "lambda:InvokeFunction" \
       --source-arn "arn:aws:logs:region:123456789123:log-group:TestLambda:*" \
       --source-account "123456789012"
   ```

1. Create a subscription filter using the following command, replacing the placeholder account with your own account and the placeholder log group with the log group to process:

   ```
   aws logs put-subscription-filter \
       --log-group-name myLogGroup \
       --filter-name demo \
       --filter-pattern "" \
       --destination-arn arn:aws:lambda:region:123456789123:function:helloworld
   ```

1. \(Optional\) Test using a sample log event\. At a command prompt, run the following command, which will put a simple log message into the subscribed stream\.

   To see the output of your Lambda function, navigate to the Lambda function where you will see the output in /aws/lambda/helloworld:

   ```
   aws logs put-log-events --log-group-name myLogGroup --log-stream-name stream1 --log-events "[{\"timestamp\":<CURRENT TIMESTAMP MILLIS> , \"message\": \"Simple Lambda Test\"}]"
   ```

   You should expect to see a response with an array of Lambda\. The **Data** attribute in the Lambda record is Base64 encoded and compressed with the gzip format\. The actual payload that Lambda receives is in the following format `{ "awslogs": {"data": "BASE64ENCODED_GZIP_COMPRESSED_DATA"} }` You can examine the raw data from the command line using the following Unix commands:

   ```
   echo -n "<BASE64ENCODED_GZIP_COMPRESSED_DATA>" | base64 -d | zcat
   ```

   The Base64 decoded and decompressed data is formatted as JSON with the following structure:

   ```
   {
       "owner": "123456789012",
       "logGroup": "CloudTrail",
       "logStream": "123456789012_CloudTrail_us-east-1",
       "subscriptionFilters": [
           "Destination"
       ],
       "messageType": "DATA_MESSAGE",
       "logEvents": [
           {
               "id": "31953106606966983378809025079804211143289615424298221568",
               "timestamp": 1432826855000,
               "message": "{\"eventVersion\":\"1.03\",\"userIdentity\":{\"type\":\"Root\"}"
           },
           {
               "id": "31953106606966983378809025079804211143289615424298221569",
               "timestamp": 1432826855000,
               "message": "{\"eventVersion\":\"1.03\",\"userIdentity\":{\"type\":\"Root\"}"
           },
           {
               "id": "31953106606966983378809025079804211143289615424298221570",
               "timestamp": 1432826855000,
               "message": "{\"eventVersion\":\"1.03\",\"userIdentity\":{\"type\":\"Root\"}"
           }
       ]
   }
   ```

   The key elements in the above data structure are the following:  
**owner**  
The AWS Account ID of the originating log data\.  
**logGroup**  
The log group name of the originating log data\.  
**logStream**  
The log stream name of the originating log data\.  
**subscriptionFilters**  
The list of subscription filter names that matched with the originating log data\.  
**messageType**  
Data messages will use the "DATA\_MESSAGE" type\. Sometimes CloudWatch Logs may emit Lambda records with a "CONTROL\_MESSAGE" type, mainly for checking if the destination is reachable\.  
**logEvents**  
The actual log data, represented as an array of log event records\. The "id" property is a unique identifier for every log event\.

## Example 3: Subscription Filters with Amazon Kinesis Data Firehose<a name="FirehoseExample"></a>

In this example, you'll create a CloudWatch Logs subscription that sends any incoming log events that match your defined filters to your Amazon Kinesis Data Firehose delivery stream\. Data sent from CloudWatch Logs to Amazon Kinesis Data Firehose is already compressed with gzip level 6 compression, so you do not need to use compression within your Kinesis Data Firehose delivery stream\.

**Note**  
Before you create the Kinesis Data Firehose stream, calculate the volume of log data that will be generated\. Be sure to create a Kinesis Data Firehose stream that can handle this volume\. If the stream cannot handle the volume, the log stream will be throttled\. For more information about Kinesis Data Firehose stream volume limits, see [Amazon Kinesis Data Firehose Data Limits](https://docs.aws.amazon.com/firehose/latest/dev/limits.html)\. 

**To create a subscription filter for Kinesis Data Firehose**

1. Create an Amazon Simple Storage Service \(Amazon S3\) bucket\. We recommend that you use a bucket that was created specifically for CloudWatch Logs\. However, if you want to use an existing bucket, skip to step 2\.

   Run the following command, replacing the placeholder region with the region you want to use:

   ```
   aws s3api create-bucket --bucket my-bucket --create-bucket-configuration LocationConstraint=region
   ```

   The following is example output:

   ```
   {
       "Location": "/my-bucket"
   }
   ```

1. Create the IAM role that will grant Amazon Kinesis Data Firehose permission to put data into your Amazon S3 bucket\.

   For more information, see [Controlling Access with Amazon Kinesis Data Firehose](https://docs.aws.amazon.com/firehose/latest/dev/controlling-access.html) in the *Amazon Kinesis Data Firehose Developer Guide*\.

   First, use a text editor to create a trust policy in a file `~/TrustPolicyForFirehose.json` as follows, replacing *account\-id* with your AWS account ID:

   ```
   {
     "Statement": {
       "Effect": "Allow",
       "Principal": { "Service": "firehose.amazonaws.com" },
       "Action": "sts:AssumeRole",
       "Condition": { "StringEquals": { "sts:ExternalId":"account-id" } }
     }
   }
   ```

1. Use the **create\-role** command to create the IAM role, specifying the trust policy file\. Note of the returned **Role\.Arn** value, as you will need it in a later step:

   ```
   aws iam create-role \
         --role-name FirehosetoS3Role \
         --assume-role-policy-document file://~/TrustPolicyForFirehose.json
   
   {
       "Role": {
           "AssumeRolePolicyDocument": {
               "Statement": {
                   "Action": "sts:AssumeRole",
                   "Effect": "Allow",
                   "Principal": {
                       "Service": "firehose.amazonaws.com"
                   }
               }
           },
           "RoleId": "AAOIIAH450GAB4HC5F431",
           "CreateDate": "2015-05-29T13:46:29.431Z",
           "RoleName": "FirehosetoS3Role",
           "Path": "/",
           "Arn": "arn:aws:iam::123456789012:role/FirehosetoS3Role"
       }
   }
   ```

1. Create a permissions policy to define what actions Kinesis Data Firehose can do on your account\. First, use a text editor to create a permissions policy in a file `~/PermissionsForFirehose.json`:

   ```
   {
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [ 
             "s3:AbortMultipartUpload", 
             "s3:GetBucketLocation", 
             "s3:GetObject", 
             "s3:ListBucket", 
             "s3:ListBucketMultipartUploads", 
             "s3:PutObject" ],
         "Resource": [ 
             "arn:aws:s3:::my-bucket", 
             "arn:aws:s3:::my-bucket/*" ]
       }
     ]
   }
   ```

1. Associate the permissions policy with the role using the following put\-role\-policy command:

   ```
   aws iam put-role-policy --role-name FirehosetoS3Role --policy-name Permissions-Policy-For-Firehose --policy-document file://~/PermissionsForFirehose.json
   ```

1. Create a destination Kinesis Data Firehose delivery stream as follows, replacing the placeholder values for **RoleARN** and **BucketARN** with the role and bucket ARNs that you created:

   ```
   aws firehose create-delivery-stream \
      --delivery-stream-name 'my-delivery-stream' \
      --s3-destination-configuration \
     '{"RoleARN": "arn:aws:iam::123456789012:role/FirehosetoS3Role", "BucketARN": "arn:aws:s3:::my-bucket"}'
   ```

   Note that Kinesis Data Firehose automatically uses a prefix in YYYY/MM/DD/HH UTC time format for delivered Amazon S3 objects\. You can specify an extra prefix to be added in front of the time format prefix\. If the prefix ends with a forward slash \(/\), it appears as a folder in the Amazon S3 bucket\.

1. Wait until the stream becomes active \(this might take a few minutes\)\. You can use the Kinesis Data Firehose **describe\-delivery\-stream** command to check the **DeliveryStreamDescription\.DeliveryStreamStatus** property\. In addition, note the **DeliveryStreamDescription\.DeliveryStreamARN** value, as you will need it in a later step:

   ```
   aws firehose describe-delivery-stream --delivery-stream-name "my-delivery-stream"
   {
       "DeliveryStreamDescription": {
           "HasMoreDestinations": false,
           "VersionId": "1",
           "CreateTimestamp": 1446075815.822,
           "DeliveryStreamARN": "arn:aws:firehose:us-east-1:123456789012:deliverystream/my-delivery-stream",
           "DeliveryStreamStatus": "ACTIVE",
           "DeliveryStreamName": "my-delivery-stream",
           "Destinations": [
               {
                   "DestinationId": "destinationId-000000000001",
                   "S3DestinationDescription": {
                       "CompressionFormat": "UNCOMPRESSED",
                       "EncryptionConfiguration": {
                           "NoEncryptionConfig": "NoEncryption"
                       },
                       "RoleARN": "delivery-stream-role",
                       "BucketARN": "arn:aws:s3:::my-bucket",
                       "BufferingHints": {
                           "IntervalInSeconds": 300,
                           "SizeInMBs": 5
                       }
                   }
               }
           ]
       }
   }
   ```

1. Create the IAM role that will grant CloudWatch Logs permission to put data into your Kinesis Data Firehose delivery stream\. First, use a text editor to create a trust policy in a file `~/TrustPolicyForCWL.json`:

   ```
   {
     "Statement": {
       "Effect": "Allow",
       "Principal": { "Service": "logs.region.amazonaws.com" },
       "Action": "sts:AssumeRole"
     }
   }
   ```

1. Use the **create\-role** command to create the IAM role, specifying the trust policy file\. Note of the returned **Role\.Arn** value, as you will need it in a later step:

   ```
   aws iam create-role \
         --role-name CWLtoKinesisFirehoseRole \
         --assume-role-policy-document file://~/TrustPolicyForCWL.json
   
   {
       "Role": {
           "AssumeRolePolicyDocument": {
               "Statement": {
                   "Action": "sts:AssumeRole",
                   "Effect": "Allow",
                   "Principal": {
                       "Service": "logs.region.amazonaws.com"
                   }
               }
           },
           "RoleId": "AAOIIAH450GAB4HC5F431",
           "CreateDate": "2015-05-29T13:46:29.431Z",
           "RoleName": "CWLtoKinesisFirehoseRole",
           "Path": "/",
           "Arn": "arn:aws:iam::123456789012:role/CWLtoKinesisFirehoseRole"
       }
   }
   ```

1. Create a permissions policy to define what actions CloudWatch Logs can do on your account\. First, use a text editor to create a permissions policy file \(for example, `~/PermissionsForCWL.json`\):

   ```
   {
       "Statement":[
         {
           "Effect":"Allow",
           "Action":["firehose:*"],
           "Resource":["arn:aws:firehose:region:123456789012:*"]
         }
       ]
   }
   ```

1. Associate the permissions policy with the role using the put\-role\-policy command:

   ```
   aws iam put-role-policy --role-name CWLtoKinesisFirehoseRole --policy-name Permissions-Policy-For-CWL --policy-document file://~/PermissionsForCWL.json
   ```

1. After the Amazon Kinesis Data Firehose delivery stream is in active state and you have created the IAM role, you can create the CloudWatch Logs subscription filter\. The subscription filter immediately starts the flow of real\-time log data from the chosen log group to your Amazon Kinesis Data Firehose delivery stream:

   ```
   aws logs put-subscription-filter \
       --log-group-name "CloudTrail" \
       --filter-name "Destination" \
       --filter-pattern "{$.userIdentity.type = Root}" \
       --destination-arn "arn:aws:firehose:region:123456789012:deliverystream/my-delivery-stream" \
       --role-arn "arn:aws:iam::123456789012:role/CWLtoKinesisFirehoseRole"
   ```

1. After you set up the subscription filter, CloudWatch Logs will forward all the incoming log events that match the filter pattern to your Amazon Kinesis Data Firehose delivery stream\. Your data will start appearing in your Amazon S3 based on the time buffer interval set on your Amazon Kinesis Data Firehose delivery stream\. Once enough time has passed, you can verify your data by checking your Amazon S3 Bucket\.

   ```
   aws s3api list-objects --bucket 'my-bucket' --prefix 'firehose/'
   {
       "Contents": [
           {
               "LastModified": "2015-10-29T00:01:25.000Z",
               "ETag": "\"a14589f8897f4089d3264d9e2d1f1610\"",
               "StorageClass": "STANDARD",
               "Key": "firehose/2015/10/29/00/my-delivery-stream-2015-10-29-00-01-21-a188030a-62d2-49e6-b7c2-b11f1a7ba250",
               "Owner": {
                   "DisplayName": "cloudwatch-logs",
                   "ID": "1ec9cf700ef6be062b19584e0b7d84ecc19237f87b5"
               },
               "Size": 593
           },
           {
               "LastModified": "2015-10-29T00:35:41.000Z",
               "ETag": "\"a7035b65872bb2161388ffb63dd1aec5\"",
               "StorageClass": "STANDARD",
               "Key": "firehose/2015/10/29/00/my-delivery-stream-2015-10-29-00-35-40-7cc92023-7e66-49bc-9fd4-fc9819cc8ed3",
               "Owner": {
                   "DisplayName": "cloudwatch-logs",
                   "ID": "1ec9cf700ef6be062b19584e0b7d84ecc19237f87b6"
               },
               "Size": 5752
           }
       ]
   }
   ```

   ```
   aws s3api get-object --bucket 'my-bucket' --key 'firehose/2015/10/29/00/my-delivery-stream-2015-10-29-00-01-21-a188030a-62d2-49e6-b7c2-b11f1a7ba250' testfile.gz
   
   {
       "AcceptRanges": "bytes",
       "ContentType": "application/octet-stream",
       "LastModified": "Thu, 29 Oct 2015 00:07:06 GMT",
       "ContentLength": 593,
       "Metadata": {}
   }
   ```

   The data in the Amazon S3 object is compressed with the gzip format\. You can examine the raw data from the command line using the following Unix command:

   ```
   zcat testfile.gz
   ```