# Quick Start: Install and Configure the CloudWatch Logs Agent on a Running EC2 Linux Instance<a name="QuickStartEC2Instance"></a>

**Tip**  
CloudWatch includes a new unified agent that can collect both logs and metrics from EC2 instances and on\-premises servers\. If you are not already using the older CloudWatch Logs agent, we recommend that you use the newer unified CloudWatch agent\. For more information, see [Getting Started with CloudWatch Logs](CWL_GettingStarted.md)\.   
The rest of this section explains the use of the older CloudWatch Logs agent\.

## Configure the Older CloudWatch Logs Agent on a Running EC2 Linux Instance<a name="QuickStartEC2Instance-oldagent"></a>

You can use the CloudWatch Logs agent installer on an existing EC2 instance to install and configure the CloudWatch Logs agent\. After installation is complete, logs automatically flow from the instance to the log stream you create while installing the agent\. The agent confirms that it has started and it stays running until you disable it\.

In addition to using the agent, you can also publish log data using the AWS CLI, CloudWatch Logs SDK, or the CloudWatch Logs API\. The AWS CLI is best suited for publishing data at the command line or through scripts\. The CloudWatch Logs SDK is best suited for publishing log data directly from applications or building your own log publishing application\.

### Step 1: Configure Your IAM Role or User for CloudWatch Logs<a name="running-ec2-step-1"></a>

The CloudWatch Logs agent supports IAM roles and users\. If your instance already has an IAM role associated with it, make sure that you include the IAM policy below\. If you don't already have an IAM role assigned to your instance, you can use your IAM credentials for the next steps or you can assign an IAM role to that instance\. For more information, see [Attaching an IAM Role to an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html#attach-iam-role)\.<a name="cloudwatchlogs_iam_role_procedure"></a>

**To configure your IAM role or user for CloudWatch Logs**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\.

1. Choose the role by selecting the role name \(do not select the check box next to the name\)\.

1. Choose **Attach Policies**, **Create Policy**\.

   A new browser tab or window opens\.

