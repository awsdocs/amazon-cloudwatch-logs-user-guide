# Use the Previous CloudWatch Logs Agent to Get Started With CloudWatch Logs<a name="UsePreviousCloudWatchLogsAgent"></a>

Using the CloudWatch Logs agent, you can publish log data from Amazon EC2 instances running Linux or Windows Server, and logged events from AWS CloudTrail\. We recommend instead using the CloudWatch unified agent to publish your log data\. For more information about the new agent, see [Collect Metrics and Logs from Amazon EC2 Instances and On\-Premises Servers with the CloudWatch Agent](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html) in the *Amazon CloudWatch User Guide*\. Alternatively, you can continue using the previous CloudWatch Logs agent\.

Contents

## CloudWatch Logs Agent Prerequisites<a name="CWL_Prerequisites"></a>

The CloudWatch Logs agent requires Python version 2\.7, 3\.0, or 3\.3, and any of the following versions of Linux:

+ Amazon Linux version 2014\.03\.02 or later

+ Ubuntu Server version 12\.04, 14\.04, or 16\.04

+ CentOS version 6, 6\.3, 6\.4, 6\.5, or 7\.0

+ Red Hat Enterprise Linux \(RHEL\) version 6\.5 or 7\.0

+ Debian 8\.0