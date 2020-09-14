# Supported Logs and Discovered Fields<a name="CWL_AnalyzeLogData-discoverable-fields"></a>

CloudWatch Logs Insights supports all types of logs\. For every log sent to CloudWatch Logs, five system fields are automatically generated: 
+ `@message` contains the raw unparsed log event\. This is equivalent to the `message` field in [InputLogevent](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_InputLogEvent.html)\.
+ `@timestamp` contains the event timestamp contained in the log event's `timestamp` field\. This is equivalent to the `timestamp` field in [InputLogevent](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_InputLogEvent.html)\.
+ `@ingestionTime` contains the time when the log event was received by CloudWatch Logs\.
+ `@logStream` contains the name of the log stream that the log event was added to\. Log streams are used to group logs by the same process that generated them\.
+ `@log` is a log group identifier in the form of `account-id:log-group-name`\. This can be useful in queries of multiple log groups, to identify which log group a particular event belongs to\.

CloudWatch Logs Insights inserts the **@** symbol at the start of fields that it generates\.

For many log types, CloudWatch Logs also automatically discovers the log fields contained in the logs\. These automatic discovery fields are shown in the following table\.

For other types of logs with fields that CloudWatch Logs Insights doesn't automatically discover, you can use the `parse` command to extract and create ephemeral fields for use in that query\. For more information, see [CloudWatch Logs Insights Query Syntax](CWL_QuerySyntax.md)

If the name of a discovered log field starts with the `@` character, CloudWatch Logs Insights displays it with an additional `@` appended to the beginning\. For example, if a log field name is `@example.com`, this field name is displayed as `@@example.com`\.


| Log Type | Discovered Log Fields | 
| --- | --- | 
|  Amazon VPC flow logs  |  `@timestamp`, `@logStream`, `@message`, `accountId`, `endTime`, `interfaceId`, `logStatus`, `startTime`, `version`, `action`, `bytes`, `dstAddr`, `dstPort`, `packets`, `protocol`, `srcAddr`, `srcPort`  | 
|  Route 53 logs  |  `@timestamp`, `@logStream`, `@message`, `edgeLocation`, `hostZoneId`, `protocol`, `queryName`, `queryTimestamp`, `queryType`, `resolverIp`, `responseCode`, `version`  | 
|  Lambda logs  |  `@timestamp`, `@logStream`, `@message`, `@requestId`, `@duration, ``@billedDuration`, `@type`, `@maxMemoryUsed`, `@memorySize` If a Lambda log line contains an X\-Ray trace ID, it also includes the following fields: `@xrayTraceId` and `@xraySegmentId`\. CloudWatch Logs Insights automatically discovers log fields in Lambda logs, but only for the first embedded JSON fragment in each log event\. If a Lambda log event contains multiple JSON fragments, you can parse and extract the log fields by using the `parse` command\. For more information, see [Fields in JSON Logs](#CWL_AnalyzeLogData-discoverable-JSON-logs)\.  | 
|  CloudTrail logs Logs in JSON format  |  For more information, see [Fields in JSON Logs](#CWL_AnalyzeLogData-discoverable-JSON-logs)\.  | 
|  Other log types  |  `@timestamp`, `@ingestionTime`, `@logStream`, `@message`, `@log`\.  | 

## Fields in JSON Logs<a name="CWL_AnalyzeLogData-discoverable-JSON-logs"></a>

CloudWatch Logs Insights represents nested JSON fields using the dot notation\. In the following example JSON event, the field `type` in the JSON object `userIdentity` is represented as `userIdentity.type`\. 

JSON arrays are flattened into a list of field names and values\. For example, to specify the value of `instanceId` for the first item in `requestParameters.instancesSet`, use `requestParameters.instancesSet.items.0.instanceId`\.

CloudWatch Logs Insights can extract a maximum of 100 log event fields from a JSON log\. For extra fields that are not extracted, you can use the **parse** command to parse these fields from the raw unparsed log event in the message field\.

```
{ "eventVersion": "1.0",
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
```