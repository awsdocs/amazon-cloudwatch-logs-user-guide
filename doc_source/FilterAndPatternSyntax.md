# Filter and Pattern Syntax<a name="FilterAndPatternSyntax"></a>

You can use metric filters to search for and match terms, phrases, or values in your log events\. When a metric filter finds one of the terms, phrases, or values in your log events, you can increment the value of a CloudWatch metric\. For example, you can create a metric filter to search for and count the occurrence of the word *ERROR* in your log events\. 

Metric filters can also extract numerical values from space\-delimited log events, such as the latency of web requests\. In these examples, you can increment your metric value by the actual numerical value extracted from the log\.

You can also use conditional operators and wildcards to create exact matches\. Before you create a metric filter, you can test your search patterns in the CloudWatch console\. The following sections explain the metric filter syntax in more detail\.

## Matching Terms in Log Events<a name="matching-terms-events"></a>

To search for a term in your log events, use the term as your metric filter pattern\. You can specify multiple terms in a metric filter pattern, but all terms must appear in a log event for there to be a match\. Metric filters are case sensitive\.

Metric filter terms that include characters other than alphanumeric or underscore must be placed inside double quotes \(""\)\.

To exclude a term, use a minus sign \(\-\) before the term\.

**Example 1: Match everything**  
The filter pattern "" matches all log events\.

**Example 2: Single term**  
The filter pattern "ERROR" matches log event messages that contain this term, such as the following:
+ \[ERROR\] A fatal exception has occurred
+ Exiting with ERRORCODE: \-1

**Example 3: Include a term and exclude a term**  
In the previous example, if you change the filter pattern to "ERROR" \- "Exiting", the log event message "Exiting with ERRORCODE: \-1" would be excluded\.

**Example 4: Multiple terms**  
The filter pattern "ERROR Exception" matches log event messages that contain both terms, such as the following:
+ \[ERROR\] Caught IllegalArgumentException
+ \[ERROR\] Unhandled Exception

The filter pattern "Failed to process the request" matches log event messages that contain all terms, such as the following:
+ \[WARN\] Failed to process the request
+ \[ERROR\] Unable to continue: Failed to process the request

### OR Pattern Matching<a name="log-filters-or-pattern-matching"></a>

You can match terms in text\-based filters using OR pattern matching\. Use a question mark for OR, such as `?term`\.

Look at the three log event examples below\. `ERROR` matches examples 1 and 2\. `?ERROR ?WARN` matches examples 1, 2, and 3, as all of them include either the word ERROR or the word WARN\. `ERROR WARN` only matches example 1, as it is the only one containing both of those words\. ERROR \-WARN matches example 2, as it matches a string that contains ERROR but does not contain WARN\.

1. `ERROR WARN message`

1. `ERROR message`

1. `WARN message`

You can match terms using OR pattern matching in space\-delimited filters\. With space\-delimited filters, `w1` means the first word in the log event, `w2` means the second word, and so on\. For the example patterns below, \[w1=ERROR, w2\] matches pattern 2 because ERROR is the first word, and \[w1=ERROR \|\| w1=WARN, w2\] matches patterns 2 and 3\. \[w1\!=ERROR&&w1\!=WARN, w2\] matches lines containing both ERROR and WARN \(pattern 1\)\.

1. ERROR WARN message

1. ERROR message

1. WARN message

You can match terms using OR pattern matching in JSON filters\. For the example patterns below, \{$\.foo = bar\} matches pattern 1, \{$\.foo = baz \} matches pattern 2, and \{$\.foo = bar \|\| $\.foo = baz \} matches pattern 1 and 2\.

1. \{"foo": "bar"\}

1. \{"foo": "baz"\}

### Matching Terms in JSON Log Events<a name="matching-terms-events-json"></a>

You can extract values from JSON log events\. To extract values from JSON log events, you need to create a string\-based metric filter\. Strings containing scientific notation are not supported\. The items in the JSON log event data must exactly match the metric filter\. You might want to create metric filters in JSON log events to indicate the following:
+ A certain event occurs\. For example eventName is "UpdateTrail"\.
+ The IP is outside a known subnet\. For example, sourceIPAddress is not in some known subnet range\.
+ A combination of two or more other conditions are true\. For example, the eventName is "UpdateTrail" and the recipientAccountId is 123456789012\.

#### Using Metric Filters to Extract Values from JSON Log Events<a name="extract-json-log-event-values"></a>

You can use metric filters to extract values from JSON log events\. A metric filter checks incoming logs and modifies a numeric value when the filter finds a match in the log data\. When you create a metric filter, you can simply increment a count each time the matching text is found in a log, or you can extract numerical values from the log and use those to increment the metric value\.

##### Matching JSON Terms Using Metric Filters<a name="metric-filter-term-matching"></a>

The metric filter syntax for JSON log events uses the following format:

```
{ SELECTOR EQUALITY_OPERATOR STRING }
```

The metric filter must be enclosed in curly braces \{ \}, to indicate this is a JSON expression\. The metric filter contains the following parts:

