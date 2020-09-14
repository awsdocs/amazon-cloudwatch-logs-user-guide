# CloudWatch Logs Agent Reference<a name="AgentReference"></a>

**Important**  
This reference is for the older CloudWatch Logs agent, which is on the path to deprecation\. We strongly recommend that you use the unified CloudWatch agent instead\. For more information about that agent, see [ Collecting metrics and logs from Amazon EC2 instance and on\-premises servers with the CloudWatch agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html)\.

The CloudWatch Logs agent provides an automated way to send log data to CloudWatch Logs from Amazon EC2 instances\. The agent includes the following components:
+ A plug\-in to the AWS CLI that pushes log data to CloudWatch Logs\.
+ A script \(daemon\) that initiates the process to push data to CloudWatch Logs\.
+ A cron job that ensures that the daemon is always running\.

## Agent Configuration File<a name="agent-configuration-file"></a>

The CloudWatch Logs agent configuration file describes information needed by the CloudWatch Logs agent\. The agent configuration file's \[general\] section defines common configurations that apply to all log streams\. The \[logstream\] section defines the information necessary to send a local file to a remote log stream\. You can have more than one \[logstream\] section, but each must have a unique name within the configuration file, e\.g\., \[logstream1\], \[logstream2\], and so on\. The \[logstream\] value along with the first line of data in the log file, define the log file's identity\.

```
[general]
state_file = value
logging_config_file = value
use_gzip_http_content_encoding = [true | false]

[logstream1]
log_group_name = value
log_stream_name = value
datetime_format = value
time_zone = [LOCAL|UTC]
file = value
file_fingerprint_lines = integer | integer-integer
multi_line_start_pattern = regex | {datetime_format}
initial_position = [start_of_file | end_of_file]
encoding = [ascii|utf_8|..]
buffer_duration = integer
batch_count = integer
batch_size = integer

[logstream2]
...
```

**state\_file**  
Specifies where the state file is stored\.

