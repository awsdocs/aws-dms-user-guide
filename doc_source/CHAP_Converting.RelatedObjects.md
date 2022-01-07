# Finding related transformed objects<a name="CHAP_Converting.RelatedObjects"></a>

After a schema conversion, in some cases AWS SCT might have created several objects for one schema object on the source database\. For example, when performing an Oracle to PostgreSQL conversion, AWS SCT takes each Oracle trigger and transforms it into a trigger and trigger function on PostgreSQL target\. Also, when AWS SCT converts an Oracle package function or procedure to PostgreSQL, it creates an equivalent function and an INIT function that should be run as init block before the procedure or function can be run\.

The following procedure lets you see all related objects that were created after a schema conversion\.

**To view related objects that were created during a schema conversion**

1. After the schema conversion, choose the converted object in the target tree view\.

1. Choose the **Related Converted Objects** tab\.

1. View the list of related target objects\.