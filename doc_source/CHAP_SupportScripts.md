# Working with diagnostic support scripts in AWS DMS<a name="CHAP_SupportScripts"></a>

If you encounter an issue when working with AWS DMS, your support engineer might need more information about either your source or target database\. We want to make sure that AWS Support gets as much of the required information as possible in the shortest possible time\. Therefore, we developed scripts to query this information for several of the major relational database engines\.

If a support script is available for your database, you can download it using the link in the corresponding script topic described following\. After verifying and reviewing the script \(described following\), you can run it according to the procedure described in the script topic\. When the script run is complete, you can upload its output to your AWS Support case \(again, described following\)\.

Before running the script, you can detect any errors that might have been introduced when downloading or storing the support script\. To do this, compare the checksum for the script file with a value provided by AWS\. AWS uses the SHA256 algorithm for the checksum\.

**To verify the support script file using a checksum**

1. Open the latest checksum file provided to verify these support scripts at [https://d2pwp9zz55emqw.cloudfront.net/sha256Check.txt](https://d2pwp9zz55emqw.cloudfront.net/sha256Check.txt)\. For example, the file might have content like the following\.

   ```
   MYSQL  dfafd0d511477c699f96c64693ad0b1547d47e74d5c5f2f2025b790b1422e3c8
   ORACLE  6c41ebcfc99518cfa8a10cb2ce8943b153b2cc7049117183d0b5de3d551bc312
   POSTGRES  6ccd274863d14f6f3146fbdbbba43f2d8d4c6a4c25380d7b41c71883aa4f9790
   SQL_SERVER  971a6f2c46aec8d083d2b3b6549b1e9990af3a15fe4b922e319f4fdd358debe7
   ```

1. Run the SHA256 validation command for your operating system in the directory that contains the support file\. For example, on the macOS operating system you can run the following command on an Oracle support script described later in this topic\.

   ```
   shasum -a 256 awsdms_support_collector_oracle.sql
   ```

1. Compare the results of the command with the value shown in the latest `sha256Check.txt` file that you opened\. The two values should match\. If they don't, contact your support engineer about the mismatch and how you can obtain a clean support script file\.

If you have a clean support script file, before running the script make sure to read and understand the SQL from both a performance and security perspective\. If you aren't comfortable running any of the SQL in this script, you can comment out or remove the problem SQL\. You can also consult with your support engineer about any acceptable workarounds\.

Upon successful completion and unless otherwise noted, the script returns output in a readable HTML format\. The script is designed to exclude from this HTML any data or security details that might compromise your business\. It also makes no modifications to your database or its environment\. However, if you find any information in the HTML that you are uncomfortable sharing, feel free to remove the problem information before uploading the HTML\. When the HTML is acceptable, upload it using the **Attachments** in the **Case details** of your support case\.

Each of the following topics describes the scripts available for a supported AWS DMS database and how to run them\. Your support engineer will direct you to a specific script documented following\.

**Topics**
+ [Oracle diagnostic support scripts](CHAP_SupportScripts.Oracle.md)
+ [SQL Server diagnostic support scripts](CHAP_SupportScripts.SQLServer.md)
+ [Diagnostic support scripts for MySQL\-compatible databases](CHAP_SupportScripts.MySQL.md)
+ [PostgreSQL diagnostic support scripts](CHAP_SupportScripts.PostgreSQL.md)