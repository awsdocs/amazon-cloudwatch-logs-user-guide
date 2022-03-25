# Sample queries<a name="CWL_QuerySyntax-examples"></a>

This section contains a list of general and useful query commands that you can run in the [CloudWatch console](https://console.aws.amazon.com/cloudwatch/)\. For information about how to run a query command, see [Tutorial: Run and modify a sample query](https://docs.aws.amazon.com/en_us/AmazonCloudWatch/latest/logs/CWL_AnalyzeLogData_RunSampleQuery.html) in the *Amazon CloudWatch Logs User Guide*\.

**General queries**

Find the 25 most recently added log events\.

```
fields @timestamp, @message | sort @timestamp desc | limit 25
```

Get a list of the number of exceptions per hour\.

```
filter @message like /Exception/ 
    | stats count(*) as exceptionCount by bin(1h)
    | sort exceptionCount desc
```

Get a list of log events that aren't exceptions\.

```
fields @message | filter @message not like /Exception/
```

**Queries for Lambda logs**

Determine the amount of overprovisioned memory\.

```
filter @type = "REPORT"
    | stats max(@memorySize / 1000 / 1000) as provisonedMemoryMB,
        min(@maxMemoryUsed / 1000 / 1000) as smallestMemoryRequestMB,
        avg(@maxMemoryUsed / 1000 / 1000) as avgMemoryUsedMB,
        max(@maxMemoryUsed / 1000 / 1000) as maxMemoryUsedMB,
        provisonedMemoryMB - maxMemoryUsedMB as overProvisionedMB
```

Create a latency report\.

```
filter @type = "REPORT" |
    stats avg(@duration), max(@duration), min(@duration) by bin(5m)
```

**Queries for Amazon VPC Flow Logs**

Find the top 15 packet transfers across hosts:

```
stats sum(packets) as packetsTransferred by srcAddr, dstAddr
    | sort packetsTransferred  desc
    | limit 15
```

Find the top 15 byte transfers for hosts on a given subnet\.

```
filter isIpv4InSubnet(srcAddr, "192.0.2.0/24")
    | stats sum(bytes) as bytesTransferred by dstAddr
    | sort bytesTransferred desc
    | limit 15
```

Find the IP addresses that use UDP as a data transfer protocol\.

```
filter protocol=17 | stats count(*) by srcAddr
```

Find the IP addresses where flow records were skipped during the capture window\.

```
filter logStatus="SKIPDATA"
    | stats count(*) by bin(1h) as t
    | sort t
```

**Queries for Route 53 logs**

Find the distribution of records per hour by query type\.

```
stats count(*) by queryType, bin(1h)
```

Find the 10 DNS resolvers with the highest number of requests\.

```
stats count(*) as numRequests by resolverIp
    | sort numRequests desc
    | limit 10
```

Find the number of records by domain and subdomain where the server failed to complete the DNS request\.

```
filter responseCode="SERVFAIL" | stats count(*) by queryName
```

**Queries for CloudTrail logs**

Find the number of log entries for each service, event type, and AWS Region\.

```
stats count(*) by eventSource, eventName, awsRegion
```

Find the Amazon EC2 hosts that were started or stopped in a given AWS Region\.

```
filter (eventName="StartInstances" or eventName="StopInstances") and awsRegion="us-east-2"
```

Find the AWS Regions, user names, and ARNs of newly created IAM users\.

```
filter eventName="CreateUser"
    | fields awsRegion, requestParameters.userName, responseElements.user.arn
```

Find the number of records where an exception occurred while invoking the API `UpdateTrail`\.

```
filter eventName="UpdateTrail" and ispresent(errorCode)
    | stats count(*) by errorCode, errorMessage
```

**Queries for NAT gateway**

If you notice higher than normal costs in your AWS bill, you can use CloudWatch Logs Insights to find the top contributors\. For more information about the following query commands, see [How can I find the top contributors to traffic through the NAT gateway in my VPC?](https://aws.amazon.com/premiumsupport/knowledge-center/vpc-find-traffic-sources-nat-gateway/) at the AWS premium support page\.

Find the instances that are sending the most traffic through you NAT gateway\.

**Note**  
In the following query commands, replace "x\.x\.x\.x" with the private IP of your NAT gateway, and replace "y\.y" with the first two octets of your VPC CIDR range\.

```
filter (dstAddr like 'x.x.x.x' and srcAddr like 'y.y.') 
| stats sum(bytes) as bytesTransferred by srcAddr, dstAddr
| sort bytesTransferred desc
| limit 10
```

Determine the traffic that's going to and from the instances in your NAT gateways\.

```
filter (dstAddr like 'x.x.x.x' and srcAddr like 'y.y.') or (srcAddr like 'xxx.xx.xx.xx' and dstAddr like 'y.y.')
| stats sum(bytes) as bytesTransferred by srcAddr, dstAddr
| sort bytesTransferred desc
| limit 10
```

Determine the internet destinations that the instances in your VPC communicate with most often for uploads and downloads\.

***For uploads***

```
filter (srcAddr like 'x.x.x.x' and dstAddr not like 'y.y.') 
| stats sum(bytes) as bytesTransferred by srcAddr, dstAddr
| sort bytesTransferred desc
| limit 10
```

***For downloads***

```
filter (dstAddr like 'x.x.x.x' and srcAddr not like 'y.y.') 
| stats sum(bytes) as bytesTransferred by srcAddr, dstAddr
| sort bytesTransferred desc
| limit 10
```

**Queries for Apache server logs**

You can use CloudWatch Logs Insights to query Apache server logs\. For more information about the following queries, see [Simplifying Apache server logs with CloudWatch Logs Insights](https://aws.amazon.com/blogs/mt/simplifying-apache-server-logs-with-amazon-cloudwatch-logs-insights/) at the AWS Cloud Operations & Migrations Blog\.

Find the most relevant fields, so you can review your access logs and check for traffic in the */admin* path of your application\.

```
fields @timestamp, remoteIP, request, status, filename| sort @timestamp desc
| filter filename="/var/www/html/admin"
| limit 20
```

Find the number unique GET requests that accessed your main page with status code "200" \(success\)\.

```
fields @timestamp, remoteIP, method, status
| filter status="200" and referrer= http://34.250.27.141/ and method= "GET"
| stats count_distinct(remoteIP) as UniqueVisits
| limit 10
```

Find the number of times your Apache service restarted\.

```
fields @timestamp, function, process, message
| filter message like "resuming normal operations"
| sort @timestamp desc
| limit 20
```

**Examples of the parse command**

Use a glob expression to extract the ephemeral fields `@user`, `@method`, and `@latency` from the log field `@message` and return the average latency for each unique combination of `@method` and `@user`\.

```
parse @message "user=*, method:*, latency := *" as @user,
    @method, @latency | stats avg(@latency) by @method,
    @user
```

Use a regular expression to extract the ephemeral fields `@user2`, `@method2`, and `@latency2` from the log field `@message` and return the average latency for each unique combination of `@method2` and `@user2`\.

```
parse @message /user=(?<user2>.*?), method:(?<method2>.*?),
    latency := (?<latency2>.*?)/ | stats avg(latency2) by @method2, 
    @user2
```

Extracts the ephemeral fields `loggingTime`, `loggingType` and `loggingMessage`, filters down to log events that contain `ERROR` or `INFO` strings, and then displays only the `loggingMessage` and `loggingType` fields for events that contain an `ERROR` string\.

```
FIELDS @message
    | PARSE @message "* [*] *" as loggingTime, loggingType, loggingMessage
    | FILTER loggingType IN ["ERROR", "INFO"]
    | DISPLAY loggingMessage, loggingType = "ERROR" as isError
```