# Step 3: Create a subscription filter<a name="CreateSubscriptionFilterFirehose"></a>

Switch to the sending account, which is 111111111111 in this example\. You will now create the subscription filter in the sending account\. In this example, the filter is associated with a log group containing AWS CloudTrail events so that every logged activity made by "Root" AWS credentials is delivered to the destination you previously created\. For more information about how to send AWS CloudTrail events to CloudWatch Logs, see [Sending CloudTrail Events to CloudWatch Logs](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/send-cloudtrail-events-to-cloudwatch-logs.html) in the *AWS CloudTrail User Guide*\.

```
aws logs put-subscription-filter \
    --log-group-name "aws-cloudtrail-logs-111111111111-300a971e" \                   
    --filter-name "firehose_test" \
    --filter-pattern "{$.userIdentity.type = AssumedRole}" \
    --destination-arn "arn:aws:logs:us-east-1:222222222222:destination:testFirehoseDestination"
```

The log group and the destination must be in the same AWS Region\. However, the destination can point to an AWS resource such as a Kinesis Data Firehose stream that is located in a different Region\.