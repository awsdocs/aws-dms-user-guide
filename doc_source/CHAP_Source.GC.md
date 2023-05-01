# Using Google Cloud for MySQL as a source for AWS DMS<a name="CHAP_Source.GC"></a>

With AWS DMS, you can use Google Cloud for MySQL as a source in much the same way as you do MySQL\. 

For information about version of GCP MySQL that AWS DMS supports as a source, see [Sources for AWS DMS](CHAP_Introduction.Sources.md)\.  

For more information, see [Using a MySQL\-compatible database as a source for AWS DMS](CHAP_Source.MySQL.md)\.

**Note**  
Support for GCP MySQL 8\.0 as a source is available in AWS DMS version 3\.4\.6\.  
AWS DMS doesn't support the SSL mode `verify-full` for GCP for MySQL instances\.  
The GCP MySQL security setting `Allow only SSL connections` isn't supported, because it requires both server and client certificate verification\. AWS DMS only supports server certificate verification\.  
AWS DMS supports the default GCP CloudSQL for MySQL value of `CRC32` for the `binlog_checksum` database flag\.