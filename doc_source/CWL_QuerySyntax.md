# CloudWatch Logs Insights Query Syntax<a name="CWL_QuerySyntax"></a>

CloudWatch Logs Insights supports a query language you can use to perform queries on your log groups\. Each query can include one or more query commands separated by Unix\-style pipe characters \(`|`\)\.

Six query commands are supported, along with many supporting functions and operations, including regular expressions, arithmetic operations, comparison operations, numeric functions, datetime functions, string functions, and generic functions\.

## CloudWatch Logs Insights Query Commands<a name="CWL_QuerySyntax-commnds"></a>

The following table lists the six supported query commands along with basic examples\. For more powerful sample queries, see [Sample Queries](CWL_QuerySyntax-examples.md)\.


| Command | Description | Examples | 
| --- | --- | --- | 
|  `fields` |  Retrieves the specified fields from log events\. You can use functions and operations within a fields command\.  |  `fields `foo-bar`, action, abs(f3-f4)` retrieves the fields `foo-bar`, `action`, and the absolute value of the difference between `f3` and `f4` for all log events in the log group\. The backtick symbol ``` is necessary around `foo-bar` because that field name includes a non\-alphanumeric character\. Any log field named in a query that has characters other than the @ sign, the period \(\.\), and alphanumeric characters must be surrounded by backtick characters\.  | 
|  `filter` |  Filters the results of a query based on one or more conditions\. You can use comparison operators \(=, \!=, <, <=, >, >=\), Boolean operators \(`and`, `or`, and `not`\) and regular expressions\.  |  `fields f1, f2, f3 \| filter (duration>2000)` retrieves the fields `f1`, `f2`, and `f3` for all log events with a value over 2000 in the `duration` field\. `filter (duration>2000)` is also a valid query, but the results do not show separate fields\. Instead, the results show the @timestamp and all log data in the @message field for all log events where duration is more than 2000\. `fields f1, f2 \| filter (f1=10 or f3>25)` retrieves the fields `f1` and `f2` for all log events where `f1` is 10 or `f3` is greater than 25\. `fields f1 \| filter statusCode like /2\d\d/` returns log events where the field statusCode has a value between 200 and 299\.  | 
|  `stats` |  Calculates aggregate statistics based on the values of log fields\. Several statistical operators are supported, including `sum()`, `avg()`, `count()`, `,min()`, and `max()`\. When you use `stats`, you can also use `by` to specify one or more criteria to use to group data when calculating the statistics\.  |  `stats avg (f1) by f2` calculates the average value of `f1` for each unique value of `f2`\.  | 
|  `sort` |  Sorts the retrieved log events\. Both ascending \(`asc`\) and descending \(`desc`\) order are supported\.  |  `fields f1, f2, f3 \| sort f1 desc` retrieves the fields `f1`, `f2`, and `f3` and sorts the returned events in descending order based on the value of `f1`\.  | 
|  `limit` |  Limits the number of log events returned by the query\.  |  `fields f1, f2 \| sort @timestamp desc \| limit 25` retrieves the fields `f1` and `f2`, sorts the events in descending order based on the value of `@timestamp`, and returns the first 25 events by sort order\. In this case the sort order is by timestamp starting with the most recent, so the most recent 25 events are returned\. Some sample queries provided with CloudWatch Logs Insights use `head` or `tail` commands instead of `limit`\. These commands are being deprecated and have been replaced with `limit`\. Use `limit` instead of `head` or `tail in all queries you write.`  | 
|  `parse` |  Extracts data from a log field, creating one or more ephemeral fields that you can process further in the query\. `parse` accepts both glob expressions and regular expressions\.  |  `parse @message "user=\*, method:\*, latency := *" as @user, @method, @latency \| stats avg(@latency) by @method, @user` extracts the ephemeral fields `@user`, `@method`, and `@latency` from the log field `@message` and returns the average latency for each unique combination of `@method` and `@user`\.  `parse @message /(?<time>[0-9]{10}\.[0-9]{1,3}) .* (?<code>[A-Z_]+\/[0-9]{3}) (?<url>[a-zA-Z.]+:[0-9]{1,5})/` extracts the ephemeral fields `time`, `code`, `uri` from squid access log\.| 

## Regular Expressions in the Filter Command<a name="CWL_QuerySyntax-regex"></a>

You can use `like` or `=~` \(equal sign followed by a tilde\) in the `filter` query command to filter by substrings or regular expressions\. Enclose your match string with double quotes or single quotes to perform substring matching\. To perform regular expression matching, enclose it with forward slashes\. The query returns only log events that match the criteria you set\.

**Examples**

The following three examples return all events in which `f1` contains the word `Exception`\. The first two examples use regular expressions, and the third example uses a substring match\. Each of these three examples is case\-sensitive\.

```
fields f1, f2, f3 | filter f1 like /Exception/
```

```
fields f1, f2, f3 | filter f1 =~ /Exception/
```

```
fields f1, f2, f3 | filter f1 like "Exception"
```

The following example uses a regular expression and returns all events in which `f1` contains the word `Exception`\. The query is not case\-sensitive\.

```
fields f1, f2, f3 | filter f1 like /(?i)Exception/ 
```

The following example uses a regular expression and returns all events in which `f1` is exactly the word `Exception`\. The query is not case\-sensitive\.

```
fields f1, f2, f3 | filter f1 =~ /^(?i)Exception$/
```

## Using Aliases in Queries<a name="CWL_QuerySyntax-alias"></a>

You can use `as` to create one or more aliases in a query\. Aliases are supported in the `fields`, `stats`, and `sort` commands\. 

You can create aliases for log fields and for the results of operations and functions\. 

**Examples**

The following examples show the use of aliases in query commands\.

```
fields abs(myField) as AbsoluteValuemyField, myField2
```

Return the absolute value of `myField` as `AbsoluteValuemyField` and also returns the field `myField2`\.

```
stats avg(f1) as myAvgF1 | sort myAvgF1 desc
```

Calculates the average of the values of the `f1` as `myAvgF1` and returns them in descending order by that value\.

## Supported Operations and Functions<a name="CWL_QuerySyntax-operations-functions"></a>

The query language supports many types of operations and functions, as shown in the following tables\.

**Comparison Operations**

You can use comparison operations in the `filter` command and as arguments for other functions\. Comparison operations accept all data types as arguments and return a Boolean result\.

```
= != < <= > >=
```

**Arithmetic Operations**

You can use arithmetic operations in the `filter` and `fields` commands and as arguments for other functions\. Arithmetic operations accept numeric data types as arguments and return numeric results\.


| Operation | Description | 
| --- | --- | 
|  `a + b` |  Addition  | 
|  `a - b` |  Subtraction  | 
|  `a * b` |  Multiplication  | 
|  `a / b` |  Division  | 
|  `a ^ b` |  Exponentiation\. `2 ^ 3` returns `8`  | 
|  `a % b` |  Remainder or modulus\. `10 % 3` returns `1`  | 

**Numeric Operations**

You can use numeric operations in the `filter` and `fields` commands and as arguments for other functions\. Numeric operations accept numeric data types as arguments and return numeric results\.


| Operation | Description | 
| --- | --- | 
|  `abs(a)` |  Absolute value  | 
|  `ceil(a)` |  Round to ceiling \(the smallest integer that is greater than the value of `a`\)\.  | 
|  `floor(a)` |  Round to floor \(the largest integer that is smaller than the value of `a`\)\.  | 
|  `greatest(a,b,... z)` |  Returns the largest value\.  | 
|  `least(a, b, ... z)` |  Returns the smallest value\.  | 
|  `log(a)` |  Natural log  | 
|  `sqrt(a)` |  Square root  | 

**General Functions**

You can use general functions in the `filter` and `fields` commands and as arguments for other functions\. 


| Function | Arguments | Result type | Description | 
| --- | --- | --- | --- | 
|  `ispresent(fieldname)` |  Log field |  Boolean |  Returns `true` if the field exists\.  | 
|  `coalesce(fieldname1, fieldname2, ... fieldnamex)` |  Log fields |  Log field |  Returns the first non\-null value from the list\.  | 

**String Functions**

You can use string functions in the `filter` and `fields` commands and as arguments for other functions\. 


| Function | Arguments | Result type | Description | 
| --- | --- | --- | --- | 
|  `isempty(fieldname)` |  String |  Boolean |  Returns `true` if the field is missing or is an empty string\.  | 
|  `isblank(fieldname)` |  String |  Boolean |  Returns `true` if the field is missing, an empty string, or contains only white space\.  | 
|  `concat(string1, string2, ... stringz)` |  Strings |  String |  Concatenates the strings\.  | 
|  `ltrim(string) or ltrim(string1, string2)` |  String |  String |  Remove white space from the left of the string\. If the function has a second string argument, it removes the characters of `string2` from the left of `string1`\. For example, `ltrim("xyZfooxyZ","xyZ")` returns `"fooxyZ"`\.  | 
|  `rtrim(string) or rtrim(string1, string2)` |  String |  String |  Remove white space from the right of the string\. If the function has a second string argument, it removes the characters of `string2` from the right of `string1`\. For example, `rtrim("xyZfooxyZ","xyZ")` returns `"xyZfoo"`\.  | 
|  `trim(string) or trim(string1, string2)` |  String |  String |  Remove white space from both ends of the string\. If the function has a second string argument, it removes the characters of `string2` from both sides of `string1`\. For example, `trim("xyZfooxyZ","xyZ")` returns `"foo"`\.  | 
|  `strlen(string)` |  String |  Number |  Returns the length of the string in Unicode code points\.  | 
|  `toupper(string)` |  String |  String |  Converts the string to uppercase\.  | 
|  `tolower(string)` |  String |  String |  Converts the string to lowercase\.  | 
|  `substr(string1, x), or substr(string1, x, y)` |  String, Number or String, Number, Number |  String |  Returns a substring from the index specified by the number argument to the end of the string\. If the function has a second number argument, it contains the length of the substring to be retrieved\. For example, `substr("xyZfooxyZ",3, 3)` returns `"foo"`\.  | 
|  `replace(string1, string2, string3)` |  String, String, String |  String |  Replaces all instances of `string2` in `string1` with `string3`\. For example: `replace("foo","o","0")` returns `"f00"`\.  | 
|  `strcontains(string1, string2)` |  String |  Number |  Returns 1 if `string1` contains `string2` and 0 otherwise\.  | 

**Datetime Functions**

You can use datetime functions in the `filter` and `fields` commands and as arguments for other functions\. You can use these functions to create time buckets for queries with aggregate functions\.

As part of datetime functions, you can use time periods that consist of a number and then either `m` for minutes or `h` for hours\. For example, `10m` is 10 minutes and `1h` is one hour\.


| Function | Arguments | Result type | Description | 
| --- | --- | --- | --- | 
|  `datefloor(a, period)` |  Timestamp, period |  Timestamp |  Truncates the timestamp to the given period\. For example, `datefloor(@timestamp, 1h)` truncates all values of `@timestamp` to the bottom of the hour\.  | 
|  `dateceil(a, period)` |  Timestamp, period |  Timestamp |  Rounds up the timestamp to the given period and then truncates\. For example, `dateceil(@timestamp, 1h)` truncates all values of `@timestamp` to the top of the hour\.  | 
|  `bin(period)` |  period |  Timestamp |  Rounds the value of `@timestamp` to the given period and then truncates\.  | 

**Aggregation Functions in the Stats Command**<a name="CWL_Insights_Aggregation_Functions"></a>

You can use aggregation functions in the `stats` command and as arguments for other functions\. 


| Function | Arguments | Result type | Description | 
| --- | --- | --- | --- | 
|  `avg(NumericFieldname)` |  Numeric log field |  Number |  The average of the values in the specified field\.  | 
|  `count(fieldname) or count(*)` |  Log field |  Number |  Counts the log records\. count\(\*\) counts all records in the log group, while count \(fieldname\) counts all records that include the specified field name\.  | 
|  `count_distinct(fieldname)` |  Log field |  Number |  Returns the number of unique values for the field\. If the field has very high cardinality \(contains many unique values\), the value returned by count\_distinct is just an approximation\.  | 
|  `max(fieldname)` |  Log field |  Log field value |  The maximum of the values for this log field in the queried logs\.  | 
|  `min(fieldname)` |  Log field |  Log field value |  The minimum of the values for this log field in the queried logs\.  | 
|  `pct(fieldname, value)` |  Log field value, value |  Log field value |  A percentile indicates the relative standing of a value in a dataset\. For example, `pct(@duration, 95)` returns the `@duration` value at which 95 percent of the values of `@duration` are lower than this value, and 5 percent are higher than this value\.  | 
|  `stddev(NumericFieldname)` |  Numeric log field |  Number |  The standard deviation of the values in the specified field\.  | 
|  `sum(NumericFieldname)` |  Numeric log field |  Number |  The sum of the values in the specified field\.  | 
