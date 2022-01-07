# Creating mapping rules in AWS SCT<a name="CHAP_Mapping"></a>

 You can add multiple source and target databases in a single AWS SCT project\. Doing this simplifies the management of projects, when you migrate multiple databases to different target platforms\. 

 After you create a new project and add source and target databases, create mapping rules\. AWS SCT requires at least one mapping rule to create a migration assessment report and convert database schemas\.

 A *mapping rule* describes a source\-target pair that includes a source database schema or source database and a target database platform\. You can create multiple mapping rules in a single AWS SCT project\. Use mapping rules to convert every source database schema to the right target database platform\. 

 You can create mapping rules only for supported database conversion pairs\. For the list of supported conversion pairs, see [Sources for AWS SCT](CHAP_Source.md)\. 

 If you open a project saved in AWS SCT version 1\.0\.655 or before, AWS SCT automatically creates mapping rules for all source database schemas to the target database platform\. To add other target database platforms, delete existing mapping rules and then create new mapping rules\. 

**Topics**
+ [Adding a new mapping rule](CHAP_Mapping.New.md)
+ [Editing mapping rules](CHAP_Mapping.Edit.md)
+ [Using virtual targets](CHAP_Mapping.VirtualTargets.md)
+ [Limitations to using multiple servers in a single AWS SCT project](CHAP_Mapping.Limitations.md)