1. Choose the **JSON** tab and type the following JSON policy document\.

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
           "*"
       ]
     }
    ]
   }
   ```

1. When you are finished, choose **Review policy**\. The Policy Validator reports any syntax errors\.

1. On the **Review Policy** page, type a **Name** and a **Description** \(optional\) for the policy that you are creating\. Review the policy **Summary** to see the permissions that are granted by your policy\. Then choose **Create policy** to save your work\.

1. Close the browser tab or window, and return to the **Add permissions** page for your role\. Choose **Refresh**, and then choose the new policy to attach it to your role\.

1. Choose **Attach Policy**\.

### Step 2: Install and Configure CloudWatch Logs on an Existing Amazon EC2 Instance<a name="running-ec2-step-2"></a>

The process for installing the CloudWatch Logs agent differs depending on whether your Amazon EC2 instance is running Amazon Linux, Ubuntu, CentOS, or Red Hat\. Use the steps appropriate for the version of Linux on your instance\.

**To install and configure CloudWatch Logs on an existing Amazon Linux instance**

Starting with Amazon Linux AMI 2014\.09, the CloudWatch Logs agent is available as an RPM installation with the awslogs package\. Earlier versions of Amazon Linux can access the awslogs package by updating their instance with the `sudo yum update -y` command\. By installing the awslogs package as an RPM instead of the using the CloudWatch Logs installer, your instance receives regular package updates and patches from AWS without having to manually reinstall the CloudWatch Logs agent\.
**Warning**  
Do not update the CloudWatch Logs agent using the RPM installation method if you previously used the Python script to install the agent\. Doing so may cause configuration issues that prevent the CloudWatch Logs agent from sending your logs to CloudWatch\.

1. Connect to your Amazon Linux instance\. For more information, see [Connect to Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-connect-to-instance-linux.html) in the *Amazon EC2 User Guide for Linux Instances*\.

   For more information about connection issues, see [Troubleshooting Connecting to Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Update your Amazon Linux instance to pick up the latest changes in the package repositories\.

   ```
   sudo yum update -y
   ```

1. Install the `awslogs` package\. This is the recommended method for installing awslogs on Amazon Linux instances\.

   ```
   sudo yum install -y awslogs
   ```

1. Edit the `/etc/awslogs/awslogs.conf` file to configure the logs to track\. For more information about editing this file, see [CloudWatch Logs Agent Reference](AgentReference.md)\.

1. By default, the `/etc/awslogs/awscli.conf` points to the us\-east\-1 region\. To push your logs to a different region, edit the `awscli.conf` file and specify that region\.

1. Start the `awslogs` service\.

   ```
   sudo service awslogs start
   ```

   If you are running Amazon Linux 2, start the `awslogs` service with the following command\.

   ```
   sudo systemctl start awslogsd
   ```

1. \(Optional\) Check the `/var/log/awslogs.log` file for errors logged when starting the service\.

1. \(Optional\) Run the following command to start the `awslogs` service at each system boot\.

   ```
   sudo chkconfig awslogs on
   ```

   If you are running Amazon Linux 2, use the following command to start the service at each system boot\.

   ```
   sudo systemctl enable awslogsd.service
   ```

1. You should see the newly created log group and log stream in the CloudWatch console after the agent has been running for a few moments\.

   For more information, see [View Log Data Sent to CloudWatch Logs](Working-with-log-groups-and-streams.md#ViewingLogData)\.

**To install and configure CloudWatch Logs on an existing Ubuntu Server, CentOS, or Red Hat instance**

If you're using an AMI running Ubuntu Server, CentOS, or Red Hat, use the following procedure to manually install the CloudWatch Logs agent on your instance\.

1. Connect to your EC2 instance\. For more information, see [Connect to Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-connect-to-instance-linux.html) in the *Amazon EC2 User Guide for Linux Instances*\.

   For more information about connection issues, see [Troubleshooting Connecting to Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Run the CloudWatch Logs agent installer using one of two options\. You can run it directly from the internet, or download the files and run it standalone\.
**Note**  
If you are running CentOS 6\.x, Red Hat 6\.x, or Ubuntu 12\.04, use the steps for downloading and running the installer standalone\. Installing the CloudWatch Logs agent directly from the internet is not supported on these systems\.
**Note**  
On Ubuntu, run `apt-get update` before running the commands below\.

   To run it directly from the internet, use the following commands and follow the prompts:

   ```
   curl https://s3.amazonaws.com/aws-cloudwatch/downloads/latest/awslogs-agent-setup.py -O
   ```

   ```
   sudo python ./awslogs-agent-setup.py --region us-east-1
   ```

   If the preceding command does not work, try the following:

   ```
   sudo python3 ./awslogs-agent-setup.py --region us-east-1
   ```

   To download and run it standalone, use the following commands and follow the prompts:

   ```
   curl https://s3.amazonaws.com/aws-cloudwatch/downloads/latest/awslogs-agent-setup.py -O
   ```

   ```
   curl https://s3.amazonaws.com/aws-cloudwatch/downloads/latest/AgentDependencies.tar.gz -O
   ```

   ```
   tar xvf AgentDependencies.tar.gz -C /tmp/
   ```

   ```
   sudo python ./awslogs-agent-setup.py --region us-east-1 --dependency-path /tmp/AgentDependencies
   ```

   You can install the CloudWatch Logs agent by specifying the us\-east\-1, us\-west\-1, us\-west\-2, ap\-south\-1, ap\-northeast\-2, ap\-southeast\-1, ap\-southeast\-2, ap\-northeast\-1, eu\-central\-1, eu\-west\-1, or sa\-east\-1 regions\.
**Note**  
For more information about the current version and the version history of `awslogs-agent-setup`, see [CHANGELOG\.txt](https://s3.amazonaws.com/aws-cloudwatch/downloads/latest/CHANGELOG.txt)\.

   The CloudWatch Logs agent installer requires certain information during setup\. Before you start, you need to know which log file to monitor and its time stamp format\. You should also have the following information ready\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/QuickStartEC2Instance.html)

   After you have completed these steps, the installer asks about configuring another log file\. You can run the process as many times as you like for each log file\. If you have no more log files to monitor, choose **N** when prompted by the installer to set up another log\. For more information about the settings in the agent configuration file, see [CloudWatch Logs Agent Reference](AgentReference.md)\.
**Note**  
Configuring multiple log sources to send data to a single log stream is not supported\.

1. You should see the newly created log group and log stream in the CloudWatch console after the agent has been running for a few moments\.

   For more information, see [View Log Data Sent to CloudWatch Logs](Working-with-log-groups-and-streams.md#ViewingLogData)\.