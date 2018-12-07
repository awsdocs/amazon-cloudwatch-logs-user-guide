# Using CloudWatch Logs with Interface VPC Endpoints<a name="cloudwatch-logs-and-interface-VPC"></a>

If you use Amazon Virtual Private Cloud \(Amazon VPC\) to host your AWS resources, you can establish a private connection between your VPC and CloudWatch Logs\. You can use this connection to send logs to CloudWatch Logs without sending them through the internet\.

Amazon VPC is an AWS service that you can use to launch AWS resources in a virtual network that you define\. With a VPC, you have control over your network settings, such the IP address range, subnets, route tables, and network gateways\. To connect your VPC to CloudWatch Logs, you define an *interface VPC endpoint* for CloudWatch Logs\. This type of endpoint enables you to connect your VPC to AWS services\. The endpoint provides reliable, scalable connectivity to CloudWatch Logs without requiring an internet gateway, network address translation \(NAT\) instance, or VPN connection\. For more information, see [What is Amazon VPC](https://docs.aws.amazon.com/vpc/latest/userguide/) in the *Amazon VPC User Guide*\.

 Interface VPC endpoints are powered by AWS PrivateLink, an AWS technology that enables private communication between AWS services using an elastic network interface with private IP addresses\. For more information, see [New – AWS PrivateLink for AWS Services](https://aws.amazon.com/blogs/aws/new-aws-privatelink-endpoints-kinesis-ec2-systems-manager-and-elb-apis-in-your-vpc/)\.

The following steps are for users of Amazon VPC\. For more information, see [Getting Started](https://docs.aws.amazon.com/vpc/latest/userguide/GetStarted.html) in the *Amazon VPC User Guide*\.

## Availability<a name="cloudwatch-logs-interface-VPC-availability"></a>

CloudWatch Logs currently supports VPC endpoints in the following Regions:
+ US East \(Ohio\)
+ US East \(N\. Virginia\)
+ US West \(N\. California\)
+ US West \(Oregon\)
+ Asia Pacific \(Mumbai\)
+ Asia Pacific \(Seoul\)
+ Asia Pacific \(Singapore\)
+ Asia Pacific \(Sydney\)
+ Asia Pacific \(Tokyo\)
+ Canada \(Central\)
+ EU \(Frankfurt\)
+ EU \(Ireland\)
+ EU \(London\)
+ EU \(Paris\)
+ South America \(São Paulo\)

## Create a VPC Endpoint for CloudWatch Logs<a name="create-VPC-endpoint-for-CloudWatchLogs"></a>

To start using CloudWatch Logs with your VPC, create an interface VPC endpoint for CloudWatch Logs\. The endpoint name will be `com.amazonaws.Region.logs`\. For more information, see [Creating an Interface Endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#create-interface-endpoint.html) in the *Amazon VPC User Guide*\.

You do not need to change the settings for CloudWatch Logs\. CloudWatch Logs calls other AWS services using either public endpoints or private interface VPC endpoints, whichever are in use\. For example, if you create an interface VPC endpoint for CloudWatch Logs, and you already have a CloudWatch Logs subscription filter for Kinesis Data Streams and an interface VPC endpoint for Kinesis Data Streams, calls between CloudWatch Logs and Kinesis Data Streams begin to flow through the interface VPC endpoint\.

## Testing the Connection Between Your VPC and CloudWatch Logs<a name="test-VPC-endpoint-for-CloudWatchLogs"></a>

After you create the endpoint, you can test the connection\.

**To test the connection between your VPC and your CloudWatch Logs endpoint**

1. Connect to an Amazon EC2 instance that resides in your VPC\. For information about connecting, see [Connect to Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpce-interface.html#create-interface-endpoint.html) or [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the Amazon EC2 documentation\.

1. From the instance, use the AWS CLI to create a log entry in one of your existing log groups\.

   First, create a JSON file with a log event\. The timestamp must be specified as the number of milliseconds after Jan 1, 1970 00:00:00 UTC\.

   ```
   [
     {
       "timestamp": 1533854071310,
       "message": "VPC Connection Test"
     }
   ]
   ```

   Then, use the `put-log-events` command to create the log entry:

   ```
   aws logs put-log-events --log-group-name LogGroupName --log-stream-name LogStreamName --log-events file://JSONFileName
   ```

   If the response to the command includes `nextSequenceToken`, the command has succeeded and your VPC endpoint is working\.