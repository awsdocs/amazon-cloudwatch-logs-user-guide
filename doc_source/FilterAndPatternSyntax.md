# Filter and pattern syntax<a name="FilterAndPatternSyntax"></a>

You can create metric filters to match terms in your log events and convert log data into metrics\. When a metric filter matches a term, it increments the metric's count\. For example, you can create a metric filter that counts the number of times the word ***ERROR*** occurs in your log events\.

You can assign units and dimensions to metrics\. For example, if you create a metric filter that counts the number of times the word ***ERROR*** occurs in your log events, you can specify a dimension that's called `ErrorCode` to show the total number of log events that contain the word ***ERROR*** and filter data by reported error codes\.

**Note**  
When you assign a unit to a metric, make sure to specify the correct one\. If you change the unit later, your change might not take effect\.

**Topics**
+ [Using filter patterns to match terms in log events](#matching-terms-events)
+ [Using metric filters to match terms and extract values from JSON log events](#metric-filters-extract-json)
+ [Using metric filters to extract values from space\-delimited log events](#extract-log-event-values)
+ [Configuring metric values for a metric filter](#changing-default-increment-value)
+ [Publishing dimensions with metrics from values in JSON or space\-delimited log events](#logs-metric-filters-dimensions)
+ [Using values in log events to increment a metric's value](#publishing-values-found-in-logs)

## Using filter patterns to match terms in log events<a name="matching-terms-events"></a>

Filter patterns make up the syntax that metric filters use to match terms in log events\. Terms can be words, exact phrases, or numeric values\. Create filter patterns with the terms that you want to match\. Filter patterns only return the log events that contain the terms you define\. You can test filter patterns in the CloudWatch console\. The following examples contain code snippets that show how you can use filter patterns to match terms in your log events\.

**Note**  
Filter patterns are case sensitive\. Enclose exact phrases and terms that include non\-alphanumeric characters in double quotation marks \(**""**\)\.

**Example: Match a single term**

The following code snippet shows an example of a single\-term filter pattern that returns all log events where messages contain the word ***ERROR***\.

```
ERROR
```

The filter pattern matches log event messages, such as the following:
+ `[ERROR 400] BAD REQUEST`
+ `[ERROR 401] UNAUTHORIZED REQUEST`
+ `[ERROR 419] MISSING ARGUMENTS`
+ `[ERROR 420] INVALID ARGUMENTS`

**Example: Match multiple terms**

The following code snippet shows an example of a multiple\-term filter pattern that returns all log events where messages contain the words ***ERROR*** and ***ARGUMENTS***\.

```
ERROR ARGUMENTS
```

The filter returns log event messages, such as the following:
+ `[ERROR 419] MISSING ARGUMENTS`
+ `[ERROR 420] INVALID ARGUMENTS`

The filter pattern doesn't return the following log event messages because they don't contain both of the terms specified in the filter pattern\.
+ `[ERROR 400] BAD REQUEST`
+ `[ERROR 401] UNAUTHORIZED REQUEST`

**Example: Match single and multiple terms**

You can use pattern matching to create filter patterns that return log events containing single and multiple terms\. Place a question mark \("?"\) before the terms that you want to match\. The following code snippet shows an example of a filter pattern that returns all log events where messages contain the word ***ERROR*** or ***ARGUMENTS*** and the words ***ERROR*** and ***ARGUMENTS***\.

```
?ERROR ?ARGUMENTS
```

The filter pattern matches log event messages, such as the following:
+ `[ERROR 400] BAD REQUEST`
+ `[ERROR 401] UNAUTHORIZED REQUEST`
+ `[ERROR 419] MISSING ARGUMENTS`
+ `[ERROR 420] INVALID ARGUMENTS`

**Example: Match exact phrases**

The following code snippet shows an example of a filter pattern that returns log events where messages contain the exact phrase ***INTERNAL SERVER ERROR***\.

```
"INTERNAL SERVER ERROR"
```

The filter pattern returns the following log event message:
+ `[ERROR 500] INTERNAL SERVER ERROR`

**Example: Include and exclude terms**

You can create filter patterns that return log events where messages include some terms and exclude other terms\. Place a minus symbol \(**"\-"**\) before the terms that you want to exclude\. The following code snippet shows an example of a filter pattern that returns log events where messages include the term ***ERROR*** and exclude the term ***ARGUMENTS***\.

```
ERROR -ARGUMENTS
```

The filter pattern returns log event messages, such as the following:
+ `[ERROR 400] BAD REQUEST`
+ `[ERROR 401] UNAUTHORIZED REQUEST`

The filter pattern doesn't return the following log event messages because they contain the word ***ARGUMENTS***\.
+ `[ERROR 419] MISSING ARGUMENTS`
+ `[ERROR 420] INVALID ARGUMENTS`

**Example: Match everything**

You can match everything in your log events with double quotation marks\. The following code snippet shows an example of a filter pattern that returns all log events\.

```
" "
```

## Using metric filters to match terms and extract values from JSON log events<a name="metric-filters-extract-json"></a>

Metric filters are configurations that include filter patterns\. You can create metric filters to match terms in your log events and convert log data into metrics\. When your metric filter matches a term, you can increment the metric's count\. Metric filters only match the terms that you define in your filter pattern\. You can test metric filters in the CloudWatch console\. You also can create metric filters to match terms and extract values from JSON log events\. The following examples describe the syntax for metric filters that match JSON terms containing strings and numeric values\.

**Example: Metric filters that match strings**

You can create metric filters to match strings in JSON log events\. The following code snippet shows an example of the syntax for string\-based metric filters\.

```
{ PropertySelector EqualityOperator String }
```

Enclose metric filters in curly braces \("\{\}"\)\. String\-based metric filters must contain the following parts:
+ **Property selector**

  Set off property selectors with a dollar sign followed by a period \("$\."\)\. Property selectors are alphanumeric strings that support hyphen \("\-"\) and underscore \("\_"\) characters\. Strings don't support scientific notation\. Property selectors point to value nodes in JSON log events\. Value nodes can be strings or numbers\. Place arrays after property selectors\. Arrays contain elements that follow a zero\-based ordering system \(0 = 1, 1 = 2, and so on\)\. Enclose elements in brackets \("\[\]"\)\. If a property selector points to an array or object, the metric filter won't match the log format\.
+ **Equality operator**

  Set off equality operators with one of the following symbols: equal \("="\) or not equal \("\!="\)\. Equality operators return a Boolean value \(true or false\)\.
+ **String **

  You can enclose strings in double quotation marks \(""\)\. Strings that contain types other than alphanumeric characters and the underscore symbol must be placed in double quotation marks\. Use the asterisk \("\*"\) as a wild card to match text\.

The following code snippet contains an example of a metric filter showing how you can format a metric filter to match a JSON term with a string\.

```
{ $.eventType = "UpdateTrail" }
```

**Example: Metric filters that match numeric values**

You can create metric filters to match numeric values in JSON log events\. The following code snippet shows an example of the syntax for metric filters that match numeric values\.

```
{ PropertySelector NumericOperator Number }
```

Enclose metric filters in curly braces \("\{\}"\)\. Metric filters that match numeric values must have the following parts:
+ **Property selector**

  Set off property selectors with a dollar sign followed by a period \("$\."\)\. Property selectors are alphanumeric strings that support hyphen \("\-"\) and underscore \("\_"\) characters\. Strings don't support scientific notation\. Property selectors point to value nodes in JSON log events\. Value nodes can be strings or numbers\. Place arrays after property selectors\. Arrays contain elements that follow a zero\-based ordering system \(0 = 1, 1 = 2, and so on\)\. Enclose elements in brackets \("\[\]"\)\. If a property selector points to an array or object, the metric filter won't match the log format\.
+ **Numeric operator**

  Set off numeric operators with one of the following symbols: greater than \(">"\), less than \("<"\), equal \("="\), not equal \("\!="\), greater than or equal to \(">="\), or less than or equal to \("<="\)\.
+ **Number**

  You can use integers that contain plus \("\+"\) or minus \("\-"\) symbols and follow scientific notation\. Use the asterisk \("\*"\) as a wild card to match numbers\.

The following code snippet contains examples showing how you can format metric filters to match JSON terms with numeric values\.

```
// Metric filter with greater than symbol       
{ $.bandwidth > 75 }      
// Metric filter with less than symbol
{ $.latency < 50 }
// Metric filter with greater than or equal to symbol
{ $.refreshRate >= 60 } 
// Metric filter with less than or equal to symbol
{ $.responseTime <= 5 }
// Metric filter with equal sign
{ $.errorCode = 400} 
// Metric filter with not equal sign and scientific notation
{ $.errorCode != 500 }
// Metric filter with scientific notation and plus symbol
{ $.number[0] = 1e-3 } 
// Metric filter with scientific notation and minus symbol
{ $.number[0] != 1e+3 }
```

### Matching terms in JSON log events<a name="match-items-metric-filters"></a>

The following examples contain code snippets that show how metric filters can match terms in a JSON log event\.

**Example: JSON log event**

```
{
      "eventType": "UpdateTrail",
      "sourceIPAddress": "111.111.111.111",
      "arrayKey": [
            "value",
            "another value"
      ],
      "objectList": [
           {
             "name": "a",
             "id": 1
           },
           {
             "name": "b",
             "id": 2
           }
      ],
      "SomeObject": null
}
```

**Note**  
If you test the example metric filters with the example JSON log event, you must enter the example JSON log on a single line\.

**Example: Metric filter that matches string**

The metric filter matches the string `"UpdateTrail"` in the property `"eventType"`\.

```
{ $.eventType = "UpdateTrail" }
```

**Example: Metric filter that matches number**

The metric filter contains a wild card and matches the property `"sourceIPAddress"` because it doesn't contain a number with the prefix `"123.123"`\.

```
{ $.sourceIPAddress != 123.123.* }
```

**Example: Metric filter that matches element in array**

The metric filter matches the element `"value"` in the array `"arrayKey"`\.

```
{ $.arrayKey[0] = "value" }
```

**Example: Metric filter that matches an object in array**

The metric filter matches the object `"id":2` in the array `"objectList"`\.

```
{ $.objectList[1].id = 2 }
```

**Example: Metric filter that matches JSON logs using `IS`**

You can create metric filters that match fields in JSON logs with the `IS` variable\. The `IS` variable can match fields that contain the values `NULL`, `TRUE`, or `FALSE`\. The following metric filter returns JSON logs where the value of `SomeObject` is `NULL`\.

```
{ $.SomeObject IS NULL }
```

**Example: Metric filter that matches JSON logs using `NOT EXISTS`**

You can create metric filters with the `NOT EXISTS` variable to return JSON logs that don't contain specific fields in the log data\. The following metric filter uses `NOT EXISTS` to return JSON logs that don't contain the field `SomeOtherObject`\.

```
{ $.SomeOtherObject NOT EXISTS }
```

**Note**  
The variables `IS NOT` and `EXISTS` currently aren't supported\.

### Using compound expressions to match terms in JSON objects<a name="compound-conditions"></a>

You can use the logical operators AND \("&&"\) and OR \("\|\|"\) in metric filters to create compound expressions that match log events where two or more conditions are true\. Compound expressions support the use of parentheses \("\(\)"\) and the following standard order of operations: \(\) > && > \|\|\. The following examples contain code snippets that show how you can use metric filters with compound expressions to match terms in a JSON object\.

**Example: JSON object**

```
{
    "user": {
        "id": 1, 
        "email": "John.Stiles@example.com"
    },
    "users": [
        {
         "id": 2,
         "email": "John.Doe@example.com"
        },
        {
         "id": 3,
         "email": "Jane.Doe@example.com"
        }
    ],
    "actions": [
        "GET",
        "PUT",
        "DELETE"
    ],
    "coordinates": [
        [0, 1, 2],
        [4, 5, 6],
        [7, 8, 9]
    ]
}
```

**Example: Expression that matches using AND \(&&\)**

The metric filter contains a compound expression that matches `"id"` in `"user"` with a numeric value of `1` and `"users"` in `"email"` with the string `"John.Doe@example.com"`\.

```
{ ($.user.id = 1) && ($.users[0].email = "John.Doe@example.com") }
```

**Example: Expression that matches using OR \(\|\|\)**

The metric filter contains a compound expression that matches `"email"` in `"user"` with the string `"John.Stiles@example.com"`\.

```
{ $.user.email = "John.Stiles@example.com" || $.coordinates[0][1] = "nonmatch" && $.actions[2] = "nonmatch" }
```

**Example: Expression that doesn't match using AND \(&&\)**

The metric filter contains a compound expression that doesn't find a match because the expression doesn't match the first and second coordinates in `"coordinates"` and the third action in `"actions"`\.

```
{ ($.user.email = "John.Stiles@example.com" || $.coordinates[0][1] = "nonmatch") && $.actions[2] = "nonmatch" }
```

**Example: Expression that doesn't match using OR \(\|\|\)**

The metric filter contains a compound expression that doesn't find a match because the expression doesn't match the first property in `"users"` or the third action in `"actions"`\.

```
{ ($.user.id = 2 && $.users[0].email = "nonmatch") || $.actions[2] = "GET" }
```

## Using metric filters to extract values from space\-delimited log events<a name="extract-log-event-values"></a>

You can create metric filters that map to and extract values from fields in space\-delimited log events\. The following examples contain code snippets that show a space\-delimited log event, a metric filter that maps to the fields in the space\-delimited log event, and the values that the metric filter extracts from the fields in the space\-delimited log event\.

**Example: Space\-delimited log event**

The following code snippet shows a space\-delimited log event that contains seven fields: `ip`, `user`, `username`, `timestamp`, `request`, `status_code`, and `bytes`\.

```
127.0.0.1 Prod frank [10/Oct/2000:13:25:15 -0700] "GET /index.html HTTP/1.0" 404 1534
```

**Note**  
Characters between brackets \("\[\]"\) and double quotation marks \(""\) are considered single fields\.

**Example: Metric filter**

To create a metric filter that maps to and extracts values from fields in a space\-delimited log event, enclose the metric filter in brackets \("\[\]"\), and specify fields with names that are separated by commas \(","\)\. The following metric filter parses seven fields\. 

```
[ip, user, username, timestamp, request =*.html*, status_code = 4*, bytes]
```

You can use numeric operators \( >, <, =, \!=, >>=, or <=\) and the asterisk \(\*\) as a wild card to give your metric filter conditions\. In the example metric filter, `request` contains a wild card that states it must extract a value with `.html`, and `status_code` contains a wild card that states it must extract a value beginning with `4`\.

If you don't know the number of fields that you're parsing in a space\-delimited log event, you can use ellipsis \(\.\.\.\) to reference any unnamed field\. Elipsis can reference as many fields as needed\. The following example shows a metric filter with ellipsis that represent the first four unnamed fields shown in the previous example metric filter\.

```
[..., request =*.html*, status_code = 4*, bytes]
```

You also can use the logical operators AND \(&&\) and OR \(\|\|\) to create compound expressions\. The following metric filter contains a compound expression that states the value of `status_code` must be `404` or `410`\.

```
[ip, user, username, timestamp, request =*.html*, status_code = 404 || status_code = 410, bytes]
```

**Example: Extracted fields and values**

The following code snippet shows the values that the metric filter extracts from the fields in the space\-delimited log event\.

```
{
   "$bytes": "1534",
   "$status_code": "404", 
   "$request": "GET /index.html HTTP/1.0", 
   "$timestamp": "10/Oct/2000:13:25:15 -0700", 
   "$username": "frank",
   "$user": "Prod", 
   "$ip": "127.0.0.1"
}
```

### Using pattern matching to match terms in space\-delimited log events<a name="pattern-matching-space-delimited"></a>

You can use pattern matching to create space\-delimited metric filters that match terms in a specific order\. Specify the order of your terms with indicators\. Use **w1** to represent your first term and **w2** and so on to represent the order of your subsequent terms\. Place commas \(","\) between your terms\. The following examples contain code snippets that show how you can use pattern matching with space\-delimited metric filters\.

**Example: Match terms in order**

The following space\-delimited metric filter returns log events where the first word in the log events is ***ERROR***\.

```
[w1=ERROR, w2]
```

**Note**  
 When you create space\-delimited metric filters that use pattern matching, you must include a blank indicator after you specify the order of your terms\. For example, if you create a metric filter that returns log events where the first word is ***ERROR***, include a blank **w2** indicator after the **w1** term\. 

**Example: Match terms with AND \(&&\) and OR \(\|\|\)**

You can use the logical operators AND \("&&"\) and OR \("\|\|"\) to create space\-delimited metric filters that contain conditions\. The following metric filter returns log events where the first word in the events is ***ERROR*** or ***WARNING***\.

```
[w1=ERROR || W1=WARNING, w2]
```

**Example: Exclude terms from matches**

You can create space\-delimited metric filters that return log events excluding one or more terms\. Place a not equal symbol \("\!="\) before the term or terms that you want to exclude\. The following code snippet shows an example of a metric filter that returns log events where the first words aren't ***ERROR*** and ***WARNING***\.

```
[w1!=ERROR && w1!=WARNING, w2]
```

## Configuring metric values for a metric filter<a name="changing-default-increment-value"></a>

When you create a metric filter, you define your filter pattern and specify your metric's value and default value\. You can set metric values to numbers, named identifiers, or numeric identifiers\. If you don't specify a default value, CloudWatch won't report data when your metric filter doesn't find a match\. We recommend that you specify a default value, even if the value is 0\. Setting a default value helps CloudWatch report data more accurately and prevents CloudWatch from aggregating spotty metrics\. CloudWatch aggregates and reports metric values every minute\.

 When your metric filter finds a match in your log events, it increments your metric's count by your metric's value\. If your metric filter doesn't find a match, CloudWatch reports the metric's default value\. For example, your log group publishes two records every minute, the metric value is 1, and the default value is 0\. If your metric filter finds matches in both log records within the first minute, the metric value for that minute is 2\. If your metric filter doesn't find matches in either records during the second minute, the default value for that minute is 0\. If you assign dimensions to metrics that metric filters generate, you can't specify default values for those metrics\.

You also can set up a metric filter to increment a metric with a value extracted from a log event, instead of a static value\. For more information, see [Using values in log events to increment a metric's value](#publishing-values-found-in-logs)\.

## Publishing dimensions with metrics from values in JSON or space\-delimited log events<a name="logs-metric-filters-dimensions"></a>

You can use the CloudWatch console or AWS CLI to create metric filters that publish dimensions with metrics that JSON and space\-delimited log events generate\. Dimensions are name/value value pairs  and only available for JSON and space\-delimited filter patterns\. You can create JSON and space\-delimited metric filters with up to three dimensions\. For more information about dimensions and information about how to assign dimensions to metrics, see the following sections:
+ [Dimensions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html#Dimension) in the *Amazon CloudWatch User guide*
+ [Example: Extract fields from an Apache log and assign dimensions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/ExtractBytesExample.html) in the *Amazon CloudWatch Logs User Guide*

**Important**  
Dimensions contain values that gather charges the same as custom metrics\. To prevent unexpected charges, don't specify high\-cardinality fields, such as `IPAddress` or `requestID`, as dimensions\.  
If you extract metrics from log events, you're charged for custom metrics\. To prevent you from collecting accidental high charges, Amazon might disable your metric filter if it generates 1000 different name/value pairs for specified dimensions over a certain amount of time\.   
You can create billing alarms that notify you of your estimated charges\. For more information, see [ Creating a billing alarm to monitor your estimated AWS charges](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/monitor_estimated_charges_with_cloudwatch.htmll)\.

### Publishing dimensions with metrics from JSON log events<a name="logs-metric-filters-JSON"></a>

The following examples contain code snippets that describe how to specify dimensions in a JSON metric filter\.

**Example: JSON log event**

```
{
  "eventType": "UpdateTrail",
  "sourceIPAddress": "111.111.111.111",
  "arrayKey": [
        "value",
        "another value"
  ],
  "objectList": [
       {"name": "a",
         "id": 1
       },
       {"name": "b",
         "id": 2
       }
  ]
  
}
```

**Note**  
If you test the example metric filter with the example JSON log event, you must enter the example JSON log on a single line\.

**Example: Metric filter**

The metric filter increments the metric whenever a JSON log event contain the properties `eventType` and `"sourceIPAddress"`\.

```
{ $.eventType = "*" && $.sourceIPAddress != 123.123.* }
```

When you create a JSON metric filter, you can specify any of the properties in the metric filter as a dimension\. For example, to set `eventType` as a dimension, use the following:

```
"eventType" : $.eventType
```

The example metric contains a dimension that's named `"eventType"`, and the dimension's value in the example log event is `"UpdateTrail"`\.

### Publishing dimensions with metrics from space\-delimited log events<a name="logs-metric-filters-dimensions-space-delimited"></a>

The following examples contain code snippets that describe how to specify dimensions in a space\-delimited metric filter\.

**Example: Space\-delimited log event**

```
127.0.0.1 Prod frank [10/Oct/2000:13:25:15 -0700] "GET /index.html HTTP/1.0" 404 1534
```

**Example: Metric filter**

```
[ip, server, username, timestamp, request, status_code, bytes > 1000]
```

The metric filter increments the metric when a space\-delimited log event includes any of the fields that are specified in the filter\. For example, the metric filter finds following fields and values in the example space\-delimited log event\.

```
{
   "$bytes": "1534", 
   "$status_code": "404", 

   "$request": "GET /index.html HTTP/1.0", 
   "$timestamp": "10/Oct/2000:13:25:15 -0700", 
   "$username": "frank",
   "$server": "Prod", 
   "$ip": "127.0.0.1"
}
```

When you create a space\-delimited metric filter, you can specify any of the fields in the metric filter as a dimension\. For example, to set `server` as a dimension, use the following:

```
"server" : $server
```

The example metric filter has a dimension that's named `server`, and the dimension's value in the example log event is `"Prod"`\.

## Using values in log events to increment a metric's value<a name="publishing-values-found-in-logs"></a>

You can create metric filters that publish numeric values found in your log events\. The procedure in this section uses the following example metric filter to show how you can publish a numeric value in a JSON log event to a metric\.

```
{ $.latency = * } metricValue: $.latency
```

**To create a metric filter that publishes a value in a log event**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Logs**, and then choose **Log groups**\.

1. Select or create a log group\.

   For information about how to create a log group, see [Create a log group in CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html) in the *Amazon CloudWatch Logs User Guide*\.

1. Choose **Actions**, and then choose **Create metric filter**\.

1. For **Filter Pattern**, enter **\{ $\.latency = \* \}**, and then choose **Next**\.

1. For **Metric Name**, enter **myMetric**\.

1. For **Metric Value**, enter **$\.latency**\.

1. \(Optional\) For **Default Value**, enter **0**, and then choose **Next**\.

   We recommend that you specify a default value, even if the value is 0\. Setting a default value helps CloudWatch report data more accurately and prevents CloudWatch from aggregating spotty metrics\. CloudWatch aggregates and reports metric values every minute\.

1. Choose **Create metric filter**\.

The example metric filter matches the term `"latency"` in the example JSON log event and publishes a numeric value of 50 to the metric **myMetric**\.

```
{
"latency": 50,
"requestType": "GET"
}
```