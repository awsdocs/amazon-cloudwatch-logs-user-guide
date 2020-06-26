# Validating the Flow of Log Events<a name="ValidateLogEventFlow"></a>

After you create the subscription filter, CloudWatch Logs forwards all the incoming log events that match the filter pattern to the Kinesis stream that is encapsulated within the destination stream called "**RecipientStream**"\. The destination owner can verify that this is happening by using the **aws kinesis get\-shard\-iterator** command to grab a Kinesis shard, and using the **aws kinesis get\-records** command to fetch some Kinesis records:

```
aws kinesis get-shard-iterator \
      --stream-name RecipientStream \
      --shard-id shardId-000000000000 \
      --shard-iterator-type TRIM_HORIZON

{
    "ShardIterator":
    "AAAAAAAAAAFGU/kLvNggvndHq2UIFOw5PZc6F01s3e3afsSscRM70JSbjIefg2ub07nk1y6CDxYR1UoGHJNP4m4NFUetzfL+wev+e2P4djJg4L9wmXKvQYoE+rMUiFq+p4Cn3IgvqOb5dRA0yybNdRcdzvnC35KQANoHzzahKdRGb9v4scv+3vaq+f+OIK8zM5My8ID+g6rMo7UKWeI4+IWiKEXAMPLE"
}

aws kinesis get-records \
      --limit 10 \
      --shard-iterator
      "AAAAAAAAAAFGU/kLvNggvndHq2UIFOw5PZc6F01s3e3afsSscRM70JSbjIefg2ub07nk1y6CDxYR1UoGHJNP4m4NFUetzfL+wev+e2P4djJg4L9wmXKvQYoE+rMUiFq+p4Cn3IgvqOb5dRA0yybNdRcdzvnC35KQANoHzzahKdRGb9v4scv+3vaq+f+OIK8zM5My8ID+g6rMo7UKWeI4+IWiKEXAMPLE"
```

**Note**  
You may need to rerun the get\-records command a few times before Kinesis starts to return data\.

You should see a response with an array of Kinesis records\. The data attribute in the Kinesis record is compressed in gzip format and then Base64 encoded\. You can examine the raw data from the command line using the following Unix command:

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
        "RecipientStream"
    ],
    "messageType": "DATA_MESSAGE",
    "logEvents": [
        {
            "id": "3195310660696698337880902507980421114328961542429EXAMPLE",
            "timestamp": 1432826855000,
            "message": "{\"eventVersion\":\"1.03\",\"userIdentity\":{\"type\":\"Root\"}"
        },
        {
            "id": "3195310660696698337880902507980421114328961542429EXAMPLE",
            "timestamp": 1432826855000,
            "message": "{\"eventVersion\":\"1.03\",\"userIdentity\":{\"type\":\"Root\"}"
        },
        {
            "id": "3195310660696698337880902507980421114328961542429EXAMPLE",
            "timestamp": 1432826855000,
            "message": "{\"eventVersion\":\"1.03\",\"userIdentity\":{\"type\":\"Root\"}"
        }
    ]
}
```

The key elements in this data structure are as follows:

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
The actual log data, represented as an array of log event records\. The ID property is a unique identifier for every log event\.