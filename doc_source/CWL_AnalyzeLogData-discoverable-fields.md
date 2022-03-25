# Supported logs and discovered fields<a name="CWL_AnalyzeLogData-discoverable-fields"></a>

CloudWatch Logs Insights supports different log types\. For every log that's sent to Amazon CloudWatch Logs, CloudWatch Logs Insights automatically generates five system fields: 
+ `@message` contains the raw unparsed log event\. This is the equivalent to the `message` field in [InputLogevent](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_InputLogEvent.html)\.
+ `@timestamp` contains the event timestamp in the log event's `timestamp` field\. This is the equivalent to the `timestamp` field in [InputLogevent](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_InputLogEvent.html)\.
+ `@ingestionTime` contains the time when CloudWatch Logs received the log event\.
+ `@logStream` contains the name of the log stream that the log event was added to\. Log streams group logs through the same process that generated them\.
+ `@log` is a log group identifier in the form of `account-id:log-group-name`\. When querying multiple log groups, this can be useful to identify which log group a particular event belongs to\.

CloudWatch Logs Insights inserts the **@** symbol at the start of fields that it generates\.

For many log types, CloudWatch Logs also automatically discovers the log fields contained in the logs\. These automatic discovery fields are shown in the following table\.

For other types of logs with fields that CloudWatch Logs Insights doesn't automatically discover, you can use the `parse` command to extract and create ephemeral fields for use in that query\. For more information, see [CloudWatch Logs Insights query syntax](CWL_QuerySyntax.md)\.

If the name of a discovered log field starts with the `@` character, CloudWatch Logs Insights displays it with an additional `@` appended to the beginning\. For example, if a log field name is `@example.com`, this field name is displayed as `@@example.com`\.


| Log type | Discovered log fields | 
| --- | --- | 
|  Amazon VPC flow logs  |  `@timestamp`, `@logStream`, `@message`, `accountId`, `endTime`, `interfaceId`, `logStatus`, `startTime`, `version`, `action`, `bytes`, `dstAddr`, `dstPort`, `packets`, `protocol`, `srcAddr`, `srcPort`    | 
|  Route 53 logs  |  `@timestamp`, `@logStream`, `@message`, `edgeLocation`, `hostZoneId`, `protocol`, `queryName`, `queryTimestamp`, `queryType`, `resolverIp`, `responseCode`, `version`  | 
|  Lambda logs  |  `@timestamp`, `@logStream`, `@message`, `@requestId`, `@duration, ``@billedDuration`, `@type`, `@maxMemoryUsed`, `@memorySize` If a Lambda log line contains an X\-Ray trace ID, it also includes the following fields: `@xrayTraceId` and `@xraySegmentId`\. CloudWatch Logs Insights automatically discovers log fields in Lambda logs, but only for the first embedded JSON fragment in each log event\. If a Lambda log event contains multiple JSON fragments, you can parse and extract the log fields by using the `parse` command\. For more information, see [Fields in JSON logs](#CWL_AnalyzeLogData-discoverable-JSON-logs)\.  | 
|  CloudTrail logs Logs in JSON format  |  For more information, see [Fields in JSON logs](#CWL_AnalyzeLogData-discoverable-JSON-logs)\.  | 
|  Other log types  |  `@timestamp`, `@ingestionTime`, `@logStream`, `@message`, `@log`\.  | 

## Fields in JSON logs<a name="CWL_AnalyzeLogData-discoverable-JSON-logs"></a>

With CloudWatch Logs Insights, you use dot notation to represent JSON fields\. This section contains an example JSON event and code snippet that show how you can access JSON fields using dot notation\.

**Example: JSON event**

```
{
    "eventVersion": "1.0",
    "userIdentity": {
        "type": "IAMUser",
        "principalId": "EX_PRINCIPAL_ID",
        "arn": "arn: aws: iam: : 123456789012: user/Alice",
        "accessKeyId": "EXAMPLE_KEY_ID",
        "accountId": "123456789012",
        "userName": "Alice"
    },
    "eventTime": "2014-03-06T21: 22: 54Z",
    "eventSource": "ec2.amazonaws.com",
    "eventName": "StartInstances",
    "awsRegion": "us-east-2",
    "sourceIPAddress": "205.251.233.176",
    "userAgent": "ec2-api-tools1.6.12.2",
    "requestParameters": {
        "instancesSet": {
            "items": [
                {
                    "instanceId": "i-abcde123"
                }
            ]
        }
    },
    "responseElements": {
        "instancesSet": {
            "items": [
                {
                    "instanceId": "i-abcde123",
                    "currentState": {
                        "code": 0,
                        "name": "pending"
                    },
                    "previousState": {
                        "code": 80,
                        "name": "stopped"
                    }
                }
            ]
        }
    }
}
```

The example JSON event contains an object that's named `userIdentity`\. `userIdentity` contains a field that's named `type`\. To represent value of `type` using dot notation, you use `userIdentity.type`\.

The example JSON event contains arrays that flatten to lists of nested field names and values\. To represent the value of `instanceId` for the first item in `requestParameters.instancesSet`, you use `requestParameters.instancesSet.items.0.instanceId`\. The number `0` that's placed before the field `instanceID` refers to the position of values for the field `items`\. The following example contains a code snippet that shows how you can access nested JSON fields in a JSON log event\.

**Example: Query**

```
fields @timestamp, @message
| filter requestParameters.instancesSet.items.0.instanceId="i-abcde123"
| sort @timestamp desc
```

The code snippet shows a query that uses dot notation with the `filter` command to access the value of the nested JSON field `instanceId`\. The query filters on messages where the value of `instanceId` equals `"i-abcde123"` and returns all of the log events that contain the specified value\.

**Note**  
CloudWatch Logs Insights can extract a maximum of 1000 log event fields from a JSON log\. For additional fields that aren't extracted, you can use the `parse` command to extract the fields from the raw unparsed log event in the message field\. For more information about the `parse` command, see [Query syntax](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html) in the Amazon CloudWatch User Guide\.