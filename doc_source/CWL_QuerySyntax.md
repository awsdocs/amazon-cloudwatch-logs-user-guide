# CloudWatch Logs Insights query syntax<a name="CWL_QuerySyntax"></a>

CloudWatch Logs Insights supports a query language that you can use to query your log groups\. The query syntax supports different functions and operations that include but aren't limited to general functions, arithmetic and comparison operations, and regular expressions\. You can create queries that contain multiple query commands\. Separate query commands in your queries with Unix\-style pipe characters \(\|\)\. For more information about the query syntax, see [Supported operations and functions](https://docs.aws.amazon.com/en_us/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html#CWL_QuerySyntax-operations-functions)\. 

CloudWatch Logs Insights supports the use of comments in queries\. CloudWatch Logs Insights ignores lines that start with the hash symbol \(\#\)\. CloudWatch Logs Insights automatically discovers fields for many log types and generates fields that start with the @ symbol\. For more information about the fields that CloudWatch Logs automatically generates, see [ Supported logs and discovered fields](https://docs.aws.amazon.com/en_us/AmazonCloudWatch/latest/logs/CWL_AnalyzeLogData-discoverable-fields.html) in the *Amazon CloudWatch User Guide*\. 

## CloudWatch Logs Insights query commands<a name="CWL_QuerySyntax-commnds"></a>

The following table lists the supported query commands for CloudWatch Logs Insights and includes basic examples\. For examples of general queries and queries for other log types, see [Sample queries](CWL_QuerySyntax-examples.md) in the *Amazon CloudWatch Logs User Guide*\.


| Command | Description | Example\(s\) | 
| --- | --- | --- | 
|  `display` |  Specifies which fields to display in the query results\. If you specify this command more than once in your query, only the fields you specify in the last occurrence are used\.  | The following example query contains the field `@message`\. The `parse` command extracts values from the message log format and creates the ephemeral fields `loggingType` and `loggingMessage`\. The query filters log events to only those with `ERROR` as the value for `loggingType` and displays only the `loggingMessage` of those events in the results\. <pre>fields @message<br />    | parse @message "[*] *" as loggingType, loggingMessage<br />    | filter loggingType = "ERROR"<br />    | display loggingMessage<br />    </pre>  | 
|  `fields` |  Retrieves the specified fields from log events for display\.  You can use functions and operations within a fields command to modify field values for display and to create new fields for use in the rest of the query\.  | The following example displays the fields `foo-bar`, `action`, and the absolute value of the difference between `f3` and `f4` for all log events in the log group\. <pre>fields `foo-bar`, action, abs(f3-f4)</pre> The following example creates and displays an ephemeral field `opStatus`\. The value of `opStatus` for each log entry is the concatenation of the values of the `Operation` and `StatusCode` fields, with a hyphen in between those values\. <pre>fields concat(Operation, '-', StatusCode) as opStatus</pre>  | 
|  `filter` |  Filters the results of a query that's based on one or more conditions\. The `filter` command supports a variety of operators and expressions\. For more information, see [Matches and regular expressions in the filter command](#CWL_QuerySyntax-regex)\.   |  The following example retrieves the `f1`, `f2`, and `f3` fields for all log events with a value greater than 2000 in the `duration` field\. <pre>fields f1, f2, f3 | filter (duration>2000)</pre> The following example shows a similar query, but the query results don't display separate fields\. Instead, the query results display the `@timestamp` field and all log data in the `@message` field for all log events where duration is greater than 2000\. <pre>filter (duration>2000)</pre> The following example retrieves `f1` and `f2` fields for all log events where `f1` equals 10 or `f3` is greater than 25\. <pre>fields f1, f2 | filter (f1=10 or f3>25)</pre> The following example returns log events where the field `statusCode` contains a value between 200 and 299\. <pre>fields f1 | filter statusCode like /2\d\d/</pre> The next example returns log events where the field `statusCode` doesn't contain a value between 200 and 299\. <pre>fields f1 | filter statusCode not like /2\d\d/</pre> The following example returns log events that have a `statusCode` of "300", "400", or "500"\. <pre>fields @timestamp, @message <br />    | filter statusCode in [300,400,500]</pre> This final example returns log events that do not have `Type` fields with values of "foo", "bar", or "1"\.<pre>fields @timestamp, @message<br />    | filter Type not in ["foo","bar",1]</pre>  | 
|  `stats` |  Uses log field values to calculate aggregate statistics\. You can use `by` with the `stats` command to specify one or more criteria\. You can use the criteria to group statistical data\. The `stats` command supports the following operators: `sum()`, `avg()`, `count()`, `min()`, and `max()`\.  |  The following example calculates the average value of `f1` for each unique value of `f2`\. <pre>stats avg (f1) by f2</pre> The following example counts the number of exceptions per hour\. <pre>filter @message like /Exception/ <br />| stats count(*) as exceptionCount by bin(1h)<br />| sort exceptionCount desc                                  <br /></pre> In the example, `as` groups the total count for the number of times the word ***"Exception"*** occurs in messages under the the ephemeral field **exceptionCount**, and `by` groups the statistical data under the field **bin\(1h\)**\.  | 
|  `sort` |  Sorts the retrieved log events\. Both ascending \(`asc`\) and descending \(`desc`\) order are supported\.  |  The following example sorts the returned events in descending order based on the value of `f1`, and displays the fields `f1`, `f2`, and `f3`\. <pre>fields f1, f2, f3 | sort f1 desc</pre>  | 
|  `limit` |  Specifies the number of log events returned by the query\. You can use this to limit the results to a small number to see a small set of relevant results\. You can also use `limit` with a number between 1000 and 10,000 to increase the number of query result rows displayed in the console to an amount greater than the default of 1000 rows\. If you don't specify a limit, the query defaults to displaying a maximum of 1000 rows\. |  The following example sorts the events in descending order based on the value of `@timestamp`, and displays the fields `f1` and `f2` for the first 25 events by sort order\. In this case, the sort order is by timestamp starting with the most recent, so the most recent 25 events are returned\. <pre>sort @timestamp desc | limit 25  |  display f1, f2</pre>  | 
|  `parse` |  Extracts data from a log field and creates one or more ephemeral fields that you can process further in the query\. The `parse` command supports glob expressions and regular expressions\. For a glob expression, use a constant string \(characters enclosed in single or double quotation marks\) with the `parse` command\. Replace variable text with an asterisk \(\*\)\. The asterisk functions as a wild card\. Use the keyword `as` following the asterisk to extract wild card values into ephemeral fields and give them an alias\. Enclose regular expressions in forward slashes \(/\)\. In the expression, each part of the matched string that is to be extracted is enclosed in a named capturing group\. An example of a named capturing group is `(?<name>.*)`, where `name` is the name and `.*` is the pattern\.  |  Using this single log line as an example: <pre>25 May 2019 10:24:39,474 [ERROR] {foo=2, bar=data} The error was: DataIntegrityException</pre> The following two `parse` expressions each do the following: the ephemeral fields `level`, `config`, and `exception` are created\. `level` has a value of `ERROR`, `config` has a value of `{foo=2, bar=data}`, and `exception` has a value of `DataIntegrityException`\. The first example uses a glob expression, and the second uses a regular expression\. <pre>parse @message "[*] * The error was: *" as level, config, exception</pre> <pre>parse @message /\[(?<level>\S+)\]\s+(?<config>\{.*\})\s+The error was: (?<exception>\S+)/</pre> Using this single log line as an example: <pre>25 May 2019 10:24:39,474 user=user1234, method=sampleMehod, latency := 216</pre> The following example uses a regular expression to extract the ephemeral fields `user2`, `method2`, and `latency2` from the log field `@message` and returns the average latency for each unique combination of `method2` and `user2`\. <pre>parse @message /user=(?<user2>.*?), method:(?<method2>.*?), latency := (?<latency2>.*?)/ <br />    | stats avg(latency2) by method2, user2</pre>  | 

**Guidelines for working with query commands**

You must surround log fields named in queries that include characters other than the `@` symbol, period \(`.`\), and non\-alphanumeric characters in backtick keys \(```\)\. For example, the log field `foo-bar` must be enclosed in backtick kets \(``foo-bar``\) because it contains a non\-alphanumeric character, the hyphen \(`-`\)\.

Use the `display` command to show the field or fields that you want to see in your query results\. The `display` command only shows the fiels you specify\. If your query contains multiple `display` commands, the query results show only the field or fields that you specified in the final `display` command\.

You can use `fields` command with the keyword *as* to create ephemeral fields that use fields and functions in your log events\. For example, `fields ispresent as isRes` creates an ephemeral field named `isRes`, and the ephemaral field can be used in the rest of your query\.

The value of `isRes` equals 0 or 1, depending on whether `resolverArn` is a discovered field \. If your query contains multiple `fields` commands and doesn't include a `display` command, you'll display all of the fields that are specified in the `fields` commands\. 

## Matches and regular expressions in the filter command<a name="CWL_QuerySyntax-regex"></a>

The filter command supports the use of regular expressions\. You can use the following comparison operators \(`=`, `!=`, `<`, `<=`, `>`, `>=`\) and Boolean operators \(`and`, `or`, and `not`\)\.

**Note**  
We assume that you're familiar with regular expressions\. CloudWatch Logs Insights supports Hyperscan, a mutiple regular expression matching library\. For more information about Hyperscan, see the [Hyperscan website](https://www.hyperscan.io/)\.

You can use the keyword `in` to test for set memberhsip and check for elements in an array\. To check for elements in an array, put the array after `in`\. You can use the Boolean operator `not` with `in`\. You can create queries that use `in` to return log events where fields are string matches\. The fields must be complete strings\. For example, the following code snippet shows a query that uses `in` to return log events where the field `logGroup` is the complete string `example_group`\.

```
fields @timestamp, @message
| filter logGroup in ["example_group"]
```

You can use the keyword phrases `like` and `not like` to match substrings\. You can use the regular expression operator `=~` to match substrings\. To match a substring with `like` and `not like`, enclose the substring that you want to match in single or double quotation marks\. You can use regular expression patterns with `like` and `not like`\. To match a substritng with the regular expression operator, enclose the substring that you want to match in forward slashes\. The following examples contain code snippets that show how you can match substrings using the `filter` command\.

**Examples: Match substrings**

The following examples return log events where `f1` contains the word ***Exception***\. All three examples are case sensitive\. 

The first example matches a substring with `like`\.

```
fields f1, f2, f3 
| filter f1 like "Exception"
```

The second example matches a substring with `like` and a regular expression pattern\.

```
fields f1, f2, f3 
| filter f1 like /Exception/
```

The last example matches a substring with a regular expression\.

```
fields f1, f2, f3 
| filter f1 =~ /Exception/
```

**Example: Match substrings with wild cards**

You can use the asterisk symbol \(`*`\) as a wild card in regular expressions to match substrings\. The following example returns log events where `f1` contains words that begin with the letter ***E***\. The example is case sensitive\.

```
fields f1, f2, f3
| filter f3 like /E*/
```

**Note**  
You can place a period before the asterisk symbol \(**\.\***\) to create a greedy quantifier that returns as many matches as possible\.

**Example: Exclude substrings from matches**

The following example shows a query that returns log events where `f1` doesn't contain the word ***Exception***\. The example is case senstive\.

```
fields f1, f2, f3 
| filter f1 not like "Exception"
```

**Example: Match substrings with case insensitve patterns**

You can match substrings that are case insensitve with `like` and regular expressions\. Place the following parameter \(**?i**\) before the substring you want to match\. The following example shows a query that returns log events where `f1` contains the word ***Exception*** or ***exception***\.

```
fields f1, f2, f3 
| filter f1 like /(?i)Exception/
```

## Using aliases in queries<a name="CWL_QuerySyntax-alias"></a>

You can use `as` to create one or more aliases in a query\. Aliases are supported in the `fields`, `stats`, and `sort` commands\. 

You can create aliases for log fields and for the results of operations and functions\. 

**Examples**

The following examples show the use of aliases in query commands\.

```
fields abs(myField) as AbsoluteValuemyField, myField2
```

Returns the absolute value of `myField` as `AbsoluteValuemyField` and also returns the field `myField2`\.

```
stats avg(f1) as myAvgF1 | sort myAvgF1 desc
```

Calculates the average of the values of the `f1` as `myAvgF1` and returns them in descending order by that value\.

## Using comments in queries<a name="CWL_QuerySyntax-comments"></a>

You can comment out lines in a query by using the `#` character\. Lines that start with the `#` character are ignored\. This can be useful to document your query or to temporarily ignore part of a complex query for one call, without deleting that line\.

In the following example, the second line of the query is ignored\.

```
fields @timestamp, @message
    # | filter @message like /delay/
    | limit 20
```

## Supported operations and functions<a name="CWL_QuerySyntax-operations-functions"></a>

The query language supports many types of operations and functions, as shown in the following tables\.

**Comparison operations**

You can use comparison operations in the `filter` command and as arguments for other functions\. Comparison operations accept all data types as arguments and return a Boolean result\.

```
= != < <= > >=
```

**Boolean operators**

You can use the Boolean operators `and`, `or`, and `not`\. You can use these Boolean operators only in functions that return a Boolean value\.

**Arithmetic operations**

You can use arithmetic operations in the `filter` and `fields` commands and as arguments for other functions\. Arithmetic operations accept numeric data types as arguments and return numeric results\.


| Operation | Description | 
| --- | --- | 
|  `a + b` |  Addition  | 
|  `a - b` |  Subtraction  | 
|  `a * b` |  Multiplication  | 
|  `a / b` |  Division  | 
|  `a ^ b` |  Exponentiation\. `2 ^ 3` returns `8`  | 
|  `a % b` |  Remainder or modulus\. `10 % 3` returns `1`  | 

**Numeric operations**

You can use numeric operations in the `filter` and `fields` commands and as arguments for other functions\. Numeric operations accept numeric data types as arguments and return numeric results\.


| Operation | Result type | Description | 
| --- | --- | --- | 
|  `abs(a: number)` |  number |  Absolute value\.  | 
|  `ceil(a: number)` |  number |  Round to ceiling \(the smallest integer that is greater than the value of `a`\)\.  | 
|  `floor(a: number)` |  number |  Round to floor \(the largest integer that is smaller than the value of `a`\)\.  | 
|  `greatest(a: number, ...numbers: number[])` |  number |  Returns the largest value\.  | 
|  `least(a: number, ...numbers: number[])` |  number |  Returns the smallest value\.  | 
|  `log(a: number)` |  number |  Natural log\.  | 
|  `sqrt(a: number)` |  number |  Square root\.  | 

**General functions**

You can use general functions in the `filter` and `fields` commands and as arguments for other functions\. 


| Function | Result type | Description | 
| --- | --- | --- | 
|  `ispresent(fieldName: LogField)` |  boolean |  Returns `true` if the field exists\.  | 
|  `coalesce(fieldName: LogField, ...fieldNames: LogField[])` |  LogField |  Returns the first non\-null value from the list\.  | 

**String functions**

You can use string functions in the `filter` and `fields` commands and as arguments for other functions\. 


| Function | Result type | Description | 
| --- | --- | --- | 
|  `isempty(fieldName: string)` |  Number |  Returns `1` if the field is missing or is an empty string\.  | 
|  `isblank(fieldName: string)` |  Number |  Returns `1` if the field is missing, an empty string, or contains only white space\.  | 
|  `concat(str: string, ...strings: string[])` |  string |  Concatenates the strings\.  | 
|  `ltrim(str: string)` `ltrim(str: string, trimChars: string)` |  string |  If the function does not have a second argument, it removes white space from the left of the string\. If the function has a second string argument, it does not remove white space\. Instead, it removes the characters in `trimChars` from the left of `str`\. For example, `ltrim("xyZxyfooxyZ","xyZ")` returns `"fooxyZ"`\.  | 
|  `rtrim(str: string)` `rtrim(str: string, trimChars: string)` |  string |  If the function does not have a second argument, it removes white space from the right of the string\. If the function has a second string argument, it does not remove white space\. Instead, it removes the characters of `trimChars` from the right of `str`\. For example, `rtrim("xyZfooxyxyZ","xyZ")` returns `"xyZfoo"`\.  | 
|  `trim(str: string)` `trim(str: string, trimChars: string)` |  string |  If the function does not have a second argument, it removes white space from both ends of the string\. If the function has a second string argument, it does not remove white space\. Instead, it removes the characters of `trimChars` from both sides of `str`\. For example, `trim("xyZxyfooxyxyZ","xyZ")` returns `"foo"`\.  | 
|  `strlen(str: string)` |  number |  Returns the length of the string in Unicode code points\.  | 
|  `toupper(str: string)` |  string |  Converts the string to uppercase\.  | 
|  `tolower(str: string)` |  string |  Converts the string to lowercase\.  | 
|  `substr(str: string, startIndex: number)` `substr(str: string, startIndex: number, length: number)` |  string |  Returns a substring from the index specified by the number argument to the end of the string\. If the function has a second number argument, it contains the length of the substring to be retrieved\. For example, `substr("xyZfooxyZ",3, 3)` returns `"foo"`\.  | 
|  `replace(fieldName: string, searchValue: string, replaceValue: string)` |  string |  Replaces all instances of `searchValue` in `fieldName: string` with `replaceValue`\. For example, the function `replace(logGroup,"smoke_test","Smoke")` searches for log events where the field `logGroup` contains the string value `smoke_test` and replaces the value with the string `Smoke`\.  | 
|  `strcontains(str: string, searchValue: string)` |  number |  Returns 1 if `str` contains `searchValue` and 0 otherwise\.  | 

**Datetime functions**

You can use datetime functions in the `filter` and `fields` commands and as arguments for other functions\. You can use these functions to create time buckets for queries with aggregate functions\. You also can use time periods that consist of a number and either `m` for minutes or `h` for hours\. For example, `10m` is 10 minutes, and `1h` is 1 hour\. The following table contains a list of the different date time functions that you can use in your query commands\. The table lists each function's result type and contains a description of each function\.

**Note**  
When you create a query command, you can use the time interval selector to select a time period that you want to query\. For example, you can set a time period between 5 and 30\-minute intervals; 1, 3, and 12\-hour intervals; or a custom time frame\. You also can set time periods between specific dates\. For information about how to run a query command, see [Tutorial: Run and modify a sample query](https://docs.aws.amazon.com/en_us/AmazonCloudWatch/latest/logs/CWL_AnalyzeLogData_RunSampleQuery.html) in the Amazon CloudWatch Logs *User Guide*\.


| Function | Result type | Description | 
| --- | --- | --- | 
|  `bin(period: Period)` |  Timestamp |  Rounds the value of `@timestamp` to the given time period and then truncates\. For example, `bin(5m)` rounds the value of `@timestamp` to 5 minutes before it truncates\.  | 
|  `datefloor(timestamp: Timestamp, period: Period)` |  Timestamp |  Truncates the timestamp to the given period\. For example, `datefloor(@timestamp, 1h)` truncates all values of `@timestamp` to the bottom of the hour\.  | 
|  `dateceil(timestamp: Timestamp, period: Period)` |  Timestamp |  Rounds up the timestamp to the given period and then truncates\. For example, `dateceil(@timestamp, 1h)` truncates all values of `@timestamp` to the top of the hour\.  | 
|  `fromMillis(fieldName: number)` |  Timestamp |  Interprets the input field as the number of milliseconds since the Unix epoch and converts it to a timestamp\.  | 
|  `toMillis(fieldName: Timestamp)` |  number |  Converts the timestamp found in the named field into a number representing the milliseconds since the Unix epoch\. For example, `toMillis(@timestamp)` converts the timestamp `2022-01-14T13:18:031.000-08:00` to `1642195111000`\.  | 

**Note**  
Currently, CloudWatch Logs Insights doesn't support filtering logs with human readable timestamps\. 

**IP address functions**

You can use IP address string functions in the `filter` and `fields` commands and as arguments for other functions\. 


| Function | Result type | Description | 
| --- | --- | --- | 
|  `isValidIp(fieldName: string)` |  boolean |  Returns `true` if the field is a valid IPv4 or IPv6 address\.  | 
|  `isValidIpV4(fieldName: string)` |  boolean |  Returns `true` if the field is a valid IPv4 address\.  | 
|  `isValidIpV6(fieldName: string)` |  boolean |  Returns `true` if the field is a valid IPv6 address\.  | 
|  `isIpInSubnet(fieldName: string, subnet: string)` |  boolean |  Returns `true` if the field is a valid IPv4 or IPv6 address within the specified v4 or v6 subnet\. When you specify the subnet, use CIDR notation such as `192.0.2.0/24` or `2001:db8::/32`\.  | 
|  `isIpv4InSubnet(fieldName: string, subnet: string)` |  boolean |  Returns `true` if the field is a valid IPv4 address within the specified v4 subnet\. When you specify the subnet, use CIDR notation such as `192.0.2.0/24`\.  | 
|  `isIpv6InSubnet(fieldName: string, subnet: string)` |  boolean |  Returns `true` if the field is a valid IPv6 address within the specified v6 subnet\. When you specify the subnet, use CIDR notation such as `2001:db8::/32`\.  | 

**Stats aggregation functions**<a name="CWL_Insights_Aggregation_Functions"></a>

You can use aggregation functions in the `stats` command and as arguments for other functions\. 


| Function | Result type | Description | 
| --- | --- | --- | 
|  `avg(fieldName: NumericLogField)` |  number |  The average of the values in the specified field\.  | 
|  `count()` `count(fieldName: LogField)` |  number |  Counts the log events\. `count()` \(or `count(*)`\) counts all events returned by the query, while `count(fieldName)` counts all records that include the specified field name\.  | 
|  `count_distinct(fieldName: LogField)` |  number |  Returns the number of unique values for the field\. If the field has very high cardinality \(contains many unique values\), the value returned by `count_distinct` is just an approximation\.  | 
|  `max(fieldName: LogField)` |  LogFieldValue |  The maximum of the values for this log field in the queried logs\.  | 
|  `min(fieldName: LogField)` |  LogFieldValue |  The minimum of the values for this log field in the queried logs\.  | 
|  `pct(fieldName: LogFieldValue, percent: number)` |  LogFieldValue |  A percentile indicates the relative standing of a value in a dataset\. For example, `pct(@duration, 95)` returns the `@duration` value at which 95 percent of the values of `@duration` are lower than this value, and 5 percent are higher than this value\.  | 
|  `stddev(fieldName: NumericLogField)` |  number |  The standard deviation of the values in the specified field\.  | 
|  `sum(fieldName: NumericLogField)` |  number |  The sum of the values in the specified field\.  | 

**Stats non\-aggregation functions**<a name="CWL_Insights_Non-Aggregation_Functions"></a>

You can use non\-aggregation functions in the `stats` command and as arguments for other functions\. 


| Function | Result type | Description | 
| --- | --- | --- | 
|  `earliest(fieldName: LogField)` |  LogField |  Returns the value of `fieldName` from the log event that has the earliest timestamp in the queried logs\.  | 
|  `latest(fieldName: LogField)` |  LogField |  Returns the value of `fieldName` from the log event that has the latest timestamp in the queried logs\.  | 
|  `sortsFirst(fieldName: LogField)` |  LogField |  Returns the value of `fieldName` that sorts first in the queried logs\.  | 
|  `sortsLast(fieldName: LogField)` |  LogField |  Returns the value of `fieldName` that sorts last in the queried logs\.  | 