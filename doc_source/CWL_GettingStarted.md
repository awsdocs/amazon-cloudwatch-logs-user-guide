# Getting Started with CloudWatch Logs<a name="CWL_GettingStarted"></a>

To collect logs from your Amazon EC2 instances and on\-premises servers into CloudWatch Logs, AWS offers two options:
+ **Recommended** – The unified CloudWatch agent\. It enables you to collect both logs and advanced metrics with one agent\. It offers support across operating systems, including servers running Windows Server\. This agent also provides better performance\.

  If you're using the unified agent to collect CloudWatch metrics, it enables the collection of additional system metrics, for in\-guest visibility\. It also supports collecting custom metrics using `StatsD` or `collectd`\.

  For more information, see [Installing the CloudWatch Agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-EC2-Instance.html) in the *Amazon CloudWatch User Guide*\.
+ **Supported, but on the path to deprecation** – The older CloudWatch Logs agent, which supports the collection of logs from only servers running Linux\. If you're already using that agent, you may continue to do so\. However, the older agent requires Python 2\.7, 3\.0, and 3\.3\. Because current EC2 instances do not use those versions of Python and those versions are deprecated and are no longer being patched, **we strongly recommend that you migrate to the unified CloudWatch agent\.**

  When you migrate from the CloudWatch Logs agent to the unified CloudWatch agent, the unified agent's configuration wizard can read your current CloudWatch Logs agent configuration file and set up the new agent to collect the same logs\. For more information about the wizard, see [ Create the CloudWatch Agent Configuration File with the Wizard](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/create-cloudwatch-agent-configuration-file-wizard.html) in the *Amazon CloudWatch User Guide*\.

**Topics**
+ [Use the Unified CloudWatch Agent to Get Started With CloudWatch Logs](UseCloudWatchUnifiedAgent.md)
+ [Use the Previous CloudWatch Logs Agent to Get Started With CloudWatch Logs](UsePreviousCloudWatchLogsAgent.md)
+ [Quick Start: Use AWS CloudFormation to Get Started With CloudWatch Logs](QuickStartCloudFormation.md)