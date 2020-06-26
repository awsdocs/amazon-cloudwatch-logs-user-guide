# Encrypt Log Data in CloudWatch Logs Using AWS KMS<a name="encrypt-log-data-kms"></a>

Log group data is always encrypted in CloudWatch Logs\. You can optionally use AWS AWS Key Management Service for this encryption\. If you do, the encryption is done using an AWS KMS \(AWS KMS\) customer master key \(CMK\)\. Encryption using AWS KMS is enabled at the log group level, by associating a CMK with a log group, either when you create the log group or after it exists\.

**Important**  
CloudWatch Logs now supports encryption context, using `kms:EncryptionContext:aws:logs:arn` as the key and the ARN of the log group as the value for that key\. If you have log groups that you have already encrypted with a CMK, and you would like to restrict the CMK to be used with a single account and log group, you should assign a new CMK that includes a condition in the IAM policy\. For more information, see [KMS Keys and Encryption Context](#encrypt-log-data-kms-policy)\.

After you associate a CMK with a log group, all newly ingested data for the log group is encrypted using the CMK\. This data is stored in encrypted format throughout its retention period\. CloudWatch Logs decrypts this data whenever it is requested\. CloudWatch Logs must have permissions for the CMK whenever encrypted data is requested\.

After you disassociate a CMK from a log group, CloudWatch Logs stops encrypting newly ingested data for the log group\. All previously ingested data remains encrypted\.

**Important**  
CloudWatch Logs supports only symmetric CMKs\. Do not use an asymmetric CMK to encrypt the data in your log groups\. For more information, see [Using Symmetric and Asymmetric Keys](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html)\.

## Limits<a name="encryption-limits"></a>
+ To perform the following steps, you must have the following permissions: `kms:CreateKey`, `kms:GetKeyPolicy`, and `kms:PutKeyPolicy`\.
+ After you associate or disassociate a CMK from a log group, it can take up to five minutes for the operation to take effect\.
+ If you revoke CloudWatch Logs access to an associated CMK or delete an associated CMK, your encrypted data in CloudWatch Logs can no longer be retrieved\.
+ You cannot associate a CMK with a log group using the CloudWatch console\.

## Step 1: Create an AWS KMS CMK<a name="create-cmk"></a>

To create an AWS KMS CMK, use the following [create\-key](https://docs.aws.amazon.com/cli/latest/reference/kms/create-key.html) command:

```
aws kms create-key
```

The output contains the key ID and Amazon Resource Name \(ARN\) of the CMK\. The following is example output:

```
{
    "KeyMetadata": {
        "KeyId": "6f815f63-e628-448c-8251-e40cb0d29f59",
        "Description": "",
        "Enabled": true,
        "KeyUsage": "ENCRYPT_DECRYPT",
        "KeyState": "Enabled",
        "CreationDate": 1478910250.94,
        "Arn": "arn:aws:kms:us-west-2:123456789012:key/6f815f63-e628-448c-8251-e40cb0d29f59",
        "AWSAccountId": "123456789012"
    }
}
```

## Step 2: Set Permissions on the CMK<a name="cmk-permissions"></a>

By default, all AWS KMS CMKs are private\. Only the resource owner can use it to encrypt and decrypt data\. However, the resource owner can grant permissions to access the CMK to other users and resources\. With this step, you give the CloudWatch service principal permission to use the CMK\. This service principal must be in the same AWS Region where the CMK is stored\.

As a best practice, we recommend that you restrict the use of the key to only those AWS accounts or log groups you specify\.

First, save the default policy for your CMK as `policy.json` using the following [get\-key\-policy](https://docs.aws.amazon.com/cli/latest/reference/kms/get-key-policy.html) command:

```
aws kms get-key-policy --key-id key-id --policy-name default --output text > ./policy.json
```

Open the `policy.json` file in a text editor and add the section in bold from one of the following statements\. Separate the existing statement from the new statement with a comma\. These statements use `Condition` sections to enhance the security of the KMS key\. For more information, see [KMS Keys and Encryption Context](#encrypt-log-data-kms-policy)\.

The `Condition` section in this example restricts the key to a single log group ARN\.

```
{
 "Version": "2012-10-17",
    "Id": "key-default-1",
    "Statement": [
        {
            "Sid": "Enable IAM User Permissions",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::Your_account_ID:root"
            },
            "Action": "kms:*",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "logs.region.amazonaws.com"
            },
            "Action": [
                "kms:Encrypt*",
                "kms:Decrypt*",
                "kms:ReEncrypt*",
                "kms:GenerateDataKey*",
                "kms:Describe*"
            ],
            "Resource": "*",
            "Condition": {
                "ArnEquals": {
                    "kms:EncryptionContext:aws:logs:arn": "arn:aws:logs:region:account-id:log-group:log-group-name"
                }
            }
        }    
    ]
}
```

The `Condition` section in this example limits the use of the KMS to the specified account, but it can be used for any log group\.

```
{
    "Version": "2012-10-17",
    "Id": "key-default-1",
    "Statement": [
        {
            "Sid": "Enable IAM User Permissions",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::Your_account_ID:root"
            },
            "Action": "kms:*",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "logs.region.amazonaws.com"
            },
            "Action": [
                "kms:Encrypt*",
                "kms:Decrypt*",
                "kms:ReEncrypt*",
                "kms:GenerateDataKey*",
                "kms:Describe*"
            ],
            "Resource": "*",
            "Condition": {
                "ArnLike": {
                    "kms:EncryptionContext:aws:logs:arn": "arn:aws:logs:region:account-id:*"
                }
            }
        }    
    ]
}
```

Finally, add the updated policy using the following [put\-key\-policy](https://docs.aws.amazon.com/cli/latest/reference/kms/put-key-policy.html) command:

```
aws kms put-key-policy --key-id key-id --policy-name default --policy file://policy.json
```

## Step 3: Associate a Log Group with a CMK<a name="associate-cmk"></a>

You can associate a CMK with a log group when you create it or after it exists\.

To find whether a log group already has a CMK associated, use the following [describe\-log\-groups](https://docs.aws.amazon.com/cli/latest/reference/logs/describe-log-groups.html) command:

```
aws logs describe-log-groups --log-group-name-prefix "log-group-name-prefix"
```

If the output includes a `kmsKeyId` field, the log group is associated with the key displayed for the value of that field\.

**To associate the CMK with a log group when you create it**  
Use the [create\-log\-group](https://docs.aws.amazon.com/cli/latest/reference/logs/create-log-group.html) command as follows:

```
aws logs create-log-group --log-group-name my-log-group --kms-key-id "key-arn"
```

**To associate the CMK with an existing log group**  
Use the [associate\-kms\-key](https://docs.aws.amazon.com/cli/latest/reference/logs/associate-kms-key.html) command as follows:

```
aws logs associate-kms-key --log-group-name my-log-group --kms-key-id "key-arn"
```

## Step 4: Disassociate a Log Group from a CMK<a name="disassociate-cmk"></a>

To disassociate the CMK associated with a log group, use the following [disassociate\-kms\-key](https://docs.aws.amazon.com/cli/latest/reference/logs/disassociate-kms-key.html) command:

```
aws logs disassociate-kms-key --log-group-name my-log-group
```

## KMS Keys and Encryption Context<a name="encrypt-log-data-kms-policy"></a>

To enhance the security of your KMS keys and your encrypted log groups, CloudWatch Logs now puts log group ARNs as part of the *encryption context* used to encrypt your log data\. Encryption context is a set of key\-value pairs that are used as additional authenticated data\. The encryption context enables you to use IAM policy conditions to limit access to your KMS key by AWS account and log group\. For more information, see [Encryption context](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#encrypt_context) and [IAM JSON Policy Elements: Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html)\.

We recommend that you use different CMK keys for each of your encrypted log groups\.

If you have a log group that you encrypted previously and now want to change the log group to use a new CMK that works only for that log group, follow these steps\.

**To convert an encrypted log group to use a CMK with a policy limiting it to that log group**

1. Enter the following command to find the ARN of the log group's current CMK:

   ```
   aws logs describe-log-groups
   ```

   The output includes the following line\. Make a note of the ARN\. You need to use it in step 7\.

   ```
   ...
   "kmsKeyId": "arn:aws:kms:us-west-2:123456789012:key/01234567-89ab-cdef-0123-456789abcdef"
   ...
   ```

1. Enter the following command to create a new CMK:

   ```
   aws kms create-key
   ```

1. Enter the following command to save the new key's policy to a `policy.json` file:

   ```
   aws kms get-key-policy --key-id new-key-id --policy-name default --output text > ./policy.json
   ```

1. Use a text editor to open `policy.json` and add a `Condition` expression to the policy:

   ```
   {
       "Version": "2012-10-17",
       "Id": "key-default-1",
       "Statement": [
           {
               "Sid": "Enable IAM User Permissions",
               "Effect": "Allow",
               "Principal": {
                   "AWS": "arn:aws:iam::ACCOUNT-ID:root"
               },
               "Action": "kms:*",
               "Resource": "*"
           },
           {
               "Effect": "Allow",
               "Principal": {
                   "Service": "logs.region.amazonaws.com"
               },
               "Action": [
                   "kms:Encrypt*",
                   "kms:Decrypt*",
                   "kms:ReEncrypt*",
                   "kms:GenerateDataKey*",
                   "kms:Describe*"
               ],
               "Resource": "*",
               "Condition": {
                   "ArnLike": {
                       "kms:EncryptionContext:aws:logs:arn": "arn:aws:logs:REGION:ACCOUNT-ID:log-
   group:LOG-GROUP-NAME"
                   }
               }
           }
       ]
   }
   ```

1. Enter the following command to add the updated policy to the new CMK:

   ```
   aws kms put-key-policy --key-id new-key-ARN --policy-name default --policy file://policy.json
   ```

1. Enter the following command to associate the policy with your log group:

   ```
   aws logs associate-kms-key --log-group-name my-log-group --kms-key-id new-key-ARN
   ```

   CloudWatch Logs now encrypts all new data using the new CMK\.

1. Next, revoke all permissions except `Decrypt` from the old CMK\. First, enter the following command to retrieve the old policy:

   ```
   aws kms get-key-policy --key-id old-key-ARN --policy-name default --output text > ./policy.json
   ```

1. Use a text editor to open `policy.json` and remove all values from the `Action` list, except for `kms:Decrypt*`

   ```
   {
       "Version": "2012-10-17",
       "Id": "key-default-1",
       "Statement": [
           {
               "Sid": "Enable IAM User Permissions",
               "Effect": "Allow",
               "Principal": {
                   "AWS": "arn:aws:iam::REGION:root"
               },
               "Action": "kms:*",
               "Resource": "*"
           },
           {
               "Effect": "Allow",
               "Principal": {
                   "Service": "logs.region.amazonaws.com"
               },
               "Action": [
                   "kms:Decrypt*"
               ],
               "Resource": "*"
           }
       ]
   }
   ```

1. Enter the following command to add the updated policy to the old CMK:

   ```
   aws kms put-key-policy --key-id old-key-ARN --policy-name default --policy file://policy.json
   ```