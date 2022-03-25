# Step 1: Create a Kinesis Data Firehose delivery stream<a name="CreateFirehoseStream"></a>

**Important**  
All steps in Step 1 are to be done in the log data recipient account\.  
In the following examples, US East \(N\. Virginia\) is used in the sample commands\. Replace this with the correct Region for your deployment\.

**To create a Kinesis Data Firehose delivery stream to be used as the destination**

1. Create an Amazon S3 bucket:

   ```
   aws s3api create-bucket --bucket firehose-test-bucket1 --create-bucket-configuration LocationConstraint=us-east-1
   ```

1. Create the IAM role that grants Kinesis Data Firehose permission to put data into the bucket\.

   1. First, use a text editor to create a trust policy in a file `~/TrustPolicyForFirehose.json`\.

      ```
      { "Statement": { "Effect": "Allow", "Principal": { "Service": "firehose.amazonaws.com" }, "Action": "sts:AssumeRole", "Condition": { "StringEquals": { "sts:ExternalId":"222222222222" } } } }
      ```

   1. Create the IAM role, specifying the trust policy file that you just made\.

      ```
      aws iam create-role \ 
          --role-name FirehosetoS3Role \ 
          --assume-role-policy-document file://~/TrustPolicyForFirehose.json
      ```

   1. The output of this command will look similar to the following\. Make a note of the role name and the role ARN\.

      ```
      {
          "Role": {
              "Path": "/",
              "RoleName": "FirehosetoS3Role",
              "RoleId": "AROAR3BXASEKW7K635M53",
              "Arn": "arn:aws:iam::222222222222:role/FirehosetoS3Role",
              "CreateDate": "2021-02-02T07:53:10+00:00",
              "AssumeRolePolicyDocument": {
                  "Statement": {
                      "Effect": "Allow",
                      "Principal": {
                          "Service": "firehose.amazonaws.com"
                      },
                      "Action": "sts:AssumeRole",
                      "Condition": {
                          "StringEquals": {
                              "sts:ExternalId": "222222222222"
                          }
                      }
                  }
              }
          }
      }
      ```

1. Enter the following command to create the Kinesis Data Firehose delivery stream\. Replace *my\-role\-arn* and *my\-bucket\-arn* with the correct values for your deployment\.

   ```
   aws firehose create-delivery-stream \
      --delivery-stream-name 'my-delivery-stream' \
      --s3-destination-configuration \
     '{"RoleARN": "arn:aws:iam::222222222222:role/FirehosetoS3Role", "BucketARN": "arn:aws:s3:::firehose-test-bucket1"}'
   ```

   The output should look similar to the following:

   ```
   {
       "DeliveryStreamARN": "arn:aws:firehose:us-east-1:222222222222:deliverystream/my-delivery-stream"
   }
   ```