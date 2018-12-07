# Supported Logs and Discovered Fields<a name="CWL_AnalyzeLogData-discoverable-fields"></a>

CloudWatch Logs Insights supports all types of logs\. For every log sent to CloudWatch Logs, three system fields are automatically generated: 
+ `@message` contains the raw unparsed log event\.
+ `@timestamp` contains the time at which the log event was added to the CloudWatch Logs\.
+ `@logStream` contains the name of the log stream to which the log event was added\.

For many log types, CloudWatch Logs also automatically discovers the log fields contained in the logs\. These automatic discovery fields are shown in the following table\.

For other types of logs with fields that CloudWatch Logs Insights does not automatically discover, you can use the `parse` command to extract and create ephemeral fields for use in that query\. For more information, see [CloudWatch Logs Insights Query Syntax](CWL_QuerySyntax.md)

If the name of a discovered log field starts with the `@` character, CloudWatch Logs Insights displays it with an additional `@` appended to the beginning\. For example, if a log field name is `@example.com`, this field name is displayed as `@@example.com`\.


| Log type | Discovered log fields | 
| --- | --- | 
|  Amazon VPC flow logs  |  `@timestamp`, `@logStream`, `@message`, `accountId`, `endTime`, `interfaceId`, `logStatus`, `startTime`, `version`, `action`, `bytes`, `dstAddr`, `dstPort`, `packets`, `protocol`, `srcAddr`, `srcPort`  | 
|  Route 53 logs  |  `@timestamp`, `@logStream`, `@message`, `edgeLocation`, `hostZoneId`, `protocol`, `queryName`, `queryTimestamp`, `queryType`, `resolverIp`, `responseCode`, `version`  | 
|  Lambda logs  |  `@timestamp`, `@logStream`, `@message`, `@requestId`, `@duration, ``@billedDuration`, `@type`, `@maxMemoryUsed`, `@memorySize` CloudWatch Logs Insights automatically discovers log fields in Lambda logs, but only for the first embedded JSON fragment in each log event\. If a Lambda log event contains multiple JSON fragments, you can parse and extract the log fields by using the `parse` command\. For more information, see [Fields in JSON Logs](#CWL_AnalyzeLogData-discoverable-JSON-logs)\.  | 
|  CloudTrail logs Logs in JSON format  |  For more information, see [Fields in JSON Logs](#CWL_AnalyzeLogData-discoverable-JSON-logs)\.  | 
|  Other log types  |  `@timestamp`, `@logStream`, `@message`\.  | 

## Fields in JSON Logs<a name="CWL_AnalyzeLogData-discoverable-JSON-logs"></a>

CloudWatch Logs Insights represents nested JSON fields using the dot notation\. In the following example JSON event, the field `type` in the JSON object `userIdentity` is represented as `userIdentity.type`\. 

JSON arrays are flattened into a list of field names and values\. For example, to specify the value of `instanceId` for the first item in `requestParameters.instancesSet`, use `requestParameters.instancesSet.items.0.instanceId`\.

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