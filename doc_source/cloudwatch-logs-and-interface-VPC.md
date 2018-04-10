# Using CloudWatch Logs with Interface VPC Endpoints<a name="cloudwatch-logs-and-interface-VPC"></a>

You can use an interface VPC endpoint to keep traffic between your Amazon VPC and CloudWatch Logs from leaving the Amazon network\. Interface VPC endpoints don't require an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Interface VPC endpoints are powered by AWS PrivateLink, an AWS technology that enables private communication between AWS services using an elastic network interface with private IP addresses in your Amazon VPC\. For more information about Amazon VPC, see [What is Amazon VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/) in the *Amazon VPC User Guide*\. For more information about AWS PrivateLink, see [New – AWS PrivateLink for AWS Services](https://aws.amazon.com/blogs/aws/new-aws-privatelink-endpoints-kinesis-ec2-systems-manager-and-elb-apis-in-your-vpc/)\.

To get started, create an interface VPC endpoint\. You do not need to change the settings for CloudWatch Logs\. CloudWatch Logs calls other AWS services using either public endpoints or private interface VPC endpoints, whichever are in use\. For example, if you create an interface VPC endpoint for CloudWatch Logs, and you already have a CloudWatch Logs subscription filter for Kinesis Data Streams and an interface VPC endpoint for Kinesis Data Streams, calls between CloudWatch Logs and Kinesis Data Streams begin to flow through the interface VPC endpoint\. For more information, see [ New – AWS PrivateLink for AWS Services](https://aws.amazon.com/blogs/aws/new-aws-privatelink-endpoints-kinesis-ec2-systems-manager-and-elb-apis-in-your-vpc/)\.

## Availability<a name="cloudwatch-logs-interface-VPC-availability"></a>

CloudWatch Logs currently supports VPC endpoints in the following Regions:

+ United States \(Oregon\);

+ Asia Pacific \(Tokyo\);

+ South America \(São Paulo\)