**SELECTOR**  
Specifies what JSON property to check\. Property selectors always start with dollar sign \($\), which signifies the root of the JSON\. Property selectors are alphanumeric strings that also support '\-' and '\_' characters\. Array elements are denoted with \[NUMBER\] syntax, and must follow a property\. Examples are: $\.eventId, $\.users\[0\], $\.users\[0\]\.id, $\.requestParameters\.instanceId\.

**EQUALITY\_OPERATOR**  
Can be either = or \!=\.

**STRING**  
A string with or without quotes\. You can use the asterisk '\*' wildcard character to match any text at, before, or after a search term\. For example, **\*Event** will match **PutEvent** and **GetEvent**\. **Event\*** will match **EventId** and **EventName**\. **Ev\*ent** will only match the actual string **Ev\*ent**\. Strings that consist entirely of alphanumeric characters do not need to be quoted\. Strings that have unicode and other characters such as ‘@,‘ ‘$,' ‘\\,' etc\. must be enclosed in double quotes to be valid\.

##### JSON Metric Filter Examples<a name="metric-filter-examples"></a>

The following is a JSON example:

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
  "SomeObject": null,
  "ThisFlag": true
}
```

The following filters would match:

```
{ $.eventType = "UpdateTrail" }
```

Filter on the event type being UpdateTrail\.

```
{ $.sourceIPAddress != 123.123.* }
```

Filter on the IP address being outside the subnet 123\.123 prefix\.

```
{ $.arrayKey[0] = "value" }
```

Filter on the first entry in arrayKey being "value"\. If arrayKey is not an array this will be false\.

```
{ $.objectList[1].id = 2 }
```

Filter on the second entry in objectList having a property called id = 2\. If objectList is not an array this will be false\. If the items in objectList are not objects or do not have an id property, this will be false\.

```
{ $.SomeObject IS NULL }
```

Filter on SomeObject being set to null\. This will only be true is the specified object is set to null\.

```
{ $.SomeOtherObject NOT EXISTS }
```

Filter on SomeOtherObject being non\-existent\. This will only be true if specified object does not exist in log data\.

```
{ $.ThisFlag IS TRUE }
```

Filters on ThisFlag being TRUE\. This also works for boolean filters which check for FALSE value\.

##### JSON Compound Conditions<a name="compound-conditions"></a>

You can combine multiple conditions into a compound expression using OR \(\|\|\) and AND \(&&\)\. Parenthesis are allowed and the syntax follows standard order of operations \(\) > && > \|\|\.

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

##### Examples<a name="w28aac15c13b9c28b6b6b6"></a>

```
{ ($.user.id = 1) && ($.users[0].email = "John.Doe@example.com") }
```

Matches the JSON above\.

```
{ ($.user.id = 2 && $.users[0].email = "nonmatch") || $.actions[2] = "GET" }
```

Doesn't match the JSON above\.

```
{ $.user.email = "John.Stiles@example.com" || $.coordinates[0][1] = nonmatch && $.actions[2] = nomatch }
```

Matches the JSON above\.

```
{ ($.user.email = "John.Stiles@example.com" || $.coordinates[0][1] = nonmatch) && $.actions[2] = nomatch }
```

Doesn't match the JSON above\.

##### JSON Special Considerations<a name="w28aac15c13b9c28b6b6b8"></a>

The SELECTOR must point to a value node \(string or number\) in the JSON\. If it points to an array or object, the filter will not be applied because the log format doesn't match the filter\. For example, both \{$\.users = 1\} and \{$\.users \!= 1\} will fail to match a log event where users is an array:

```
{
  "users": [1, 2, 3]
}
```

##### Numeric Comparisons<a name="w28aac15c13b9c28b6b6c10"></a>

The metric filter syntax supports precise matching on numeric comparisons\. The following numeric comparisons are supported: <, >, >=, <=, =, \!=

Numeric filters have a syntax of

```
{ SELECTOR NUMERIC_OPERATOR NUMBER }
```

The metric filter must be enclosed in curly braces \{ \}, to indicate this is a JSON expression\. The metric filter contains the following parts:

**SELECTOR**  
Specifies what JSON property to check\. Property selectors always start with dollar sign \($\), which signifies the root of the JSON\. Property selectors are alphanumeric strings that also support '\-' and '\_' characters\. Array elements are denoted with \[NUMBER\] syntax, and must follow a property\. Examples are: $\.latency, $\.numbers\[0\], $\.errorCode, $\.processes\[4\]\.averageRuntime\.

**NUMERIC\_OPERATOR**  
Can be one of the following: =, \!=, <, >, <=, or >=\.

**NUMBER**  
An integer with an optional \+ or \- sign, a decimal with an optional \+ or \- sign, or a number in scientific notation, which is an integer or a decimal with an optional \+ or \- sign, followed by 'e', followed by an integer with an optional \+ or \- sign\.

Examples:

```
{ $.latency >= 500 }
{ $.numbers[0] < 10e3 }
{ $.numbers[0] < 10e-3 }
{ $.processes[4].averageRuntime <= 55.5 }
{ $.errorCode = 400 }
{ $.errorCode != 500 }
{ $.latency > +1000 }
```

### Using Metric Filters to Extract Values from Space\-Delimited Log Events<a name="extract-log-event-values"></a>

You can use metric filters to extract values from space\-delimited log events\. The characters between a pair of square brackets \[\] or two double quotes \(""\) are treated as a single field\. For example:

```
127.0.0.1 - frank [10/Oct/2000:13:25:15 -0700] "GET /apache_pb.gif HTTP/1.0" 200 1534
127.0.0.1 - frank [10/Oct/2000:13:35:22 -0700] "GET /apache_pb.gif HTTP/1.0" 500 5324
127.0.0.1 - frank [10/Oct/2000:13:50:35 -0700] "GET /apache_pb.gif HTTP/1.0" 200 4355
```

To specify a metric filter pattern that parses space\-delimited events, the metric filter pattern has to specify the fields with a name, separated by commas, with the entire pattern enclosed in square brackets\. For example: \[ip, user, username, timestamp, request, status\_code, bytes\]\.

In cases where you don't know the number of fields, you can use shorthand notification using an ellipsis \(…\)\. For example:

```
[..., status_code, bytes]
[ip, user, ..., status_code, bytes]
[ip, user, ...]
```

You can also add conditions to your fields so that only log events that match all conditions would match the filters\. For example:

```
[ip, user, username, timestamp, request, status_code, bytes > 1000]
[ip, user, username, timestamp, request, status_code = 200, bytes]
[ip, user, username, timestamp, request, status_code = 4*, bytes]
[ip, user, username, timestamp, request = *html*, status_code = 4*, bytes]
```

You can use `&&` as a logical AND operator and `||` as a logical OR operator, as in the following examples:

```
[ip, user, username, timestamp, request, status_code = 4* && bytes > 1000]
[ip, user, username, timestamp, request, status_code = 403 || status_code = 404, bytes]
```

CloudWatch Logs supports both string and numeric conditional fields\. For string fields, you can use = or \!= operators with an asterisk \(\*\)\.

For numeric fields, you can use the >, <, >=, <=, =, and \!= operators\.

If you are using a space\-delimited filter, extracted fields map to the names of the space\-delimited fields \(as expressed in the filter\) to the value of each of these fields\. If you are not using a space\-delimited filter, this will be empty\.

Example Filter:

```
[..., request=*.html*, status_code=4*,]
```

Example log event for the filter:

```
127.0.0.1 - frank [10/Oct/2000:13:25:15 -0700] \"GET /index.html HTTP/1.0\" 404 1534
```

Extracted fields for the log event and filter pattern:

```
{
   "$status_code": "404", 
   "$request": "GET /products/index.html HTTP/1.0", 
   "$7": "1534", 
   "$4": "10/Oct/2000:13:25:15 -0700", 
   "$3": "frank",
   "$2": "-", 
   "$1": "127.0.0.1"
}
```

## Setting How the Metric Value Changes When Matches Are Found<a name="changing-default-increment-value"></a>

When a metric filter finds one of the matching terms, phrases, or values in your log events, it increments the count in the CloudWatch metric by the amount you specify for Metric Value\. The metric value is aggregated and reported every minute\.

If logs are ingested during a one\-minute time period but no matches are found, the value specified for Default Value \(if any\) is reported\. However, if no log events are ingested during a one\-minute period, then no value is reported\.

Specifying a Default Value, even if that value is 0, helps ensure that data is reported more often, helping prevent spotty metrics when matches are not found\.

For example, suppose there is a log group that publishes two records every minute and the Metric Value is 1 and the Default Value is 0\. If matches are found in the both log records in the first minute, the metric value for that minute is 2\. If there are no matches in the log records published in the second minute, the Default Value of 0 is used for both log records and the metric value for that minute is 0\.

If you don't specify a Default Value, then no data is reported for any periods where no pattern matches are found\.

## Publishing Numerical Values Found in Log Entries<a name="publishing-values-found-in-logs"></a>

Instead of just counting the number of matching items found in logs, you can also use the metric filter to publish values based on numerical values found in the logs\. The following procedure shows how to publish a metric with the latency found in the JSON request `metricFilter: { $.latency = * } metricValue: $.latency`\.

**To publish a metric with the latency in a JSON request**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Log groups**\.

1. Choose `Actions`, **Create metric filter**\.

1. For **Filter Pattern**, type **\{ $\.latency = \* \}**, and then choose **Next**\.

1. For **Metric Name**, type **myMetric**\.

1. For **Metric Value**, enter **$\.latency**\.

1. For **Default Value** enter 0, and then choose **Next**\. Specifying a default value ensures that data is reported even during periods when no log events match the filter\. This prevents spotty or missing metrics when logs are ingested but don't match the filter\.

1. Choose **Create metric filter**\.

The following log event would publish a value of 50 to the metric **myMetric** following filter creation\.

```
{
"latency": 50,
"requestType": "GET"
}
```