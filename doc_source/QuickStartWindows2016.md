# Quick Start: Enable Your Amazon EC2 Instances Running Windows Server 2016 to Send Logs to CloudWatch Logs Using the CloudWatch Logs Agent<a name="QuickStartWindows2016"></a>

**Tip**  
CloudWatch includes a new unified agent that can collect both logs and metrics from EC2 instances and on\-premises servers\. We recommend that you use the newer unified CloudWatch agent\. For more information, see [Getting Started with CloudWatch Logs](CWL_GettingStarted.md)\.   
The rest of this section explains the use of the older CloudWatch Logs agent\.

## Enable Your Amazon EC2 Instances Running Windows Server 2016 to Send Logs to CloudWatch Logs Using the older CloudWatch Logs Agent<a name="QuickStartWindows2016-olderagent"></a>

There are multiple methods you can use to enable instances running Windows Server 2016 to send logs to CloudWatch Logs\. The steps in this section use Systems Manager Run Command\. For information about the other possible methods, see [Sending Logs, Events, and Performance Counters to Amazon CloudWatch](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/send_logs_to_cwl.html)\.

**Topics**
+ [Download the Sample Configuration File](#configure_cwl_download)
+ [Configure the JSON File for CloudWatch](#send_logs_to_cwl_json)
+ [Create an IAM User and Role for Systems Manager](#iam_permissions)
+ [Verify Systems Manager Prerequisites](#send_logs_cwl_syspre)
+ [Verify Internet Access](#send_logs_cwl_internet)
+ [Enable CloudWatch Logs Using Systems Manager Run Command](#remote-commands-cloudwatch)

### Download the Sample Configuration File<a name="configure_cwl_download"></a>

Download the following sample file to your computer: [AWS\.EC2\.Windows\.CloudWatch\.json](https://s3.amazonaws.com/ec2-downloads-windows/CloudWatchConfig/AWS.EC2.Windows.CloudWatch.json)\.

### Configure the JSON File for CloudWatch<a name="send_logs_to_cwl_json"></a>

You determine which logs to send to CloudWatch by specifying your choices in a configuration file\. The process of creating this file and specifying your choices can take 30 minutes or more to complete\. After you have completed this task once, you can reuse the configuration file on all of your instances\.

**Topics**
+ [Step 1: Enable CloudWatch Logs](#enable-CloudWatchLogs-in-JSON-file)
+ [Step 2: Configure Settings for CloudWatch](#configure_cwl_credentials)
+ [Step 3: Configure the Data to Send](#configure_logs)
+ [Step 4: Configure Flow Control](#configure_log_flow)
+ [Step 5: Save JSON Content](#save_json_content)

#### Step 1: Enable CloudWatch Logs<a name="enable-CloudWatchLogs-in-JSON-file"></a>

At the top of the JSON file, change "false" to "true" for `IsEnabled`:

```
"IsEnabled": true,
```

#### Step 2: Configure Settings for CloudWatch<a name="configure_cwl_credentials"></a>

Specify credentials, region, a log group name, and a log stream namespace\. This enables the instance to send log data to CloudWatch Logs\. To send the same log data to different locations, you can add additional sections with unique IDs \(for example, "CloudWatchLogs2" and CloudWatchLogs3"\) and a different region for each ID\.

**To configure settings to send log data to CloudWatch Logs**

1. In the JSON file, locate the `CloudWatchLogs` section\.

   ```
   {
       "Id": "CloudWatchLogs",
       "FullName": "AWS.EC2.Windows.CloudWatch.CloudWatchLogsOutput,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "AccessKey": "",
           "SecretKey": "",
           "Region": "us-east-1",
           "LogGroup": "Default-Log-Group",
           "LogStream": "{instance_id}"
       }
   },
   ```

1.  Leave the `AccessKey` and `SecretKey` field blank\. You configure credentials using an IAM role\.

1. For `Region`, type the region to which to send log data \(for example, `us-east-2`\)\.

1. For `LogGroup`, type the name for your log group\. This name appears on the **Log Groups** screen in the CloudWatch console\.

1. For `LogStream`, type the destination log stream\. This name appears on the **Log Groups > Streams** screen in the CloudWatch console\.

   If you use `{instance_id}`, the default, the log stream name is the instance ID of this instance\.

   If you specify a log stream name that doesn't already exist, CloudWatch Logs automatically creates it for you\. You can define a log stream name using a literal string, the predefined variables `{instance_id}`, `{hostname}`, and `{ip_address}`, or a combination of these\.

#### Step 3: Configure the Data to Send<a name="configure_logs"></a>

You can send event log data, Event Tracing for Windows \(ETW\) data, and other log data to CloudWatch Logs\.

**To send Windows application event log data to CloudWatch Logs**

1. In the JSON file, locate the `ApplicationEventLog` section\.

   ```
   {
       "Id": "ApplicationEventLog",
       "FullName": "AWS.EC2.Windows.CloudWatch.EventLog.EventLogInputComponent,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "LogName": "Application",
           "Levels": "1"
       }
   },
   ```

1. For `Levels`, specify the type of messages to upload\. You can specify one of the following values:
   + **1** \- Upload only error messages\.
   + **2** \- Upload only warning messages\.
   + **4** \- Upload only information messages\.

   You can combine values to include more than one type of message\. For example, a value of **3** uploads error messages \(**1**\) and warning messages \(**2**\)\. A value of **7** uploads error messages \(**1**\), warning messages \(**2**\), and information messages \(**4**\)\.

**To send security log data to CloudWatch Logs**

1. In the JSON file, locate the `SecurityEventLog` section\.

   ```
   {
       "Id": "SecurityEventLog",
       "FullName": "AWS.EC2.Windows.CloudWatch.EventLog.EventLogInputComponent,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "LogName": "Security",
           "Levels": "7"
       }
   },
   ```

1. For `Levels`, type **7** to upload all messages\.

**To send system event log data to CloudWatch Logs**

1. In the JSON file, locate the `SystemEventLog` section\.

   ```
   {
       "Id": "SystemEventLog",
       "FullName": "AWS.EC2.Windows.CloudWatch.EventLog.EventLogInputComponent,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "LogName": "System",
           "Levels": "7"
       }
   },
   ```

1. For `Levels`, specify the type of messages to upload\. You can specify one of the following values:
   + **1** \- Upload only error messages\.
   + **2** \- Upload only warning messages\.
   + **4** \- Upload only information messages\.

   You can combine values to include more than one type of message\. For example, a value of **3** uploads error messages \(**1**\) and warning messages \(**2**\)\. A value of **7** uploads error messages \(**1**\), warning messages \(**2**\), and information messages \(**4**\)\.

**To send other types of event log data to CloudWatch Logs**

1. In the JSON file, add a new section\. Each section must have a unique `Id`\.

   ```
   {
       "Id": "Id-name",
       "FullName": "AWS.EC2.Windows.CloudWatch.EventLog.EventLogInputComponent,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "LogName": "Log-name",
           "Levels": "7"
       }
   },
   ```

1. For `Id`, type a name for the log to upload \(for example, **WindowsBackup**\)\.

1. For `LogName`, type the name of the log to upload\. You can find the name of the log as follows\.

   1. Open Event Viewer\.

   1. In the navigation pane, choose **Applications and Services Logs**\.

   1. Navigate to the log, and then choose **Actions**, **Properties**\.

1. For `Levels`, specify the type of messages to upload\. You can specify one of the following values:
   + **1** \- Upload only error messages\.
   + **2** \- Upload only warning messages\.
   + **4** \- Upload only information messages\.

   You can combine values to include more than one type of message\. For example, a value of **3** uploads error messages \(**1**\) and warning messages \(**2**\)\. A value of **7** uploads error messages \(**1**\), warning messages \(**2**\), and information messages \(**4**\)\.

**To send Event Tracing for Windows data to CloudWatch Logs**

ETW \(Event Tracing for Windows\) provides an efficient and detailed logging mechanism that applications can write logs to\. Each ETW is controlled by a session manager that can start and stop the logging session\. Each session has a provider and one or more consumers\.

1. In the JSON file, locate the `ETW` section\.

   ```
   {
       "Id": "ETW",
       "FullName": "AWS.EC2.Windows.CloudWatch.EventLog.EventLogInputComponent,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "LogName": "Microsoft-Windows-WinINet/Analytic",
           "Levels": "7"
       }
   },
   ```

1. For `LogName`, type the name of the log to upload\.

1. For `Levels`, specify the type of messages to upload\. You can specify one of the following values:
   + **1** \- Upload only error messages\.
   + **2** \- Upload only warning messages\.
   + **4** \- Upload only information messages\.

   You can combine values to include more than one type of message\. For example, a value of **3** uploads error messages \(**1**\) and warning messages \(**2**\)\. A value of **7** uploads error messages \(**1**\), warning messages \(**2**\), and information messages \(**4**\)\.

**To send custom logs \(any text\-based log file\) to CloudWatch Logs**

1. In the JSON file, locate the `CustomLogs` section\.

   ```
   {
       "Id": "CustomLogs",
       "FullName": "AWS.EC2.Windows.CloudWatch.CustomLog.CustomLogInputComponent,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "LogDirectoryPath": "C:\\CustomLogs\\",
           "TimestampFormat": "MM/dd/yyyy HH:mm:ss",
           "Encoding": "UTF-8",
           "Filter": "",
           "CultureName": "en-US",
           "TimeZoneKind": "Local",
           "LineCount": "5"
       }
   },
   ```

1. For `LogDirectoryPath`, type the path where logs are stored on your instance\.

1. For `TimestampFormat`, type the time stamp format to use\. For more information about supported values, see the [Custom Date and Time Format Strings](https://msdn.microsoft.com/en-us/library/8kb3ddd4(v=vs.110).aspx) topic on MSDN\.
**Important**  
Your source log file must have the time stamp at the beginning of each log line and there must be a space following the time stamp\.

1. For `Encoding`, type the file encoding to use \(for example, UTF\-8\)\. For a list of supported values, see the [Encoding Class](http://msdn.microsoft.com/en-us/library/system.text.encoding.aspx) topic on MSDN\.
**Note**  
Use the encoding name, not the display name\.

1. \(Optional\) For `Filter`, type the prefix of log names\. Leave this parameter blank to monitor all files\. For more information about supported values, see the [FileSystemWatcherFilter Property](https://msdn.microsoft.com/en-us/library/system.io.filesystemwatcher.filter.aspx) topic on MSDN\.

1. \(Optional\) For `CultureName`, type the locale where the time stamp is logged\. If `CultureName` is blank, it defaults to the same locale currently used by your Windows instance\. For more information about, see the `Language tag` column in the table in the [Product Behavior](https://msdn.microsoft.com/en-us/library/cc233982.aspx) topic on MSDN\.
**Note**  
The `div`, `div-MV`, `hu`, and `hu-HU` values are not supported\.

1. \(Optional\) For `TimeZoneKind`, type `Local` or `UTC`\. You can set this to provide time zone information when no time zone information is included in your log's time stamp\. If this parameter is left blank and if your time stamp doesn't include time zone information, CloudWatch Logs defaults to the local time zone\. This parameter is ignored if your time stamp already contains time zone information\.

1. \(Optional\) For `LineCount`, type the number of lines in the header to identify the log file\. For example, IIS log files have virtually identical headers\. You could enter **5**, which would read the first three lines of the log file header to identify it\. In IIS log files, the third line is the date and time stamp, but the time stamp is not always guaranteed to be different between log files\. For this reason, we recommend including at least one line of actual log data to uniquely fingerprint the log file\.

**To send IIS log data to CloudWatch Logs**

1. In the JSON file, locate the `IISLog` section\.

   ```
   {
       "Id": "IISLogs",
       "FullName": "AWS.EC2.Windows.CloudWatch.CustomLog.CustomLogInputComponent,AWS.EC2.Windows.CloudWatch",
       "Parameters": {
           "LogDirectoryPath": "C:\\inetpub\\logs\\LogFiles\\W3SVC1",
           "TimestampFormat": "yyyy-MM-dd HH:mm:ss",
           "Encoding": "UTF-8",
           "Filter": "",
           "CultureName": "en-US",
           "TimeZoneKind": "UTC",
           "LineCount": "5"
       }
   },
   ```

1. For `LogDirectoryPath`, type the folder where IIS logs are stored for an individual site \(for example, `C:\inetpub\logs\LogFiles\W3SVCn`\)\.
**Note**  
Only W3C log format is supported\. IIS, NCSA, and Custom formats are not supported\.

1. For `TimestampFormat`, type the time stamp format to use\. For more information about supported values, see the [Custom Date and Time Format Strings](https://msdn.microsoft.com/en-us/library/8kb3ddd4(v=vs.110).aspx) topic on MSDN\.

1. For `Encoding`, type the file encoding to use \(for example, UTF\-8\)\. For more information about supported values, see the [Encoding Class](http://msdn.microsoft.com/en-us/library/system.text.encoding.aspx) topic on MSDN\.
**Note**  
Use the encoding name, not the display name\.

1. \(Optional\) For `Filter`, type the prefix of log names\. Leave this parameter blank to monitor all files\. For more information about supported values, see the [FileSystemWatcherFilter Property](https://msdn.microsoft.com/en-us/library/system.io.filesystemwatcher.filter.aspx) topic on MSDN\.

1. \(Optional\) For `CultureName`, type the locale where the time stamp is logged\. If `CultureName` is blank, it defaults to the same locale currently used by your Windows instance\. For more information about supported values, see the `Language tag` column in the table in the [Product Behavior](https://msdn.microsoft.com/en-us/library/cc233982.aspx) topic on MSDN\.
**Note**  
The `div`, `div-MV`, `hu`, and `hu-HU` values are not supported\.

1. \(Optional\) For `TimeZoneKind`, enter `Local` or `UTC`\. You can set this to provide time zone information when no time zone information is included in your log's time stamp\. If this parameter is left blank and if your time stamp doesn't include time zone information, CloudWatch Logs defaults to the local time zone\. This parameter is ignored if your time stamp already contains time zone information\.

1. \(Optional\) For `LineCount`, type the number of lines in the header to identify the log file\. For example, IIS log files have virtually identical headers\. You could enter **5**, which would read the first five lines of the log file's header to identify it\. In IIS log files, the third line is the date and time stamp, but the time stamp is not always guaranteed to be different between log files\. For this reason, we recommend including at least one line of actual log data for uniquely fingerprinting the log file\.

#### Step 4: Configure Flow Control<a name="configure_log_flow"></a>

Each data type must have a corresponding destination in the `Flows` section\. For example, to send the custom log, ETW log, and system log to CloudWatch Logs, add `(CustomLogs,ETW,SystemEventLog),CloudWatchLogs` to the `Flows` section\.

**Warning**  
Adding a step that is not valid blocks the flow\. For example, if you add a disk metric step, but your instance doesn't have a disk, all steps in the flow are blocked\.

You can send the same log file to more than one destination\. For example, to send the application log to two different destinations that you defined in the `CloudWatchLogs` section, add `ApplicationEventLog,(CloudWatchLogs,CloudWatchLogs2)` to the `Flows` section\.

**To configure flow control**

1. In the `AWS.EC2.Windows.CloudWatch.json` file, locate the `Flows` section\.

   ```
   "Flows": {
       "Flows": [
         "PerformanceCounter,CloudWatch",
         "(PerformanceCounter,PerformanceCounter2), CloudWatch2",
         "(CustomLogs, ETW, SystemEventLog),CloudWatchLogs",
         "CustomLogs, CloudWatchLogs2",
         "ApplicationEventLog,(CloudWatchLogs, CloudWatchLogs2)"
       ]
   }
   ```

1. For `Flows`, add each data type that is to be uploaded \(for example, `ApplicationEventLog`\) and its destination \(for example, `CloudWatchLogs`\)\.

#### Step 5: Save JSON Content<a name="save_json_content"></a>

You are now finished editing the JSON file\. Save it, and paste the file contents into a text editor in another window\. You will need the file contents in a later step of this procedure\.

### Create an IAM User and Role for Systems Manager<a name="iam_permissions"></a>

An IAM role for instance credentials is required when you use Systems Manager Run Command\. This role enables Systems Manager to perform actions on the instance\. You can optionally create a unique IAM user account for configuring and running Systems Manager\. For more information, see [Configuring Security Roles for Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-access.html) in the *AWS Systems Manager User Guide*\. For information about how to attach an IAM role to an existing instance, see [Attaching an IAM Role to an Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/iam-roles-for-amazon-ec2.html#attach-iam-role) in the *Amazon EC2 User Guide for Windows Instances*\.

### Verify Systems Manager Prerequisites<a name="send_logs_cwl_syspre"></a>

Before you use Systems Manager Run Command to configure integration with CloudWatch Logs, verify that your instances meet the minimum requirements\. For more information, see [Systems Manager Prerequisites](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-setting-up.html) in the *AWS Systems Manager User Guide*\.

### Verify Internet Access<a name="send_logs_cwl_internet"></a>

Your Amazon EC2 Windows Server instances and managed instances must have outbound internet access in order to send log and event data to CloudWatch\. For more information about how to configure internet access, see [Internet Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) in the *Amazon VPC User Guide*\.

### Enable CloudWatch Logs Using Systems Manager Run Command<a name="remote-commands-cloudwatch"></a>

Run Command enables you to manage the configuration of your instances on demand\. You specify a Systems Manager document, specify parameters, and execute the command on one or more instances\. The SSM agent on the instance processes the command and configures the instance as specified\.

**To configure integration with CloudWatch Logs using Run Command**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Open the SSM console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Run Command**\.

1. Choose **Run a command**\.

1. For **Command document**, choose **AWS\-ConfigureCloudWatch**\.

1. For **Target instances**, choose the instances to integrate with CloudWatch Logs\. If you do not see an instance in this list, it might not be configured for Run Command\. For more information, see [Systems Manager Prerequisites](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/systems-manager-setting-up.html) in the *Amazon EC2 User Guide for Windows Instances*\.

1. For **Status**, choose **Enabled**\.

1. For **Properties**, copy and paste the JSON content you created in the previous tasks\.

1. Complete the remaining optional fields and choose **Run**\.

Use the following procedure to view the results of command execution in the Amazon EC2 console\.

**To view command output in the console**

1. Select a command\.

1. Choose the **Output** tab\.

1. Choose **View Output**\. The command output page shows the results of your command execution\.