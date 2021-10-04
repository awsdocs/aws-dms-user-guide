# Using transformation rule expressions to define column content<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions"></a>

To define content for new and existing columns, you can use an expression within a transformation rule\. For example, using expressions you can add a column or replicate source table headers to a target\. You can also use expressions to flag records on target tables as inserted, updated, or deleted at the source\. 

**Topics**
+ [Adding a column using an expression](#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions-adding)
+ [Flagging target records using an expression](#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions-Flagging)
+ [Replicating source table headers using expressions](#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions-Headers)
+ [Using SQLite functions to build expressions](#CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions-SQLite)

## Adding a column using an expression<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions-adding"></a>

To add columns to tables using an expression in a transformation rule, use an `add-column` rule action and a `column` rule target\.

The following example adds a new column to the `ITEM` table\. It sets the new column name to `FULL_NAME`, with a data type of `string`, 50 characters long\. The expression concatenates the values of two existing columns, `FIRST_NAME` and `LAST_NAME`, to evaluate to `FULL_NAME`\. The `schema-name`, `table-name`, and expression parameters refer to objects in the source database table\. `Value` and the `data-type` block refer to objects in the target database table\.

```
{
    "rules": [
        {
            "rule-type": "selection", 
            "rule-id": "1",
            "rule-name": "1",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "%"
            },
            "rule-action": "include"
        },
        {
            "rule-type": "transformation",
            "rule-id": "2",
            "rule-name": "2",
            "rule-action": "add-column",
            "rule-target": "column",
            "object-locator": {
                "schema-name": "Test",
                "table-name": "ITEM"
            },
            "value": "FULL_NAME",
            "expression": "$FIRST_NAME||'_'||$LAST_NAME",
            "data-type": {
                 "type": "string",
                 "length": 50
            }
        }
    ]
}
```

## Flagging target records using an expression<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions-Flagging"></a>

To flag records in target tables as inserted, updated, or deleted in the source table, use an expression in a transformation rule\. The expression uses an `operation_indicator` function to flag records\. Records deleted from the source aren't deleted from the target\. Instead, the target record is flagged with a user\-provided value to indicate that it was deleted from the source\.

**Note**  
The `operation_indicator` function works only on tables that have a primary key\.

For example, the following transformation rule first adds a new `Operation` column to a target table\. It then updates the column with the value `D` whenever a record is deleted from a source table\.

```
{
      "rule-type": "transformation",
      "rule-id": "2",
      "rule-name": "2",
      "rule-target": "column",
      "object-locator": {
        "schema-name": "%",
        "table-name": "%"
      },
      "rule-action": "add-column",
      "value": "Operation",
      "expression": "operation_indicator('D', 'U', 'I')",
      "data-type": {
        "type": "string",
        "length": 50
      }
}
```

## Replicating source table headers using expressions<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions-Headers"></a>

By default, headers for source tables aren't replicated to the target\. To indicate which headers to replicate, use a transformation rule with an expression that includes the table column header\. 

You can use the following column headers in expressions\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions.html)

The following example adds a new column to the target by using the stream position value from the source\. For SQL Server, the stream position value is the LSN for the source endpoint\. For Oracle, the stream position value is the SCN for the source endpoint\.

```
{
      "rule-type": "transformation",
     "rule-id": "2",
      "rule-name": "2",
      "rule-target": "column",
      "object-locator": {
        "schema-name": "%",
        "table-name": "%"
      },
      "rule-action": "add-column",
      "value": "transact_id",
      "expression": "$AR_H_STREAM_POSITION",
      "data-type": {
        "type": "string",
        "length": 50
      }
    }
```

The following example adds a new column to the target that has a unique incrementing number from the source\. This value represents a 35 digit unique number at task level\. The first 16 digits are part of a timestamp, and the last 19 digits are the record\_id number incremented by the DBMS\.

```
{
"rule-type": "transformation",
"rule-id": "2",
"rule-name": "2",
"rule-target": "column",
"object-locator": {
"schema-name": "%",
"table-name": "%"
},
"rule-action": "add-column",
"value": "transact_id",
"expression": "$AR_H_CHANGE_SEQ",
"data-type": {
"type": "string",
"length": 50
}
}
```

## Using SQLite functions to build expressions<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions-SQLite"></a>

To build transformation rule expressions that manipulate source column content, you can use the following types of SQLite functions:
+ String functions
+ LOB functions
+ Numeric functions
+ NULL check functions
+ Date and time functions
+ Hash function
+ CASE expression

Following, you can find string functions that you can use to build transformation rule expressions\.


| String functions | Description | 
| --- | --- | 
|  `lower(x)`  |  The `lower(x)` function returns a copy of string *`x`* with all characters converted to lowercase\. The default, built\-in `lower` function works for ASCII characters only\.  | 
|  `ltrim(x,y)`  |  The `ltrim(x,y)` function returns a string formed by removing all characters that appear in y from the left side of x\. If there is no value for y, `ltrim(x)` removes spaces from the left side of x\.  | 
|  `replace(x,y,z)`  |  The `replace(x,y,z)` function returns a string formed by substituting string z for every occurrence of string y in string x\.  | 
| rtrim\(x,y\) |  The `rtrim(x,y)` function returns a string formed by removing all characters that appear in y from the right side of x\. If there is no value for y, `rtrim(x)` removes spaces from the right side of x\.  | 
| substr\(x,y,z\) |  The `substr(x,y,z)` function returns a substring of the input string `x` that begins with the `y`th character, and which is *`z`* characters long\.  If *`z`* is omitted, `substr(x,y)` returns all characters through the end of string `x` beginning with the `y`th character\. The leftmost character of `x` is number 1\. If *`y`* is negative, the first character of the substring is found by counting from the right rather than the left\. If *`z`* is negative, then the `abs(z)` characters preceding the `y`th character are returned\. If `x` is a string, then the characters' indices refer to actual UTF\-8 characters\. If `x` is a BLOB, then the indices refer to bytes\.  | 
| trim\(x,y\) |  The `trim(x,y)` function returns a string formed by removing all characters that appear in `y` from both sides of `x`\. If there is no value for `y`, `trim(x)` removes spaces from both sides of `x`\.  | 
|  `replaceChars(X,Y,Z)`  |  The `replaceChars(X,Y,Z)` function replaces any character in string `X` that also exists in string `Y` \(characters to be replaced\) with `Z` \(replacement characters\) in the same position\. This function is especially useful for removing characters that aren't valid from paths and file names\. If string `Z` doesn't include a character that has a corresponding position in string `X`, that string `X`character is replaced with the first character in string `Z`\. If string `X` includes a character that doesn't exist in string `Z`, the original character is kept unchanged\. For example, specifying `replaceChars("abcde","abcd","123")` returns **1231e**\.   | 

Following, you can find LOB functions that you can use to build transformation rule expressions\.


| LOB functions | Description | 
| --- | --- | 
|  `hex(x)`  |  The `hex` function receives a BLOB as an argument and returns an uppercase hexadecimal string version of the BLOB content\.  | 
|  `randomblob (N)`  |  The `randomblob(N)` function returns an `N`\-byte BLOB that contains pseudorandom bytes\. If *N* is less than 1, a 1\-byte random BLOB is returned\.   | 
|  `zeroblob(N)`  |  The `zeroblob(N)` function returns a BLOB that consists of `N` bytes of 0x00\.  | 

Following, you can find numeric functions that you can use to build transformation rule expressions\.


| Numeric functions | Description | 
| --- | --- | 
|  `abs(x)`  |  The `abs(x)` function returns the absolute value of the numeric argument `x`\. The `abs(x)` function returns NULL if *x* is NULL\. The `abs(x)` function returns 0\.0 if **x** is a string or BLOB that can't be converted to a numeric value\.  | 
|  `random()`  |  The `random` function returns a pseudorandom integer between \-9,223,372,036,854,775,808 and \+9,223,372,036,854,775,807\.  | 
|  `round (x,y)`  |  The `round (x,y)` function returns a floating\-point value *x* rounded to *y* digits to the right of the decimal point\. If there is no value for *y*, it's assumed to be 0\.  | 
|  `max (x,y...)`  |  The multiargument `max` function returns the argument with the maximum value, or returns NULL if any argument is NULL\.  The `max` function searches its arguments from left to right for an argument that defines a collating function\. If one is found, it uses that collating function for all string comparisons\. If none of the arguments to `max` define a collating function, the `BINARY` collating function is used\. The `max` function is a simple function when it has two or more arguments, but it operates as an aggregate function if it has a single argument\.  | 
|  `min (x,y...)`  |  The multiargument `min` function returns the argument with the minimum value\.  The `min` function searches its arguments from left to right for an argument that defines a collating function\. If one is found, it uses that collating function for all string comparisons\. If none of the arguments to `min` define a collating function, the `BINARY` collating function is used\. The `min` function is a simple function when it has two or more arguments, but it operates as an aggregate function if it has a single argument\.   | 

Following, you can find NULL check functions that you can use to build transformation rule expressions\.


| NULL check functions | Description | 
| --- | --- | 
|  `coalesce (x,y...)`  |  The `coalesce` function returns a copy of its first non\-NULL argument, but it returns NULL if all arguments are NULL\. The coalesce function has at least two arguments\.  | 
|  `ifnull(x,y)`  |  The `ifnull` function returns a copy of its first non\-NULL argument, but it returns NULL if both arguments are NULL\. The `ifnull` function has exactly two arguments\. The `ifnull` function is the same as `coalesce` with two arguments\.  | 
|  `nullif(x,y)`  |  The `nullif(x,y)` function returns a copy of its first argument if the arguments are different, but it returns NULL if the arguments are the same\.  The `nullif(x,y)` function searches its arguments from left to right for an argument that defines a collating function\. If one is found, it uses that collating function for all string comparisons\. If neither argument to nullif defines a collating function, then the `BINARY` collating function is used\.  | 

Following, you can find date and time functions that you can use to build transformation rule expressions\.


| Date and time functions | Description | 
| --- | --- | 
|  `date(timestring, modifier, modifier...)`  |  The `date` function returns the date in the format YYYY\-MM\-DD\.  | 
|  `time(timestring, modifier, modifier...)`  |  The `time` function returns the time in the format HH:MM:SS\.  | 
|  `datetime(timestring, modifier, modifier...)`  |  The `datetime` function returns the date and time in the format YYYY\-MM\-DD HH:MM:SS\.  | 
|  `julianday(timestring, modifier, modifier...)`  |  The `julianday` function returns the number of days since noon in Greenwich on November 24, 4714 B\.C\.  | 
|  `strftime(format, timestring, modifier, modifier...)`  |  The `strftime` function returns the date according to the format string specified as the first argument, using one of the following variables: `%d`: day of month `%H`: hour 00–24 `%f`: \*\* fractional seconds SS\.SSS `%j`: day of year 001–366 `%J`: \*\* Julian day number `%m`: month 01–12 `%M`: minute 00–59 `%s`: seconds since 1970\-01\-01 `%S`: seconds 00–59 `%w`: day of week 0–6 sunday==0 `%W`: week of year 00–53 `%Y`: year 0000–9999 `%%`: %  | 

Following, you can find a hash function that you can use to build transformation rule expressions\.


| Hash function | Description | 
| --- | --- | 
|  `hash_sha256(x)`  |  The `hash` function generates a hash value for an input column \(using the SHA\-256 algorithm\) and returns the hexadecimal value of the generated hash value\.  To use the `hash` function in an expression, add `hash_sha256(x)` to the expression and replace *`x`* with the source column name\.  | 

### Using a CASE expression<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions-SQLite.CASE"></a>

The SQLite `CASE` expression evaluates a list of conditions and returns an expression based on the result\. Syntax is shown following\.

```
    CASE case_expression
     WHEN when_expression_1 THEN result_1
     WHEN when_expression_2 THEN result_2
     ...
     [ ELSE result_else ] 
    END

# Or 

     CASE
     WHEN case_expression THEN result_1
     WHEN case_expression THEN result_2
     ...
     [ ELSE result_else ] 
    END
```

### Examples<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Expressions-SQLite.Ex"></a>

**Example of adding a new string column to the target table using a case condition**  
The following example transformation rule adds a new string column, `emp_seniority`, to the target table, `employee`\. It uses the SQLite `round` function on the salary column, with a case condition to check if the salary equals or exceeds 20,000\. If it does, the column gets the value `SENIOR`, and anything else has the value `JUNIOR`\.  

```
{
      "rule-type": "transformation",
      "rule-id": "2",
      "rule-name": "2",
      "rule-action": "add-column",
      "rule-target": "column",
      "object-locator": {
        "schema-name": "public",
        "table-name": "employee"
      },
      "value": "emp_seniority",
      "expression": " CASE WHEN round($emp_salary)>=20000 THEN ‘SENIOR’ ELSE ‘JUNIOR’ END",
      "data-type": {
        "type": "string",
        "lenght": 50
      }

}
```

**Example of adding a new date column to the target table**  
The following example adds a new date column, `createdate`, to the target table, `employee`\. When you use the SQLite date function `datetime`, the date is added to the newly created table for each row inserted\.  

```
{
      "rule-type": "transformation",
      "rule-id": "2",
      "rule-name": "2",
      "rule-action": "add-column",
      "rule-target": "column",
      "object-locator": {
        "schema-name": "public",
        "table-name": "employee"
      },
      "value": "createdate",
      "expression": "datetime ()",
      "data-type": {
        "type": "datetime",
        "precision": 6
      }
   		 }
```

**Example of adding a new numeric column to the target table**  
The following example adds a new numeric column, `rounded_emp_salary`, to the target table, `employee`\. It uses the SQLite `round` function to add the rounded salary\.   

```
{
      "rule-type": "transformation",
      "rule-id": "2",
      "rule-name": "2",
      "rule-action": "add-column",
      "rule-target": "column",
      "object-locator": {
        "schema-name": "public",
        "table-name": "employee"
      },
      "value": "rounded_emp_salary",
      "expression": "round($emp_salary)",
      "data-type": {
        "type": "int8"
      }
}
```

**Example of adding a new string column to the target table using the hash function**  
The following example adds a new string column, `hashed_emp_number`, to the target table, `employee`\. The SQLite `hash_sha256(x)` function creates hashed values on the target for the source column, `emp_number`\.  

```
  {
      "rule-type": "transformation",
      "rule-id": "2",
      "rule-name": "2",
      "rule-action": "add-column",
      "rule-target": "column",
      "object-locator": {
        "schema-name": "public",
        "table-name": "employee"
      },
      "value": "hashed_emp_number",
      "expression": "hash_sha256($emp_number)",
      "data-type": {
        "type": "string",
        "lenght": 50
      }
   		 }
```