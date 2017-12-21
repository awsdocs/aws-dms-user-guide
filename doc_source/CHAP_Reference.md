# Reference for AWS Database Migration Service Including Data Conversion Reference and Additional Topics<a name="CHAP_Reference"></a>

This reference section includes additional information you may need when using AWS Database Migration Service \(AWS DMS\), including data type conversion information and additional procedures\. 

There are several ways to begin a database migration\. You can select the AWS DMS console wizard that walks you through each step of the process, or you can do each step by selecting the appropriate task from the navigation pane\. You can also use the AWS CLI; for information on using the AWS CLI with AWS DMS, see [ DMS](http://docs.aws.amazon.com/cli/latest/reference/dms/index.html)\.

There are a few important things to remember about data types when migrating a database: 

+ The FLOAT data type is inherently an approximation\. The FLOAT data type is special in the sense that when you insert a specific value, it may be represented differently in the database, as it is not an accurate data type, such as a decimal data type like NUMBER or NUMBER\(p,s\)\. As a result, the internal value of FLOAT stored in the database might be different than the value that you insert, so the migrated value of a FLOAT might not match exactly the value on the source database\. 

  Here are some articles on the topic: 

  IEEE floating point [https://en\.wikipedia\.org/wiki/IEEE\_floating\_point](https://en.wikipedia.org/wiki/IEEE_floating_point)

   IEEE Floating\-Point Representation [https://msdn\.microsoft\.com/en\-us/library/0b34tf65\.aspx](https://msdn.microsoft.com/en-us/library/0b34tf65.aspx)

  Why Floating\-Point Numbers May Lose Precision [https://msdn\.microsoft\.com/en\-us/library/c151dt3s\.aspx](https://msdn.microsoft.com/en-us/library/c151dt3s.aspx)

+ The UTF\-8 4\-byte character set \(utf8mb4\) is not supported and could cause unexpected behavior in a source database\. Plan to convert any data using the UTF\-8 4\-byte character set before migrating\.


+ [Source Data Types](CHAP_Reference.Source.md)
+ [Target Data Types](CHAP_Reference.Target.md)
+ [Data Types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)
+ [Using Extra Connection Attributes with AWS Database Migration Service](CHAP_Introduction.ConnectionAttributes.md)
+ [Using ClassicLink with AWS Database Migration Service](CHAP_Reference.ClassicLink.md)