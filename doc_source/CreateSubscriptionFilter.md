# Create a Subscription Filter<a name="CreateSubscriptionFilter"></a>

After you create a destination, the log data recipient account can share the destination ARN \(arn:aws:logs:us\-east\-1:999999999999:destination:testDestination\) with other AWS accounts so that they can send log events to the same destination\. These other sending accounts users then create a subscription filter on their respective log groups against this destination\. The subscription filter immediately starts the flow of real\-time log data from the chosen log group to the specified destination\.

In the following example, a subscription filter is created in a sending account\. the filter is associated with a log group containing AWS CloudTrail events so that every logged activity made by "Root" AWS credentials is delivered to the destination you previously created\. That destination encapsulates a Kinesis stream called "RecipientStream"\. For more information about how to send AWS CloudTrail events to CloudWatch Logs, see [Sending CloudTrail Events to CloudWatch Logs](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/send-cloudtrail-events-to-cloudwatch-logs.html) in the *AWS CloudTrail User Guide*\.

```
aws logs put-subscription-filter \
    --log-group-name "CloudTrail" \
    --filter-name "RecipientStream" \
    --filter-pattern "{$.userIdentity.type = Root}" \
    --destination-arn "arn:aws:logs:region:999999999999:destination:testDestination"
```

The log group and the destination must be in the same AWS region\. However, the destination can point to an AWS resource such as a Kinesis stream that is located in a different region\.