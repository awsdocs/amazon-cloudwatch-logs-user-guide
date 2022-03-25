# Validating the flow of log events<a name="ValidateLogEventFlowFirehose"></a>

After you create the subscription filter, CloudWatch Logs forwards all the incoming log events that match the filter pattern to the Kinesis Data Firehose delivery stream\. The data starts appearing in your Amazon S3 bucket based on the time buffer interval that is set on the Kinesis Data Firehose delivery stream\. Once enough time has passed, you can verify your data by checking the Amazon S3 bucket\. To check the bucket, enter the following command:

```
aws s3api list-objects --bucket 'firehose-test-bucket1' 
```

The output of that command will be similar to the following:

```
{
    "Contents": [
        {
            "Key": "2021/02/02/08/my-delivery-stream-1-2021-02-02-08-55-24-5e6dc317-071b-45ba-a9d3-4805ba39c2ba",
            "LastModified": "2021-02-02T09:00:26+00:00",
            "ETag": "\"EXAMPLEa817fb88fc770b81c8f990d\"",
            "Size": 198,
            "StorageClass": "STANDARD",
            "Owner": {
                "DisplayName": "firehose+2test",
                "ID": "EXAMPLE27fd05889c665d2636218451970ef79400e3d2aecca3adb1930042e0"
            }
        }
    ]
}
```

You can then retrieve a specific object from the bucket by entering the following command\. Replace the value of `key` with the value you found in the previous command\.

```
aws s3api get-object --bucket 'firehose-test-bucket1' --key '2021/02/02/08/my-delivery-stream-1-2021-02-02-08-55-24-5e6dc317-071b-45ba-a9d3-4805ba39c2ba' testfile.gz
```

The data in the Amazon S3 object is compressed with the gzip format\. You can examine the raw data from the command line using one of the following commands:

Linux:

```
zcat testfile.gz
```

macOS:

```
zcat <testfile.gz
```