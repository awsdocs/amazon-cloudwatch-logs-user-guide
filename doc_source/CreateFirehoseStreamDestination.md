# Step 2: Create a destination<a name="CreateFirehoseStreamDestination"></a>

**Important**  
All steps in this procedure are to be done in the log data recipient account\.

When the destination is created, CloudWatch Logs sends a test message to the destination on the recipient account’s behalf\. When the subscription filter is active later, CloudWatch Logs sends log events to the destination on the source account’s behalf\.

**To create a destination**

1. Wait until the Kinesis Data Firehose stream that you created in [Step 1: Create a Kinesis Data Firehose delivery stream](CreateFirehoseStream.md) becomes active\. You can use the following command to check the **StreamDescription\.StreamStatus** property\.

   ```
   aws firehose describe-delivery-stream --delivery-stream-name "my-delivery-stream"
   ```

   In addition, take note of the **DeliveryStreamDescription\.DeliveryStreamARN** value, because you will need to use it in a later step\. Sample output of this command:

   ```
   {
       "DeliveryStreamDescription": {
           "DeliveryStreamName": "my-delivery-stream",
           "DeliveryStreamARN": "arn:aws:firehose:us-east-1:222222222222:deliverystream/my-delivery-stream",
           "DeliveryStreamStatus": "ACTIVE",
           "DeliveryStreamEncryptionConfiguration": {
               "Status": "DISABLED"
           },
           "DeliveryStreamType": "DirectPut",
           "VersionId": "1",
           "CreateTimestamp": "2021-02-01T23:59:15.567000-08:00",
           "Destinations": [
               {
                   "DestinationId": "destinationId-000000000001",
                   "S3DestinationDescription": {
                       "RoleARN": "arn:aws:iam::222222222222:role/FirehosetoS3Role",
                       "BucketARN": "arn:aws:s3:::firehose-test-bucket1",
                       "BufferingHints": {
                           "SizeInMBs": 5,
                           "IntervalInSeconds": 300
                       },
                       "CompressionFormat": "UNCOMPRESSED",
                       "EncryptionConfiguration": {
                           "NoEncryptionConfig": "NoEncryption"
                       },
                       "CloudWatchLoggingOptions": {
                           "Enabled": false
                       }
                   },
                   "ExtendedS3DestinationDescription": {
                       "RoleARN": "arn:aws:iam::222222222222:role/FirehosetoS3Role",
                       "BucketARN": "arn:aws:s3:::firehose-test-bucket1",
                       "BufferingHints": {
                           "SizeInMBs": 5,
                           "IntervalInSeconds": 300
                       },
                       "CompressionFormat": "UNCOMPRESSED",
                       "EncryptionConfiguration": {
                           "NoEncryptionConfig": "NoEncryption"
                       },
                       "CloudWatchLoggingOptions": {
                           "Enabled": false
                       },
                       "S3BackupMode": "Disabled"
                   }
               }
           ],
           "HasMoreDestinations": false
       }
   }
   ```

   It might take a minute or two for your delivery stream to show up in the active state\.

1. When the delivery stream is active, create the IAM role that will grant CloudWatch Logs the permission to put data into your Kinesis Data Firehose stream\. First, you'll need to create a trust policy in a file **\~/TrustPolicyForCWL\.json**\. Use a text editor to create this policy\. For more information about CloudWatch Logs endpoints, see [ Amazon CloudWatch Logs endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/cwl_region.html)\. 

   This policy includes a `aws:SourceArn` global condition context key that specifies the `sourceAccountId` to help prevent the confused deputy security problem\. If you don't yet know the source account ID in the first call, we recommend that you put the destination ARN in the source ARN field\. In the subsequent calls, you should set the source ARN to be the actual source ARN that you gathered from the first call\. For more information, see [Confused deputy prevention](Subscriptions-confused-deputy.md)\. 

   ```
   {
       "Statement": {
           "Effect": "Allow",
           "Principal": {
               "Service": "logs.us-east-1.amazonaws.com"
           },
           "Action": "sts:AssumeRole",
           "Condition": {
               "StringLike": {
                   "aws:SourceArn": [
                       "arn:aws:logs:region:sourceAccountId:*",
                       "arn:aws:logs:region:recipientAccountId:*"
                   ]
               }
           }
        }
   }
   ```

