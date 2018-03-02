# Encrypt Log Data in CloudWatch Logs Using AWS KMS<a name="encrypt-log-data-kms"></a>

You can encrypt the log data in CloudWatch Logs using an AWS Key Management Service \(AWS KMS\) customer master key \(CMK\)\. Encryption is enabled at the log group level, by associating a CMK with a log group, either when you create the log group or after it exists\.

After you associate a CMK with a log group, all newly ingested data for the log group is encrypted using the CMK\. This data is stored in encrypted format throughout its retention period\. CloudWatch Logs decrypts this data whenever it is requested\. CloudWatch Logs must have permissions for the CMK whenever encrypted data is requested\.

After you disassociate a CMK from a log group, CloudWatch Logs stops encrypting newly ingested data for the log group\. All previously ingested data remains encrypted\.

## Limits<a name="encryption-limits"></a>

+ To associate a CMK with a log group and perform the following steps, you must have the following permissions: `kms:CreateKey`, `kms:GetKeyPolicy`, and `kms:PutKeyPolicy`\.

+ After you associate or disassociate a CMK from a log group, it can take up to five minutes for the operation to take effect\.

+ If you revoke CloudWatch Logs access to an associated CMK or delete an associated CMK, your encrypted data in CloudWatch Logs can no longer be retrieved\.

+ You cannot associate a CMK with a log group using the CloudWatch console\.

## Step 1: Create an AWS KMS CMK<a name="create-cmk"></a>

To create an AWS KMS CMK, use the following [create\-key](http://docs.aws.amazon.com/cli/latest/reference/kms/create-key.html) command:

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

By default, all AWS KMS CMKs are private; only the resource owner can use it to encrypt and decrypt data\. However, the resource owner can grant permissions to access the CMK to other users and resources\. With this step, you give the CloudWatch service principal permission to use the CMK\. This service principal must be in the same region as where the CMK is stored\.

First, save the default policy for your CMK as `policy.json` using the following [get\-key\-policy](http://docs.aws.amazon.com/cli/latest/reference/kms/get-key-policy.html) command:

```
aws kms get-key-policy --key-id key-id --policy-name default --output text > ./policy.json
```

Open the `policy.json` file in a text editor and add the statement in bold, replacing *region* with the region to use for your log group and separating the existing statement from the new statement with a comma\.

```
{
  "Version" : "2012-10-17",
  "Id" : "key-default-1",
  "Statement" : [ {
      "Sid" : "Enable IAM User Permissions",
      "Effect" : "Allow",
      "Principal" : {
        "AWS" : "arn:aws:iam::880185128111:root"
      },
      "Action" : "kms:*",
      "Resource" : "*"
    },
    {
      "Effect": "Allow",
      "Principal": { "Service": "logs.region.amazonaws.com" },
      "Action": [ 
        "kms:Encrypt*",
        "kms:Decrypt*",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*",
        "kms:Describe*"
      ],
      "Resource": "*"
    }  
  ]
}
```

Finally, add the updated policy using the following [put\-key\-policy](http://docs.aws.amazon.com/cli/latest/reference/kms/put-key-policy.html) command:

```
aws kms put-key-policy --key-id key-id --policy-name default --policy file://policy.json
```

## Step 3: Associate a Log Group with a CMK<a name="associate-cmk"></a>

You can associate a CMK with a log group when you create it or afterwards\.

**To associate the CMK with a log group when you create it**  
Use the [create\-log\-group](http://docs.aws.amazon.com/cli/latest/reference/logs/create-log-group.html) command as follows:

```
aws logs create-log-group --log-group-name my-log-group --kms-key-id "key-arn"
```

**To associate the CMK with an existing log group**  
Use the [associate\-kms\-key](http://docs.aws.amazon.com/cli/latest/reference/logs/associate-kms-key.html) command as follows:

```
aws logs associate-kms-key --log-group-name my-log-group --kms-key-id "key-arn"
```

## Step 4: Disassociate a Log Group from a CMK<a name="disassociate-cmk"></a>

To disassociate the CMK associated with a log group, use the following [disassociate\-kms\-key](http://docs.aws.amazon.com/cli/latest/reference/logs/disassociate-kms-key.html) command:

```
aws logs disassociate-kms-key --log-group-name my-log-group
```