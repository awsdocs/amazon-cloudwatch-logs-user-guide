# Sample Queries<a name="CWL_QuerySyntax-examples"></a>

This section includes example queries that show the power of CloudWatch Logs Insights\.

**General Queries**

Find the 25 most recently added log events:

```
fields @timestamp, @message | sort @timestamp desc | limit 25
```

Get a list of the number of exceptions per hour:

```
filter @message like /Exception/ 
| stats count(*) as exceptionCount by bin(1h)
| sort exceptionCount desc
```

Get a list of log events that are not exceptions:

```
fields @message | filter @message not like /Exception/
```

**Filter Command Examples**

To use 

**Queries for Lambda Logs**

Determine the amount of overprovisioned memory:

```
filter @type = "REPORT"
| stats max(@memorySize / 1024 / 1024) as provisonedMemoryMB,
    min(@maxMemoryUsed / 1024 / 1024) as smallestMemoryRequestMB,
    avg(@maxMemoryUsed / 1024 / 1024) as avgMemoryUsedMB,
    max(@maxMemoryUsed / 1024 / 1024) as maxMemoryUsedMB,
    provisonedMemoryMB - maxMemoryUsedMB as overProvisionedMB
```

Create a latency report:

```
filter type = "REPORT" |
stats avg(@duration), max(@duration), min(@duration) by bin(5m)
```

**Queries for Amazon VPC Flow Logs**

Find the top 15 packet transfers across hosts:

```
stats sum(packets) as packetsTransferred by srcAddr, dstAddr
| sort packetsTransferred  desc
| limit 15
```

Find the top 15 byte transfers for a given host: 

```
filter srcAddr= "192.0.2.0/24"
| stats sum(bytes) as bytesTransferred by dstAddr
| sort bytesTransferred  desc
| limit 15
```

Find the IP addresses using UDP as a data transfer protocol:

```
filter protocol=17 | stats count(*) by srcAddr
```

Find the IP addresses where flow records were skipped during the capture window:

```
filter logStatus="SKIPDATA"
| stats count(*) by bin(1h) as t
| sort t
```

**Queries for Route 53 Logs**

Find the distribution of records per hour by query type:

```
stats count(*) by queryType, bin(1h)
```

Find the 10 DNS resolvers with the highest number of requests:

```
stats count(*) as numRequests by resolverIp
| sort numRequests desc
| limit 10
```

Find the number of records by domain and subdomain where the server failed to complete the DNS request:

```
filter responseCode="SERVFAIL" | stats count(*) by queryName
```

**Queries for CloudTrail Logs**

Find the number of log entries for each service, event type, and Region:

```
stats count(*) by eventSource, eventName, awsRegion
```

Find the Amazon EC2 hosts that were started or stopped in a given Region:

```
filter (eventName="StartInstances" or eventName="StopInstances") and region="us-east-2"
```

Find the Regions, user names, and ARNs of newly created IAM users:

```
filter eventName="CreateUser"
| fields awsRegion, requestParameters.userName, responseElements.user.arn
```

Find the number of records where an exception occurred while invoking the API UpdateTrail:

```
filter eventName="UpdateTrail" and ispresent(errorCode)
| stats count(*) by errorCode, errorMessage
```
