# Oracle to MySQL conversion settings<a name="schema-conversion-oracle-mysql"></a>

Oracle to MySQL conversion settings in DMS Schema Conversion include the following:
+ Your source Oracle database can use the `ROWID` pseudocolumn\. MySQL doesn't support similar functionality\. DMS Schema Conversion can emulate the `ROWID` pseudocolumn in the converted code\. To do so, turn on the **Generate row ID** option\.

  If your source Oracle code doesn't use the `ROWID` pseudocolumn, turn off the **Generate row ID** option\. In this case, the converted code works faster\.
+ Your source Oracle code can include the `TO_CHAR`, `TO_DATE`, and `TO_NUMBER` functions with parameters that MySQL doesn't support\. By default, DMS Schema Conversion emulates the usage of these parameters in the converted code\.

  You can use native MySQL `TO_CHAR`, `TO_DATE`, and `TO_NUMBER` functions when your source Oracle code lacks parameters that are unsupported by MySQL\. In this case, the converted code works faster\. To do so, select the following values:
  + **Use a native MySQL TO\_CHAR function**
  + **Use a native MySQL TO\_DATE function**
  + **Use a native MySQL TO\_NUMBER function**
+ Your database and applications can run in different time zones\. By default, DMS Schema Conversion emulates time zones in the converted code\. However, you don't need this emulation when your database and applications use the same time zone\. In this case, select **Improve the performance of the converted code where the database and applications use the same time zone**\.