# Getting Started with CloudWatch Logs<a name="CWL_GettingStarted"></a>

To collect logs from your Amazon EC2 instances and on\-premises servers into CloudWatch Logs, AWS offers both a new unified CloudWatch agent, and an older CloudWatch Logs agent\. We recommend the unified CloudWatch agent\. The new unified agent has the following advantages\.
+ You can collect both logs and advanced metrics with the installation and configuration of just one agent\.
+ The unified agent enables the collection of logs from servers running Windows Server\.
+ If you are using the agent to collect CloudWatch metrics, the unified agent also enables the collection of additional system metrics, for in\-guest visibility\.
+ The unified agent provides better performance\.

The older CloudWatch Logs agent is still supported\. If you are already using that agent, you may continue to do so\. If you would like to migrate from the CloudWatch Logs agent to the new unified CloudWatch agent, we also provide a migration path\.

**Topics**
+ [Use the Unified CloudWatch Agent to Get Started With CloudWatch Logs](UseCloudWatchUnifiedAgent.md)
+ [Use the Previous CloudWatch Logs Agent to Get Started With CloudWatch Logs](UsePreviousCloudWatchLogsAgent.md)
+ [Quick Start: Use AWS CloudFormation to Get Started With CloudWatch Logs](QuickStartCloudFormation.md)

**Differences between the two agents**

The two agents provide similar functionality for CloudWatch Logs, with the unified CloudWatch agent adding the ability to collect logs from servers running Windows Server\. The other difference is the symbols supported for log timestamp format, as shown in the following table\.


| Symbols Supported by Both Agents | Symbols Supported Only by Unified CloudWatch Agent | Symbols Supported Only by Older CloudWatch Logs Agent | 
| --- | --- | --- | 
|  %A, %a, %b, %B, %d, %H, %l, %m, %M, %p, %S, %y, %Y, %Z, %z  |  %\-d, %\-l, %\-m, %\-M, %\-S  |  %c, %f, %j, %U, %W, %w  | 

For more information about the meanings of the symbols supported by the new CloudWatch agent, see [ CloudWatch Agent Configuration File: Logs Section](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-Configuration-File-Details.html#CloudWatch-Agent-Configuration-File-Logssection) in the *Amazon CloudWatch User Guide*\. For information about symbols supported by the CloudWatch Logs agent, see [Agent Configuration File](AgentReference.md#agent-configuration-file)\.