**logging\_config\_file**  
\(Optional\) Specifies the location of the agent logging config file\. If you do not specify an agent logging config file here, the default file awslogs\.conf is used\. The default file location is `/var/awslogs/etc/awslogs.conf` if you installed the agent with a script, and is `/etc/awslogs/awslogs.conf` if you installed the agent with rpm\. The file is in Python configuration file format \(https://docs\.python\.org/2/library/logging\.config\.html\#logging\-config\-fileformat\)\. Loggers with the following names can be customized\.  

```
cwlogs.push
cwlogs.push.reader
cwlogs.push.publisher
cwlogs.push.event
cwlogs.push.batch
cwlogs.push.stream
cwlogs.push.watcher
```
The sample below changes the level of reader and publisher to WARNING while the default value is INFO\.  

```
[loggers]
keys=root,cwlogs,reader,publisher
            
[handlers]
keys=consoleHandler
            
[formatters]
keys=simpleFormatter
           
[logger_root]
level=INFO
handlers=consoleHandler
            
[logger_cwlogs]
level=INFO
handlers=consoleHandler
qualname=cwlogs.push
propagate=0
            
[logger_reader]
level=WARNING
handlers=consoleHandler
qualname=cwlogs.push.reader
propagate=0
            
[logger_publisher]
level=WARNING
handlers=consoleHandler
qualname=cwlogs.push.publisher
propagate=0
            
[handler_consoleHandler]
class=logging.StreamHandler
level=INFO
formatter=simpleFormatter
args=(sys.stderr,)
            
[formatter_simpleFormatter]
format=%(asctime)s - %(name)s - %(levelname)s - %(process)d - %(threadName)s - %(message)s
```

**use\_gzip\_http\_content\_encoding**  
When set to true \(default\), enables gzip http content encoding to send compressed payloads to CloudWatch Logs\. This decreases CPU usage, lowers NetworkOut, and decreases put latency\. To disable this feature, add **use\_gzip\_http\_content\_encoding = false** to the **\[general\]** section of the CloudWatch Logs agent configuration file, and then restart the agent\.  
This setting is only available in awscli\-cwlogs version 1\.3\.3 and later\.

**log\_group\_name**  
Specifies the destination log group\. A log group is created automatically if it doesn't already exist\. Log group names can be between 1 and 512 characters long\. Allowed characters include a\-z, A\-Z, 0\-9, '\_' \(underscore\), '\-' \(hyphen\), '/' \(forward slash\), and '\.' \(period\)\.

**log\_stream\_name**  
Specifies the destination log stream\. You can use a literal string or predefined variables \(\{instance\_id\}, \{hostname\}, \{ip\_address\}\), or combination of both to define a log stream name\. A log stream is created automatically if it doesn't already exist\.

**datetime\_format**  
Specifies how the timestamp is extracted from logs\. The timestamp is used for retrieving log events and generating metrics\. The current time is used for each log event if the **datetime\_format** isn't provided\. If the provided **datetime\_format** value is invalid for a given log message, the timestamp from the last log event with a successfully parsed timestamp is used\. If no previous log events exist, the current time is used\.  
The common datetime\_format codes are listed below\. You can also use any datetime\_format codes supported by Python, datetime\.strptime\(\)\. The timezone offset \(%z\) is also supported even though it's not supported until python 3\.2, \[\+\-\]HHMM without colon\(:\)\. For more information, see [strftime\(\) and strptime\(\) Behavior](https://docs.python.org/2/library/datetime.html#strftime-strptime-behavior)\.  
**%y**: Year without century as a zero\-padded decimal number\. 00, 01, \.\.\., 99  
**%Y**: Year with century as a decimal number\.1970, 1988, 2001, 2013  
**%b**: Month as locale's abbreviated name\. Jan, Feb, \.\.\., Dec \(en\_US\);  
**%B**: Month as locale's full name\. January, February, \.\.\., December \(en\_US\);  
**%m**: Month as a zero\-padded decimal number\. 01, 02, \.\.\., 12  
**%d**: Day of the month as a zero\-padded decimal number\. 01, 02, \.\.\., 31  
**%H**: Hour \(24\-hour clock\) as a zero\-padded decimal number\. 00, 01, \.\.\., 23  
**%I**: Hour \(12\-hour clock\) as a zero\-padded decimal number\. 01, 02, \.\.\., 12  
**%p**: Locale's equivalent of either AM or PM\.  
**%M**: Minute as a zero\-padded decimal number\. 00, 01, \.\.\., 59  
**%S**: Second as a zero\-padded decimal number\. 00, 01, \.\.\., 59  
**%f**: Microsecond as a decimal number, zero\-padded on the left\. 000000, \.\.\., 999999  
**%z**: UTC offset in the form \+HHMM or \-HHMM\. \+0000, \-0400, \+1030  
**Example formats:**  
`Syslog: '%b %d %H:%M:%S', e.g. Jan 23 20:59:29`  
`Log4j: '%d %b %Y %H:%M:%S', e.g. 24 Jan 2014 05:00:00`  
`ISO8601: '%Y-%m-%dT%H:%M:%S%z', e.g. 2014-02-20T05:20:20+0000` 

**time\_zone**  
Specifies the time zone of log event timestamp\. The two supported values are UTC and LOCAL\. The default is LOCAL, which is used if time zone can't be inferred based on **datetime\_format**\.

**file**  
Specifies log files that you want to push to CloudWatch Logs\. File can point to a specific file or multiple files \(using wildcards such as /var/log/system\.log\*\)\. Only the latest file is pushed to CloudWatch Logs based on file modification time\. We recommend that you use wildcards to specify a series of files of the same type, such as access\_log\.2014\-06\-01\-01, access\_log\.2014\-06\-01\-02, and so on, but not multiple kinds of files, such as access\_log\_80 and access\_log\_443\. To specify multiple kinds of files, add another log stream entry to the configuration file so each kind of log file goes to a different log stream\. Zipped files are not supported\.

**file\_fingerprint\_lines**  
Specifies the range of lines for identifying a file\. The valid values are one number or two dash delimited numbers, such as '1', '2\-5'\. The default value is '1' so the first line is used to calculate fingerprint\. Fingerprint lines are not sent to CloudWatch Logs unless all the specified lines are available\.

**multi\_line\_start\_pattern**  
Specifies the pattern for identifying the start of a log message\. A log message is made of a line that matches the pattern and any following lines that don't match the pattern\. The valid values are regular expression or \{datetime\_format\}\. When using \{datetime\_format\}, the datetime\_format option should be specified\. The default value is ‘^\[^\\s\]' so any line that begins with non\-whitespace character closes the previous log message and starts a new log message\.

**initial\_position**  
Specifies where to start to read data \(start\_of\_file or end\_of\_file\)\. The default is start\_of\_file\. It's only used if there is no state persisted for that log stream\.

**encoding**  
Specifies the encoding of the log file so that the file can be read correctly\. The default is utf\_8\. Encodings supported by Python codecs\.decode\(\) can be used here\.  
Specifying an incorrect encoding might cause data loss because characters that cannot be decoded are replaced with some other character\.
Below are some common encodings:  
 `ascii, big5, big5hkscs, cp037, cp424, cp437, cp500, cp720, cp737, cp775, cp850, cp852, cp855, cp856, cp857, cp858, cp860, cp861, cp862, cp863, cp864, cp865, cp866, cp869, cp874, cp875, cp932, cp949, cp950, cp1006, cp1026, cp1140, cp1250, cp1251, cp1252, cp1253, cp1254, cp1255, cp1256, cp1257, cp1258, euc_jp, euc_jis_2004, euc_jisx0213, euc_kr, gb2312, gbk, gb18030, hz, iso2022_jp, iso2022_jp_1, iso2022_jp_2, iso2022_jp_2004, iso2022_jp_3, iso2022_jp_ext, iso2022_kr, latin_1, iso8859_2, iso8859_3, iso8859_4, iso8859_5, iso8859_6, iso8859_7, iso8859_8, iso8859_9, iso8859_10, iso8859_13, iso8859_14, iso8859_15, iso8859_16, johab, koi8_r, koi8_u, mac_cyrillic, mac_greek, mac_iceland, mac_latin2, mac_roman, mac_turkish, ptcp154, shift_jis, shift_jis_2004, shift_jisx0213, utf_32, utf_32_be, utf_32_le, utf_16, utf_16_be, utf_16_le, utf_7, utf_8, utf_8_sig` 

**buffer\_duration**  
Specifies the time duration for the batching of log events\. The minimum value is 5000ms and default value is 5000ms\.

**batch\_count**  
Specifies the max number of log events in a batch, up to 10000\. The default value is 10000\.

**batch\_size**  
Specifies the max size of log events in a batch, in bytes, up to 1048576 bytes\. The default value is 1048576 bytes\. This size is calculated as the sum of all event messages in UTF\-8, plus 26 bytes for each log event\.

## Using the CloudWatch Logs Agent with HTTP Proxies<a name="agent-http-proxies"></a>

You can use the CloudWatch Logs agent with HTTP proxies\.

**Note**  
HTTP proxies are supported in awslogs\-agent\-setup\.py version 1\.3\.8 or later\.

**To use the CloudWatch Logs agent with HTTP proxies**

1. Do one of the following:

   1. For a new installation of the CloudWatch Logs agent, run the following commands:

      ```
      curl https://s3.amazonaws.com/aws-cloudwatch/downloads/latest/awslogs-agent-setup.py -O
      ```

      ```
      sudo python awslogs-agent-setup.py --region us-east-1 --http-proxy http://your/proxy --https-proxy http://your/proxy --no-proxy 169.254.169.254
      ```

      In order to maintain access to the Amazon EC2 metadata service on EC2 instances, use **\-\-no\-proxy 169\.254\.169\.254** \(recommended\)\. For more information, see [Instance Metadata and User Data](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html) in the *Amazon EC2 User Guide for Linux Instances*\.

      In the values for `http-proxy` and `https-proxy`, you specify the entire URL\.

   1. For an existing installation of the CloudWatch Logs agent, edit /var/awslogs/etc/proxy\.conf, and add your proxies:

      ```
      HTTP_PROXY=
      HTTPS_PROXY=
      NO_PROXY=
      ```

1. Restart the agent for the changes to take effect:

   ```
   sudo service awslogs restart
   ```

   If you are using Amazon Linux 2, use the following command to restart the agent:

   ```
   sudo service awslogsd restart
   ```

## Compartmentalizing CloudWatch Logs Agent Configuration Files<a name="create-additional-configuration-files"></a>

If you're using awslogs\-agent\-setup\.py version 1\.3\.8 or later with awscli\-cwlogs 1\.3\.3 or later, you can import different stream configurations for various components independently of one another by creating additional configuration files in the **/var/awslogs/etc/config/** directory\. When the CloudWatch Logs agent starts, it includes any stream configurations in these additional configuration files\. Configuration properties in the \[general\] section must be defined in the main configuration file \(/var/awslogs/etc/awslogs\.conf\) and are ignored in any additional configuration files found in /var/awslogs/etc/config/\.

If you don't have a **/var/awslogs/etc/config/** directory because you installed the agent with rpm, you can use the **/etc/awslogs/config/** directory instead\.

Restart the agent for the changes to take effect:

```
sudo service awslogs restart
```

If you are using Amazon Linux 2, use the following command to restart the agent:

```
sudo service awslogsd restart
```

## CloudWatch Logs Agent FAQs<a name="agent-faq"></a>

**What kinds of file rotations are supported?**  
The following file rotation mechanisms are supported:  
+ Renaming existing log files with a numerical suffix, then re\-creating the original empty log file\. For example, /var/log/syslog\.log is renamed /var/log/syslog\.log\.1\. If /var/log/syslog\.log\.1 already exists from a previous rotation, it is renamed /var/log/syslog\.log\.2\.
+ Truncating the original log file in place after creating a copy\. For example, /var/log/syslog\.log is copied to /var/log/syslog\.log\.1 and /var/log/syslog\.log is truncated\. There might be data loss for this case, so be careful about using this file rotation mechanism\.
+ Creating a new file with a common pattern as the old one\. For example, /var/log/syslog\.log\.2014\-01\-01 remains and /var/log/syslog\.log\.2014\-01\-02 is created\.
The fingerprint \(source ID\) of the file is calculated by hashing the log stream key and the first line of file content\. To override this behavior, the **file\_fingerprint\_lines** option can be used\. When file rotation happens, the new file is supposed to have new content and the old file is not supposed to have content appended; the agent pushes the new file after it finishes reading the old file\.

**How can I determine which version of agent am I using?**  
If you used a setup script to install the CloudWatch Logs agent, you can use **/var/awslogs/bin/awslogs\-version\.sh** to check what version of the agent you are using\. It prints out the version of the agent and its major dependencies\. If you used yum to install the CloudWatch Logs agent, you can use **"yum info awslogs"** and **"yum info aws\-cli\-plugin\-cloudwatch\-logs"** to check the version of the CloudWatch Logs agent and plugin\.

**How are log entries converted to log events?**  
Log events contain two properties: the timestamp of when the event occurred, and the raw log message\. By default, any line that begins with non\-whitespace character closes the previous log message if there is one, and starts a new log message\. To override this behavior, the **multi\_line\_start\_pattern** can be used and any line that matches the pattern starts a new log message\. The pattern could be any regex or '\{datetime\_format\}'\. For example, if the first line of every log message contains a timestamp like '2014\-01\-02T13:13:01Z', then the **multi\_line\_start\_pattern** can be set to '\\d\{4\}\-\\d\{2\}\-\\d\{2\}T\\d\{2\}:\\d\{2\}:\\d\{2\}Z'\. To simplify the configuration, the ‘\{datetime\_format\}' variable can be used if the **datetime\_format option** is specified\. For the same example, if **datetime\_format** is set to '%Y\-%m\-%dT%H:%M:%S%z', then multi\_line\_start\_pattern could be simply '\{datetime\_format\}'\.  
The current time is used for each log event if the **datetime\_format** isn't provided\. If the provided **datetime\_format** is invalid for a given log message, the timestamp from the last log event with a successfully parsed timestamp is used\. If no previous log events exist, the current time is used\. A warning message is logged when a log event falls back to the current time or time of previous log event\.  
Timestamps are used for retrieving log events and generating metrics, so if you specify the wrong format, log events could become non\-retrievable and generate wrong metrics\.

**How are log events batched?**  
A batch becomes full and is published when any of the following conditions are met:  

1. The **buffer\_duration** amount of time has passed since the first log event was added\.

1. Less than **batch\_size** of log events have been accumulated but adding the new log event exceeds the **batch\_size**\.

1. The number of log events has reached **batch\_count**\.

1. Log events from the batch don't span more than 24 hours, but adding the new log event exceeds the 24 hours constraint\.

**What would cause log entries, log events, or batches to be skipped or truncated?**  
To follow the constraint of the `PutLogEvents` operation, the following issues could cause a log event or batch to be skipped\.  
The CloudWatch Logs agent writes a warning to its log when data is skipped\.

1. If the size of a log event exceeds 256 KB, the log event is skipped completely\.

1. If the timestamp of log event is more than 2 hours in future, the log event is skipped\.

1. If the timestamp of log event is more than 14 days in past, the log event is skipped\.

1. If any log event is older than the retention period of log group, the whole batch is skipped\.

1. If the batch of log events in a single `PutLogEvents` request spans more than 24 hours, the `PutLogEvents` operation fails\.

**Does stopping the agent cause data loss/duplicates?**  
Not as long as the state file is available and no file rotation has happened since the last run\. The CloudWatch Logs agent can start from where it stopped and continue pushing the log data\.

**Can I point different log files from the same or different hosts to the same log stream?**  
Configuring multiple log sources to send data to a single log stream is not supported\.

**What API calls does the agent make \(or what actions should I add to my IAM policy\)?**  
The CloudWatch Logs agent requires the `CreateLogGroup`, `CreateLogStream`, `DescribeLogStreams`, and `PutLogEvents` operations\. If you're using the latest agent, `DescribeLogStreams` is not needed\. See the sample IAM policy below\.  

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

**I don't want the CloudWatch Logs agent to create either log groups or log streams automatically\. How can I prevent the agent from recreating both log groups and log streams?**  
In your IAM policy, you can restrict the agent to only the following operations: `DescribeLogStreams`, `PutLogEvents`\.  
Before you revoke the `CreateLogGroup` and `CreateLogStream` permissions from the agent, be sure to create both the log groups and log streams that you want the agent to use\. The logs agent cannot create log streams in a log group that you have created unless it has both the `CreateLogGroup` and `CreateLogStream` permissions\.

**What logs should I look at when troubleshooting?**  
The agent installation log is at `/var/log/awslogs-agent-setup.log` and the agent log is at `/var/log/awslogs.log`\.