# Character substitution task settings<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.CharacterSubstitution"></a>

You can specify that your replication task perform character substitutions on the target database for all source database columns with the AWS DMS `STRING` or `WSTRING` data type\. You can configure character substitution for any task with endpoints from the following source and target databases:
+ Source databases:
  + Oracle
  + Microsoft SQL Server
  + MySQL
  + PostgreSQL
  + SAP Adaptive Server Enterprise \(ASE\)
  + IBM Db2 LUW
+ Target databases:
  + Oracle
  + Microsoft SQL Server
  + MySQL
  + PostgreSQL
  + SAP Adaptive Server Enterprise \(ASE\)
  + Amazon Redshift

You can specify character substitutions using the `CharacterSetSettings` parameter in your task settings\. These character substitutions occur for characters specified using the Unicode code point value in hexadecimal notation\. You can implement the substitutions in two phases, in the following order if both are specified:

1. **Individual character replacement** – AWS DMS can replace the values of selected characters on the source with specified replacement values of corresponding characters on the target\. Use the `CharacterReplacements` array in `CharacterSetSettings` to select all source characters having the Unicode code points you specify\. Use this array also to specify the replacement code points for the corresponding characters on the target\. 

   To select all characters on the source that have a given code point, set an instance of `SourceCharacterCodePoint` in the `CharacterReplacements` array to that code point\. Then specify the replacement code point for all equivalent target characters by setting the corresponding instance of `TargetCharacterCodePoint` in this array\. To delete target characters instead of replacing them, set the appropriate instances of `TargetCharacterCodePoint` to zero \(0\)\. You can replace or delete as many different values of target characters as you want by specifying additional pairs of `SourceCharacterCodePoint` and `TargetCharacterCodePoint` settings in the `CharacterReplacements` array\. If you specify the same value for multiple instances of `SourceCharacterCodePoint`, the value of the last corresponding setting of `TargetCharacterCodePoint` applies on the target\.

   For example, suppose that you specify the following values for `CharacterReplacements`\.

   ```
   "CharacterSetSettings": {
       "CharacterReplacements": [ {
           "SourceCharacterCodePoint": 62,
           "TargetCharacterCodePoint": 61
           }, {
           "SourceCharacterCodePoint": 42,
           "TargetCharacterCodePoint": 41
           }
       ]
   }
   ```

   In this example, AWS DMS replaces all characters with the source code point hex value 62 on the target by characters with the code point value 61\. Also, AWS DMS replaces all characters with the source code point 42 on the target by characters with the code point value 41\. In other words, AWS DMS replaces all instances of the letter `'b'`on the target by the letter `'a'`\. Similarly, AWS DMS replaces all instances of the letter `'B'` on the target by the letter `'A'`\.

1. **Character set validation and replacement** – After any individual character replacements complete, AWS DMS can make sure that all target characters have valid Unicode code points in the single character set that you specify\. You use `CharacterSetSupport` in `CharacterSetSettings` to configure this target character verification and modification\. To specify the verification character set, set `CharacterSet` in `CharacterSetSupport` to the character set's string value\. \(The possible values for `CharacterSet` follow\.\) You can have AWS DMS modify the invalid target characters in one of the following ways:
   + Specify a single replacement Unicode code point for all invalid target characters, regardless of their current code point\. To configure this replacement code point, set `ReplaceWithCharacterCodePoint` in `CharacterSetSupport` to the specified value\.
   + Configure the deletion of all invalid target characters by setting `ReplaceWithCharacterCodePoint` to zero \(0\)\.

   For example, suppose that you specify the following values for `CharacterSetSupport`\.

   ```
   "CharacterSetSettings": {
       "CharacterSetSupport": {
           "CharacterSet": "UTF16_PlatformEndian",
           "ReplaceWithCharacterCodePoint": 0
       }
   }
   ```

   In this example, AWS DMS deletes any characters found on the target that are invalid in the `"UTF16_PlatformEndian"` character set\. So, any characters specified with the hex value `2FB6` are deleted\. This value is invalid because this is a 4\-byte Unicode code point and UTF16 character sets accept only characters with 2\-byte code points\.

**Note**  
The replication task completes all of the specified character substitutions before starting any global or table\-level transformations that you specify through table mapping\. For more information about table mapping, see [Using table mapping to specify task settings](CHAP_Tasks.CustomizingTasks.TableMapping.md)\.

The values that AWS DMS supports for `CharacterSet` appear in the table following\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.CustomizingTasks.TaskSettings.CharacterSubstitution.html)