# Using the Time Travel logs<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.TimeTravel.LogSchema"></a>

*Time Travel log files* are comma\-separated value \(CSV\) files with the fields following\.

```
log_timestamp 
component 
dms_source_code_location 
transaction_id 
event_id 
event_timestamp 
lsn/scn 
primary_key
record_type 
event_type 
schema_name 
table_name 
statement 
action 
result 
raw_data
```

After your Time Travel logs are available in S3, you can directly access and query them with tools such as Amazon Athena\. Or you can download the logs as you can any file from S3\.

The example following shows a Time Travel log where transactions for a table called `mytable` are logged\. The line endings for the following log are added for readability\.

```
"log_timestamp ","tt_record_type","dms_source_code_location ","transaction_id",
"event_id","event_timestamp","scn_lsn","primary_key","record_type","event_type",
"schema_name","table_name","statement","action","result","raw_data"
"2021-09-23T01:03:00:778230","SOURCE_CAPTURE","postgres_endpoint_wal_engine.c:00819",
"609284109","565612992","2021-09-23 01:03:00.765321+00","00000E9C/D53AB518","","DML",
"UPDATE (3)","dmstest","mytable","","Migrate","","table dmstest.mytable:
UPDATE: id[bigint]:2244937 phone_number[character varying]:'phone-number-482'
age[integer]:82 gender[character]:'f' isactive[character]:'true ' 
date_of_travel[timestamp without time zone]:'2021-09-23 01:03:00.76593' 
description[text]:'TEST DATA TEST DATA TEST DATA TEST DATA'"
```