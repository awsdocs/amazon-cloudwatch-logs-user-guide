# Quick Start: Install and Configure the CloudWatch Logs Agent on an EC2 Linux Instance at Launch<a name="EC2NewInstanceCWL"></a>

**Tip**  
The older CloudWatch Logs agent discussed in this section is on the path to deprecation\. We strongly recommend that you instead use the new unified CloudWatch agent that can collect both logs and metrics\. Additionally, the older CloudWatch Logs agent requires Python 3\.3 or earlier, and these versions are not installed on new EC2 instances by default\. For more information about the unified CloudWatch agent, see [Installing the CloudWatch Agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-EC2-Instance.html)\.   
The rest of this section explains the use of the older CloudWatch Logs agent\.

## Installing the older CloudWatch Logs Agent on an EC2 Linux Instance at Launch<a name="EC2NewInstanceCWL-oldagent"></a>

You can use Amazon EC2 user data, a feature of Amazon EC2 that allows parametric information to be passed to the instance on launch, to install and configure the CloudWatch Logs agent on that instance\. To pass the CloudWatch Logs agent installation and configuration information to Amazon EC2, you can provide the configuration file in a network location such as an Amazon S3 bucket\.

Configuring multiple log sources to send data to a single log stream is not supported\.

**Prerequisite**  
Create an agent configuration file that describes all your log groups and log streams\. This is a text file that describes the log files to monitor as well as the log groups and log streams to upload them to\. The agent consumes this configuration file and starts monitoring and uploading all the log files described in it\. For more information about the settings in the agent configuration file, see [CloudWatch Logs Agent Reference](AgentReference.md)\.

The following is a sample agent configuration file for Amazon Linux

```
[general]
state_file = /var/awslogs/state/agent-state  
 
[/var/log/messages]
file = /var/log/messages
log_group_name = /var/log/messages
log_stream_name = {instance_id}
datetime_format = %b %d %H:%M:%S
```

The following is a sample agent configuration file for Ubuntu

```
[general]
state_file = /var/awslogs/state/agent-state
 
[/var/log/syslog]
file = /var/log/syslog
log_group_name = /var/log/syslog
log_stream_name = {instance_id}
datetime_format = %b %d %H:%M:%S
```

**To configure your IAM role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**, **Create Policy**\.

1. On the **Create Policy** page, for **Create Your Own Policy**, choose **Select**\. For more information about creating custom policies, see [IAM Policies for Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-policies-for-amazon-ec2.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. On the **Review Policy** page, for **Policy Name**, type a name for the policy\.

1. For **Policy Document**, paste in the following policy:

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "logs:CreateLogGroup",
                   "logs:CreateLogStream",
                   "logs:PutLogEvents",
                   "logs:DescribeLogStreams"
               ],
               "Resource": [
                   "arn:aws:logs:*:*:*"
               ]
           },
           {
               "Effect": "Allow",
               "Action": [
                   "s3:GetObject"
               ],
               "Resource": [
                   "arn:aws:s3:::myawsbucket/*"
               ]
           }
       ]
   }
   ```

1. Choose **Create Policy**\.

1. In the navigation pane, choose **Roles**, **Create New Role**\.

1. On the **Set Role Name** page, type a name for the role and then choose **Next Step**\.

1. On the **Select Role Type** page, choose **Select** next to **Amazon EC2**\.

1. On the **Attach Policy** page, in the table header, choose **Policy Type**, **Customer Managed**\.

1. Select the IAM policy that you created and then choose **Next Step**\.

1. Choose **Create Role**\.

   For more information about IAM users and policies, see [IAM Users and Groups](https://docs.aws.amazon.com/IAM/latest/UserGuide/Using_WorkingWithGroupsAndUsers.html) and [Managing IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/ManagingPolicies.html) in the *IAM User Guide*\.

**To launch a new instance and enable CloudWatch Logs**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

   For more information, see [Launching an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html) in *Amazon EC2 User Guide for Linux Instances*\.

1. On the **Step 1: Choose an Amazon Machine Image \(AMI\)** page, select the Linux instance type to launch, and then on the **Step 2: Choose an Instance Type** page, choose **Next: Configure Instance Details**\.

   Make sure that [cloud\-init](http://cloudinit.readthedocs.org/en/latest/index.html) is included in your Amazon Machine Image \(AMI\)\. Amazon Linux AMIs, and AMIs for Ubuntu and RHEL already include cloud\-init, but CentOS and other AMIs in the AWS Marketplace might not\.

1. On the **Step 3: Configure Instance Details** page, for **IAM role**, select the IAM role that you created\.

1. Under **Advanced Details**, for **User data**, paste the following script into the box\. Then update that script by changing the value of the **\-c** option to the location of your agent configuration file:

   ```
   #!/bin/bash
   curl https://s3.amazonaws.com/aws-cloudwatch/downloads/latest/awslogs-agent-setup.py -O
   chmod +x ./awslogs-agent-setup.py
   ./awslogs-agent-setup.py -n -r us-east-1 -c s3://AWSDOC-EXAMPLE-BUCKET1/my-config-file
   ```

1. Make any other changes to the instance, review your launch settings, and then choose **Launch**\.

1. You should see the newly created log group and log stream in the CloudWatch console after the agent has been running for a few moments\.

   For more information, see [View Log Data Sent to CloudWatch Logs](Working-with-log-groups-and-streams.md#ViewingLogData)\.