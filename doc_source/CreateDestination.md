# Create a Destination<a name="CreateDestination"></a>

**Important**  
All steps in this procedure are to be done in the log data recipient account\.

For this example, the log data recipient account has an AWS account ID of 999999999999, while the log data sender AWS account ID is 111111111111\.

 This example creates a destination using a Kinesis stream called RecipientStream, and a role that enables CloudWatch Logs to write data to it\. 

**To create a destination**

1. Create a destination stream in Kinesis\. At a command prompt, type:

   ```
   aws kinesis create-stream --stream-name "RecipientStream" --shard-count 1
   ```

1. Wait until the Kinesis stream becomes active\. You can use the **aws kinesis describe\-stream** command to check the **StreamDescription\.StreamStatus** property\. In addition, take note of the **StreamDescription\.StreamARN** value because it will be passed to CloudWatch Logs later:

   ```
   aws kinesis describe-stream --stream-name "RecipientStream"
   {
     "StreamDescription": {
       "StreamStatus": "ACTIVE",
       "StreamName": "RecipientStream",
       "StreamARN": "arn:aws:kinesis:us-east-1:999999999999:stream/RecipientStream",
       "Shards": [
         {
           "ShardId": "shardId-000000000000",
           "HashKeyRange": {
             "EndingHashKey": "34028236692093846346337460743176EXAMPLE",
             "StartingHashKey": "0"
           },
           "SequenceNumberRange": {
             "StartingSequenceNumber": "4955113521868881845667950383198145878459135270218EXAMPLE"
           }
         }
       ]
     }
   }
   ```

   It might take a minute or two for your stream to show up in the active state\.

1. Create the IAM role that will grant CloudWatch Logs the permission to put data into your Kinesis stream\. First, you'll need to create a trust policy in a file **\~/TrustPolicyForCWL\.json**\. Use a text editor to create this policy file, do not use the IAM console\.

   ```
   {
     "Statement": {
       "Effect": "Allow",
       "Principal": { "Service": "logs.region.amazonaws.com" },
       "Action": "sts:AssumeRole"
     }
   }
   ```

1. Use the **aws iam create\-role** command to create the IAM role, specifying the trust policy file\. Take note of the returned Role\.Arn value because that will also be passed to CloudWatch Logs later:

   ```
   aws iam create-role \
       --role-name CWLtoKinesisRole \
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
           "RoleName": "CWLtoKinesisRole",
           "Path": "/",
           "Arn": "arn:aws:iam::999999999999:role/CWLtoKinesisRole"
       }
   }
   ```

1. Create a permissions policy to define which actions CloudWatch Logs can perform on your account\. First, you'll use a text editor to create a permissions policy in a file **\~/PermissionsForCWL\.json**:

   ```
   {
     "Statement": [
       {
         "Effect": "Allow",
         "Action": "kinesis:PutRecord",
         "Resource": "arn:aws:kinesis:region:999999999999:stream/RecipientStream"
       }
     ]
   }
   ```

1. Associate the permissions policy with the role using the **aws iam put\-role\-policy** command:

   ```
   aws iam put-role-policy \
       --role-name CWLtoKinesisRole \
       --policy-name Permissions-Policy-For-CWL \
       --policy-document file://~/PermissionsForCWL.json
   ```

1. After the Kinesis stream is in the active state and you have created the IAM role, you can create the CloudWatch Logs destination\.

   1. This step will not associate an access policy with your destination and is only the first step out of two that completes a destination creation\. Make a note of the **DestinationArn** that is returned in the payload:

      ```
      aws logs put-destination \
          --destination-name "testDestination" \
          --target-arn "arn:aws:kinesis:region:999999999999:stream/RecipientStream" \
          --role-arn "arn:aws:iam::999999999999:role/CWLtoKinesisRole"
      
      {
        "DestinationName" : "testDestination",
        "RoleArn" : "arn:aws:iam::999999999999:role/CWLtoKinesisRole",
        "DestinationArn" : "arn:aws:logs:us-east-1:999999999999:destination:testDestination",
        "TargetArn" : "arn:aws:kinesis:us-east-1:999999999999:stream/RecipientStream"
      }
      ```

   1. After step 7a is complete, in the log data recipient account, associate an access policy with the destination\. This policy enables the log data sender account \(111111111111\) to access the destination in the log data recipient account \(999999999999\)\. You can use a text editor to put this policy in the **\~/AccessPolicy\.json** file:

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
            "Resource" : "arn:aws:logs:region:999999999999:destination:testDestination"
          }
        ]
      }
      ```
**Note**  
If multiple accounts are sending logs to this destination, each sender account must be listed separately in the policy\. This policy does not support specifying `*` as the `Principal` or the use of the `aws:PrincipalOrgId` global key\.

   1. This creates a policy that defines who has write access to the destination\. This policy must specify the **logs:PutSubscriptionFilter** action to access the destination\. Cross\-account users will use the **PutSubscriptionFilter** action to send log events to the destination:

      ```
      aws logs put-destination-policy \
          --destination-name "testDestination" \
          --access-policy file://~/AccessPolicy.json
      ```

      This access policy enables users in the AWS Account with ID 111111111111 to call **PutSubscriptionFilter** against the destination with ARN arn:aws:logs:*region*:999999999999:destination:testDestination\. Any other user's attempt to call PutSubscriptionFilter against this destination will be rejected\.

      To validate a user's privileges against an access policy, see [Using Policy Validator](https://docs.aws.amazon.com/IAM/latest/UserGuide/policies_policy-validator.html) in the *IAM User Guide*\.