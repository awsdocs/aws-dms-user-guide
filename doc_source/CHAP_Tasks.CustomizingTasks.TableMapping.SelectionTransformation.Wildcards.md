# Wildcards in table mapping<a name="CHAP_Tasks.CustomizingTasks.TableMapping.SelectionTransformation.Wildcards"></a>

This section describes wildcards you can use when specifying the schema and table names for table mapping\.


| Wildcard | Matches | 
| --- |--- |
| % | Zero or more characters | 
| \_ | A single character | 
| \[\_\] | A literal underscore character | 
| \[ab\] | A set of characters\. For example, \[ab\] matches either 'a' or 'b'\. | 
| \[a\-d\] | A range of characters\. For example,\[a\-d\] matches either 'a', 'b', 'c', or 'd'\. | 

For Oracle source and target endpoints, you can use the `escapeCharacter` extra connection attribute to specify an escape character\. An escape character allows you to use a specified wildcard character in expressions as if it was not wild\. For example, `escapeCharacter=#` allows you to use '\#' to make a wildcard character act as an ordinary character in an expression as in the this sample code\.

```
{
    "rules": [
        {
            "rule-type": "selection",
            "rule-id": "542485267",
            "rule-name": "542485267",
            "object-locator": { "schema-name": "ROOT", "table-name": "TEST#_T%" },
            "rule-action": "include",
            "filters": []
        }
    ]
}
```

Here, the '\#' escape character makes the '\_' wildcard character act as a normal character\. AWS DMS selects tables in the schema named `ROOT`, where each table has a name with `TEST_T` as its prefix\.