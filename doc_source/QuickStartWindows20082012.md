# Quick Start: Enable Your Amazon EC2 Instances Running Windows Server 2012 and Windows Server 2008 to Send logs to CloudWatch Logs<a name="QuickStartWindows20082012"></a>

**Tip**  
CloudWatch includes a new unified agent that can collect both logs and metrics from EC2 instances and on\-premises servers\. We recommend that you use the newer unified CloudWatch agent\. For more information, see [Getting Started with CloudWatch Logs](CWL_GettingStarted.md)\.   
The rest of this section explains the use of the older CloudWatch Logs agent\.

## Enable Your Amazon EC2 Instances Running Windows Server 2012 and Windows Server 2008 to Send logs to CloudWatch Logs<a name="QuickStartWindows20082012-olderagent"></a>

Use the following steps to enable your instances running Windows Server 2012 and Windows Server 2008 to send logs to CloudWatch Logs\.

### Download the Sample Configuration File<a name="configure_cwl_download2012"></a>

Download the following sample JSON file to your computer: [AWS\.EC2\.Windows\.CloudWatch\.json](https://s3.amazonaws.com/ec2-downloads-windows/CloudWatchConfig/AWS.EC2.Windows.CloudWatch.json)\. You edit it in the following steps\.

### Configure the JSON File for CloudWatch<a name="send_logs_to_cwl_json2012"></a>

You determine which logs to send to CloudWatch by specifying your choices in the JSON configuration file\. The process of creating this file and specifying your choices can take 30 minutes or more to complete\. After you have completed this task once, you can reuse the configuration file on all of your instances\.

**Topics**
+ [Step 1: Enable CloudWatch Logs](#enable-CloudWatchLogs-in-JSON-file2012)
+ [Step 2: Configure Settings for CloudWatch](#configure_cwl_credentials2012)
+ [Step 3: Configure the Data to Send](#configure_logs2012)
+ [Step 4: Configure Flow Control](#configure_log_flow2012)

#### Step 1: Enable CloudWatch Logs<a name="enable-CloudWatchLogs-in-JSON-file2012"></a>

At the top of the JSON file, change "false" to "true" for `IsEnabled`:

```
"IsEnabled": true,
```

#### Step 2: Configure Settings for CloudWatch<a name="configure_cwl_credentials2012"></a>

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

#### Step 3: Configure the Data to Send<a name="configure_logs2012"></a>

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

1. For `Encoding`, type the file encoding to use \(for example, UTF\-8\)\. For more information about supported values, see the [Encoding Class](http://msdn.microsoft.com/en-us/library/system.text.encoding.aspx) topic on MSDN\.
**Note**  
Use the encoding name, not the display name\.

1. \(Optional\) For `Filter`, type the prefix of log names\. Leave this parameter blank to monitor all files\. For more information about supported values, see the [FileSystemWatcherFilter Property](https://msdn.microsoft.com/en-us/library/system.io.filesystemwatcher.filter.aspx) topic on MSDN\.

1. \(Optional\) For `CultureName`, type the locale where the time stamp is logged\. If `CultureName` is blank, it defaults to the same locale currently used by your Windows instance\. For more information about supported values, see the `Language tag` column in the table in the [Product Behavior](https://msdn.microsoft.com/en-us/library/cc233982.aspx) topic on MSDN\.
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

#### Step 4: Configure Flow Control<a name="configure_log_flow2012"></a>

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

You are now finished editing the JSON file\. You use it in a later step\.

### Start the Agent<a name="ec2config2012"></a>

To enable an Amazon EC2 instance running Windows Server 2012 or Windows Server 2008 to send logs to CloudWatch Logs, use the EC2Config service \(`EC2Config.exe)`\. Your instance should have EC2Config 4\.0 or later, and you can use this procedure\. For more information about using an earlier version of EC2Config, see [Use EC2Config 3\.x or Earlier to Configure CloudWatch](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/send_logs_to_cwl_instances.html#ec2config-3x) in the *Amazon EC2 User Guide for Windows Instances*

**To configure CloudWatch using EC2Config 4\.x**

1. Check the encoding of the AWS\.EC2\.Windows\.CloudWatch\.json file that you edited earlier in this procedure\. Only UTF\-8 without BOM encoding is supported\. Then save the file in the following folder on your Windows Server 2008 \- 2012 R2 instance: `C:\Program Files\Amazon\SSM\Plugins\awsCloudWatch\`\.

1. Start or restart the SSM agent \(`AmazonSSMAgent.exe`\) using the Windows Services control panel or using the following PowerShell command:

   ```
   PS C:\> Restart-Service AmazonSSMAgent
   ```

After the SSM agent restarts, it detects the configuration file and configures the instance for CloudWatch integration\. If you change parameters and settings in the local configuration file, you need to restart the SSM agent to pick up the changes\. To disable CloudWatch integration on the instance, change `IsEnabled` to `false` and save your changes in the configuration file\.