1. Use the **aws iam create\-role** command to create the IAM role, specifying the trust policy file that you just created\. 

   ```
   aws iam create-role \
         --role-name CWLtoKinesisFirehoseRole \
         --assume-role-policy-document file://~/TrustPolicyForCWL.json
   ```

   The following is a sample output\. Take note of the returned Role\.Arn value, because you will need to use it in a later step\.

   ```
   {
       "Role": {
           "Path": "/",
           "RoleName": "CWLtoKinesisFirehoseRole",
           "RoleId": "AROAR3BXASEKYJYWF243H",
           "Arn": "arn:aws:iam::222222222222:role/CWLtoKinesisFirehoseRole",
           "CreateDate": "2021-02-02T08:10:43+00:00",
           "AssumeRolePolicyDocument": {
               "Statement": {
                   "Effect": "Allow",
                   "Principal": {
                       "Service": "logs.us-east-1.amazonaws.com"
                   },
                   "Action": "sts:AssumeRole",
                   "Condition": {
                       "StringLike": {
                           "aws:SourceArn": [
                               "arn:aws:logs:region:sourceAccountId:*",
                               "arn:aws:logs:region:recipientAccountId:*"
                           ]
                       }
                   }
               }
           }
       }
   }
   ```

1. Create a permissions policy to define which actions CloudWatch Logs can perform on your account\. First, use a text editor to create a permissions policy in a file **\~/PermissionsForCWL\.json**:

   ```
   {
       "Statement":[
         {
           "Effect":"Allow",
           "Action":["firehose:*"],
           "Resource":["arn:aws:firehose:region:222222222222:*"]
         }
       ]
   }
   ```

1. Associate the permissions policy with the role by entering the following command:

   ```
   aws iam put-role-policy --role-name CWLtoKinesisFirehoseRole --policy-name Permissions-Policy-For-CWL --policy-document file://~/PermissionsForCWL.json
   ```

1. After the Kinesis Data Firehose delivery stream is in the active state and you have created the IAM role, you can create the CloudWatch Logs destination\.

   1. This step will not associate an access policy with your destination and is only the first step out of two that completes a destination creation\. Make a note of the **targetAarn** that is returned in the payload, because you will use this as the `destination.arn` in a later step\.

      ```
      aws logs put-destination \                                                       
          --destination-name "testFirehoseDestination" \
          --target-arn "arn:aws:firehose:us-east-1:222222222222:deliverystream/my-delivery-stream" \
          --role-arn "arn:aws:iam::222222222222:role/CWLtoKinesisFirehoseRole"
      
      {
          "destination": {
              "destinationName": "testFirehoseDestination",
              "targetArn": "arn:aws:firehose:us-east-1:222222222222:deliverystream/my-delivery-stream",
              "roleArn": "arn:aws:iam::222222222222:role/CWLtoKinesisFirehoseRole",
              "arn": "arn:aws:logs:us-east-1:222222222222:destination:testFirehoseDestination"}
      }
      ```

   1. After the previous step is complete, in the log data recipient account \(222222222222\), associate an access policy with the destination\. This policy enables the log data sender account \(111111111111\) to access the destination in the log data recipient account \(222222222222\)\. You can use a text editor to put this policy in the **\~/AccessPolicy\.json** file:

      ```
      {
        "Version" : "2012-10-17",
        "Statement" : [
          {
            "Sid" : "",
            "Effect" : "Allow",
            "Principal" : {
              "AWS" : "111111111111"
            },
            "Action" : "logs:PutSubscriptionFilter",
            "Resource" : "arn:aws:logs:us-east-1:222222222222:destination:testFirehoseDestination"
          }
        ]
      }
      ```

   1. This creates a policy that defines who has write access to the destination\. This policy must specify the **logs:PutSubscriptionFilter** action to access the destination\. Cross\-account users will use the **PutSubscriptionFilter** action to send log events to the destination:

      ```
      aws logs put-destination-policy \
          --destination-name "testFirehoseDestination" \
          --access-policy file://~/AccessPolicy.json
      ```