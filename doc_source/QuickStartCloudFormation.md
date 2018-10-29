# Quick Start: Use AWS CloudFormation to Get Started With CloudWatch Logs<a name="QuickStartCloudFormation"></a>

AWS CloudFormation enables you to describe and provision your AWS resources in JSON format\. The advantages of this method include being able to manage a collection of AWS resources as a single unit, and easily replicating your AWS resources across regions\.

When you provision AWS using AWS CloudFormation, you create templates that describe the AWS resources to use\. The following example is a template snippet that creates a log group and a metric filter that counts 404 occurrences and sends this count to the log group\. 

```
"WebServerLogGroup": {
    "Type": "AWS::Logs::LogGroup",
    "Properties": {
        "RetentionInDays": 7
    }
},

"404MetricFilter": {
    "Type": "AWS::Logs::MetricFilter",
    "Properties": {
        "LogGroupName": {
            "Ref": "WebServerLogGroup"
        },
        "FilterPattern": "[ip, identity, user_id, timestamp, request, status_code = 404, size, ...]",
        "MetricTransformations": [
            {
                "MetricValue": "1",
                "MetricNamespace": "test/404s",
                "MetricName": "test404Count"
            }
        ]
    }
}
```

This is a basic example\. You can set up much richer CloudWatch Logs deployments using AWS CloudFormation\. For more information about template examples, see [Amazon CloudWatch Logs Template Snippets](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/quickref-cloudwatchlogs.html) in the *AWS CloudFormation User Guide*\. For more information about getting started, see [Getting Started with AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/GettingStarted.html) in the *AWS CloudFormation User Guide*\.