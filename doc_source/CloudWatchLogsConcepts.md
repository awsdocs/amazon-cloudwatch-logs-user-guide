# Amazon CloudWatch Logs Concepts<a name="CloudWatchLogsConcepts"></a>

The terminology and concepts that are central to your understanding and use of CloudWatch Logs are described below\.

**Log events**  
A log event is a record of some activity recorded by the application or resource being monitored\. The log event record that CloudWatch Logs understands contains two properties: the timestamp of when the event occurred, and the raw event message\. Event messages must be UTF\-8 encoded\.

**Log streams**  
A log stream is a sequence of log events that share the same source\. More specifically, a log stream is generally intended to represent the sequence of events coming from the application instance or resource being monitored\. For example, a log stream may be associated with an Apache access log on a specific host\. When you no longer need a log stream, you can delete it using the [aws logs delete\-log\-stream](https://docs.aws.amazon.com/cli/latest/reference/logs/delete-log-stream.html) command\. In addition, AWS may delete empty log streams that are over 2 months old\.

**Log groups**  
Log groups define groups of log streams that share the same retention, monitoring, and access control settings\. Each log stream has to belong to one log group\. For example, if you have a separate log stream for the Apache access logs from each host, you could group those log streams into a single log group called `MyWebsite.com/Apache/access_log`\.  
There is no limit on the number of log streams that can belong to one log group\.

**Metric filters**  
You can use metric filters to extract metric observations from ingested events and transform them to data points in a CloudWatch metric\. Metric filters are assigned to log groups, and all of the filters assigned to a log group are applied to their log streams\.

**Retention settings**  
Retention settings can be used to specify how long log events are kept in CloudWatch Logs\. Expired log events get deleted automatically\. Just like metric filters, retention settings are also assigned to log groups, and the retention assigned to a log group is applied to their log streams\.