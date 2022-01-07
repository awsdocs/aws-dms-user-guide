# Converting SQL Server to MySQL<a name="CHAP_Source.SQLServer.ToMySQL"></a>

Some things to consider when migrating a SQL Server schema to MySQL:
+ MySQL doesn’t support the MERGE statement\. However, AWS SCT can emulate the MERGE statement during conversion by using the INSERT ON DUPLICATE KEY clause and the UPDATE FROM and DELETE FROM statements\. 

  For correct emulation using INSERT ON DUPLICATE KEY, make sure that a unique constraint or primary key exists on the target MySQL database\.
+ A GOTO statement and a label can be used to change the order that statements are run in\. Any Transact\-SQL statements that follow a GOTO statement are skipped and processing continues at the label\. GOTO statements and labels can be used anywhere within a procedure, batch, or statement block\. GOTO statements can also be nested\.

  MySQL doesn’t use GOTO statements\. When AWS SCT converts code that contains a GOTO statement, it converts the statement to use a BEGIN…END or LOOP…END LOOP statement\. You can find examples of how AWS SCT converts GOTO statements in the table following\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Source.SQLServer.ToMySQL.html)
+ MySQL doesn't support multi\-statement table\-valued functions\. AWS SCT simulates table\-valued functions during a conversion by creating temporary tables and rewriting statements to use these temporary tables\.