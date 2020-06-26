# Quick Start: Install the CloudWatch Logs Agent Using AWS OpsWorks and Chef<a name="QuickStartChef"></a>

You can install the CloudWatch Logs agent and create log streams using AWS OpsWorks and Chef, which is a third\-party systems and cloud infrastructure automation tool\. Chef uses "recipes," which you write to install and configure software on your computer, and "cookbooks," which are collections of recipes, to perform its configuration and policy distribution tasks\. For more information, see [Chef](http://www.getchef.com/chef/)\.

The Chef recipes examples below show how to monitor one log file on each EC2 instance\. The recipes use the stack name as the log group and the instance's hostname as the log stream name\. To monitor multiple log files, you need to extend the recipes to create multiple log groups and log streams\.

## Step 1: Create Custom Recipes<a name="opsworks-step-1"></a>

Create a repository to store your recipes\. AWS OpsWorks supports Git and Subversion, or you can store an archive in Amazon S3\. The structure of your cookbook repository is described in [Cookbook Repositories](https://docs.aws.amazon.com/opsworks/latest/userguide/workingcookbook-installingcustom-repo.html) in the *AWS OpsWorks User Guide*\. The examples below assume that the cookbook is named `logs`\. The install\.rb recipe installs the CloudWatch Logs agent\. You can also download the cookbook example \([CloudWatchLogs\-Cookbooks\.zip](https://s3.amazonaws.com/aws-cloudwatch/downloads/CloudWatchLogs-Cookbooks.zip)\)\.

Create a file named metadata\.rb that contains the following code:

```
#metadata.rb

name             'logs'
version          '0.0.1'
```

Create the CloudWatch Logs configuration file:

```
#config.rb

template "/tmp/cwlogs.cfg" do
  cookbook "logs"
  source "cwlogs.cfg.erb"
  owner "root"
  group "root"
  mode 0644
end
```

Download and install the CloudWatch Logs agent:

```
# install.rb
 
directory "/opt/aws/cloudwatch" do
  recursive true
end

remote_file "/opt/aws/cloudwatch/awslogs-agent-setup.py" do
  source "https://s3.amazonaws.com/aws-cloudwatch/downloads/latest/awslogs-agent-setup.py"
  mode "0755"
end
 
  execute "Install CloudWatch Logs agent" do
  command "/opt/aws/cloudwatch/awslogs-agent-setup.py -n -r region -c /tmp/cwlogs.cfg"
  not_if { system "pgrep -f aws-logs-agent-setup" }
end
```

**Note**  
In the above example, replace *region* with one of the following: us\-east\-1, us\-west\-1, us\-west\-2, ap\-south\-1, ap\-northeast\-2, ap\-southeast\-1, ap\-southeast\-2, ap\-northeast\-1, eu\-central\-1, eu\-west\-1, or sa\-east\-1\.

If the installation of the agent fails, check to make sure that the `python-dev` package is installed\. If it isn't, use the following command, and then retry the agent installation:

```
sudo apt-get -y install python-dev
```

This recipe uses a cwlogs\.cfg\.erb template file that you can modify to specify various attributes such as what files to log\. For more information about these attributes, see [CloudWatch Logs Agent Reference](AgentReference.md)\.

```
[general]
# Path to the AWSLogs agent's state file. Agent uses this file to maintain
# client side state across its executions.
state_file = /var/awslogs/state/agent-state


## Each log file is defined in its own section. The section name doesn't
## matter as long as its unique within this file.
#
#[kern.log]
#
## Path of log file for the agent to monitor and upload.
#
#file = /var/log/kern.log
#
## Name of the destination log group.
#
#log_group_name = kern.log
#
## Name of the destination log stream.
#
#log_stream_name = {instance_id}
#
## Format specifier for timestamp parsing.
#
#datetime_format = %b %d %H:%M:%S
#
#

[<%= node[:opsworks][:stack][:name] %>]
datetime_format = [%Y-%m-%d %H:%M:%S]
log_group_name = <%= node[:opsworks][:stack][:name].gsub(' ','_') %>
file = <%= node[:cwlogs][:logfile] %>
log_stream_name = <%= node[:opsworks][:instance][:hostname] %>
```

The template gets the stack name and host name by referencing the corresponding attributes in the stack configuration and deployment JSON\. The attribute that specifies the file to log is defined in the cwlogs cookbook's default\.rb attributes file \(logs/attributes/default\.rb\)\.

```
default[:cwlogs][:logfile] = '/var/log/aws/opsworks/opsworks-agent.statistics.log'
```

## Step 2: Create an AWS OpsWorks Stack<a name="opsworks-step-2"></a>

1. Open the AWS OpsWorks console at [https://console\.aws\.amazon\.com/opsworks/](https://console.aws.amazon.com/opsworks/)\.

1. On the **OpsWorks Dashboard**, choose **Add stack** to create an AWS OpsWorks stack\.

1. On the **Add stack** screen, choose **Chef 11 stack**\.

1. For **Stack name**, enter a name\.

1. For **Use custom Chef Cookbooks**, choose **Yes**\.

1. For **Repository type**, select the repository type that you use\. If you're using the above example, choose **Http Archive**\.

1. For **Repository URL**, enter the repository where you stored the cookbook that you created in the previous step\. If you're using the above example, enter **https://s3\.amazonaws\.com/aws\-cloudwatch/downloads/CloudWatchLogs\-Cookbooks\.zip**\.

1. Choose **Add Stack** to create the stack\.

## Step 3: Extend Your IAM Role<a name="opsworks-step-3"></a>

To use CloudWatch Logs with your AWS OpsWorks instances, you need to extend the IAM role used by your instances\.

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**, **Create Policy**\.

1. On the **Create Policy** page, under **Create Your Own Policy**, choose **Select**\. For more information about creating custom policies, see [IAM Policies for Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-policies-for-amazon-ec2.html) in the *Amazon EC2 User Guide for Linux Instances*\.

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
     }
    ]
   }
   ```

1. Choose **Create Policy**\.

1. In the navigation pane, choose **Roles**, and then in the contents pane, for **Role Name**, select the name of the instance role used by your AWS OpsWorks stack\. You can find the one used by your stack in the stack settings \(the default is `aws-opsworks-ec2-role`\)\.
**Note**  
Choose the role name, not the check box\.

1. On the **Permissions** tab, under **Managed Policies**, choose **Attach Policy**\.

1. On the **Attach Policy** page, in the table header \(next to **Filter** and **Search**\), choose **Policy Type**, **Customer Managed Policies**\.

1. For **Customer Managed Policies**, select the IAM policy that you created above and choose **Attach Policy**\.

   For more information about IAM users and policies, see [IAM Users and Groups](https://docs.aws.amazon.com/IAM/latest/UserGuide/Using_WorkingWithGroupsAndUsers.html) and [Managing IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/ManagingPolicies.html) in the *IAM User Guide*\.

## Step 4: Add a Layer<a name="opsworks-step-4"></a>

1. Open the AWS OpsWorks console at [https://console\.aws\.amazon\.com/opsworks/](https://console.aws.amazon.com/opsworks/)\.

1. In the navigation pane, choose **Layers**\.

1. In the contents pane, select a layer and choose **Add layer**\.

1. On the **OpsWorks** tab, for **Layer type**, choose **Custom**\.

1. For the **Name** and **Short name** fields, enter the long and short name for the layer, and then choose **Add layer**\.

1. On the **Recipes** tab, under **Custom Chef Recipes**, there are several headings—*Setup*, *Configure*, *Deploy*, *Undeploy*, and *Shutdown*—that correspond to AWS OpsWorks lifecycle events\. AWS OpsWorks triggers these events at these key points in instance's lifecycle, which runs the associated recipes\.
**Note**  
If the above headings aren't visible, under **Custom Chef Recipes**, choose **edit**\.

1. Enter *logs::config, logs::install* next to **Setup**, choose **\+** to add it to the list, and then choose **Save**\.

   AWS OpsWorks runs this recipe on each of the new instances in this layer, right after the instance boots\.

## Step 5: Add an Instance<a name="opsworks-step-5"></a>

The layer only controls how to configure instances\. You now need to add some instances to the layer and start them\.

1. Open the AWS OpsWorks console at [https://console\.aws\.amazon\.com/opsworks/](https://console.aws.amazon.com/opsworks/)\.

1. In the navigation pane, choose **Instances** and then under your layer, choose **\+ Instance**\.

1. Accept the default settings and choose **Add Instance** to add the instance to the layer\.

1. In the row's **Actions** column, click **start** to start the instance\.

   AWS OpsWorks launches a new EC2 instance and configures CloudWatch Logs\. The instance's status changes to online when it's ready\.

## Step 6: View Your Logs<a name="opsworks-step-6"></a>

You should see the newly created log group and log stream in the CloudWatch console after the agent has been running for a few moments\.

For more information, see [View Log Data Sent to CloudWatch Logs](Working-with-log-groups-and-streams.md#ViewingLogData)\.