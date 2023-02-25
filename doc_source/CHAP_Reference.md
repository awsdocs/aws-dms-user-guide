# AWS DMS reference<a name="CHAP_Reference"></a>

In this reference section, you can find additional information you might need when using AWS Database Migration Service \(AWS DMS\), including data type conversion information\. 

AWS DMS maintains data types when you do a homogeneous database migration where both source and target use the same engine type\. When you do a heterogeneous migration, where you migrate from one database engine type to a different database engine, data types are converted to an intermediate data type\. To see how the data types appear on the target database, consult the data type tables for the source and target database engines\.

Be aware of a few important things about data types when migrating a database: 
+ The FLOAT data type is inherently an approximation\. When you insert a specific value in FLOAT, it might be represented differently in the database\. This difference is because FLOAT isn't an exact data type, such as a decimal data type like NUMBER or NUMBER\(p,s\)\. As a result, the internal value of FLOAT stored in the database might be different than the value that you insert\. Thus, the migrated value of a FLOAT might not match exactly the value in the source database\. 

  For more information on this issue, see the following articles: 
  + [ IEEE floating point](https://en.wikipedia.org/wiki/IEEE_floating_point) in Wikipedia
  + [ IEEE floating\-point representation](https://learn.microsoft.com/en-us/cpp/build/ieee-floating-point-representation?view=msvc-170) on Microsoft Learn
  + [ Why floating\-point numbers may lose precision](https://learn.microsoft.com/en-us/cpp/build/why-floating-point-numbers-may-lose-precision?view=msvc-170) on Microsoft Learn

**Topics**
+ [Data types for AWS Database Migration Service](CHAP_Reference.DataTypes.md)