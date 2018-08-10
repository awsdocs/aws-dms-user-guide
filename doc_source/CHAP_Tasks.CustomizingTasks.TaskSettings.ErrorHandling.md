# Error Handling Task Settings<a name="CHAP_Tasks.CustomizingTasks.TaskSettings.ErrorHandling"></a>

You can set the error handling behavior of your replication task during change data capture \(CDC\) using the following settings:
+ `DataErrorPolicy` – Determines the action AWS DMS takes when there is an error related to data processing at the record level\. Some examples of data processing errors include conversion errors, errors in transformation, and bad data\. The default is `LOG_ERROR`\.
  + `IGNORE_RECORD` – The task continues and the data for that record is ignored\. The error counter for the `DataErrorEscalationCount` property is incremented so that if you set a limit on errors for a table, this error will count toward that limit\. 
  + `LOG_ERROR` – The task continues and the error is written to the task log\.
  + `SUSPEND_TABLE` – The task continues but data from the table with the error record is moved into an error state and the data is not replicated\.
  + `STOP_TASK` – The task stops and manual intervention is required\.
+ `DataTruncationErrorPolicy` – Determines the action AWS DMS takes when data is truncated\. The default is `LOG_ERROR`\.
  + `IGNORE_RECORD` – The task continues and the data for that record is ignored\. The error counter for the `DataErrorEscalationCount` property is incremented so that if you set a limit on errors for a table, this error will count toward that limit\. 
  + `LOG_ERROR` – The task continues and the error is written to the task log\.
  + `SUSPEND_TABLE` – The task continues but data from the table with the error record is moved into an error state and the data is not replicated\.
  + `STOP_TASK` – The task stops and manual intervention is required\.
+ `DataErrorEscalationPolicy` – Determines the action AWS DMS takes when the maximum number of errors \(set in the `DataErrorsEscalationCount` parameter\) is reached\. The default is `SUSPEND_TABLE`\.
  + `SUSPEND_TABLE` – The task continues but data from the table with the error record is moved into an error state and the data is not replicated\.
  + `STOP_TASK` – The task stops and manual intervention is required\.
+ `DataErrorEscalationCount` – Sets the maximum number of errors that can occur to the data for a specific record\. When this number is reached, the data for the table that contains the error record is handled according to the policy set in the `DataErrorEscalationCount`\. The default is 0\. 
+ `TableErrorPolicy` – Determines the action AWS DMS takes when an error occurs when processing data or metadata for a specific table\. This error only applies to general table data and is not an error that relates to a specific record\. The default is `SUSPEND_TABLE`\.
  + `SUSPEND_TABLE` – The task continues but data from the table with the error record is moved into an error state and the data is not replicated\.
  + `STOP_TASK` – The task stops and manual intervention is required\.
+ `TableErrorEscalationPolicy` – Determines the action AWS DMS takes when the maximum number of errors \(set using the `TableErrorEscalationCount` parameter\)\. The default and only user setting is `STOP_TASK`, where the task is stopped and manual intervention is required\.
+ `TableErrorEscalationCount` – The maximum number of errors that can occur to the general data or metadata for a specific table\. When this number is reached, the data for the table is handled according to the policy set in the `TableErrorEscalationPolicy`\. The default is 0\. 
+ `RecoverableErrorCount` – The maximum number of attempts made to restart a task when an environmental error occurs\. After the system attempts to restart the task the designated number of times, the task is stopped and manual intervention is required\. The default value is \-1, which instructs AWS DMS to attempt to restart the task indefinitely\. Set this value to 0 to never attempt to restart a task\. If a fatal error occurs, AWS DMS stops attempting to restart the task after 6 attempts\.
+ `RecoverableErrorInterval` – The number of seconds that AWS DMS waits between attempts to restart a task\. The default is 5\. 
+ `RecoverableErrorThrottling` – When enabled, the interval between attempts to restart a task is increased each time a restart is attempted\. The default is `true`\. 
+ `RecoverableErrorThrottlingMax` – The maximum number of seconds that AWS DMS waits between attempts to restart a task if `RecoverableErrorThrottling` is enabled\. The default is 1800\. 
+ `ApplyErrorDeletePolicy` – Determines what action AWS DMS takes when there is a conflict with a DELETE operation\. The default is `IGNORE_RECORD`\.
  + `IGNORE_RECORD` – The task continues and the data for that record is ignored\. The error counter for the `ApplyErrorEscalationCount` property is incremented so that if you set a limit on errors for a table, this error will count toward that limit\. 
  + `LOG_ERROR` – The task continues and the error is written to the task log\.
  + `SUSPEND_TABLE` – The task continues but data from the table with the error record is moved into an error state and the data is not replicated\.
  + `STOP_TASK` – The task stops and manual intervention is required\.
+ `ApplyErrorInsertPolicy` – Determines what action AWS DMS takes when there is a conflict with an INSERT operation\. The default is `LOG_ERROR`\.
  + `IGNORE_RECORD` – The task continues and the data for that record is ignored\. The error counter for the `ApplyErrorEscalationCount` property is incremented so that if you set a limit on errors for a table, this error will count toward that limit\. 
  + `LOG_ERROR` – The task continues and the error is written to the task log\.
  + `SUSPEND_TABLE` – The task continues but data from the table with the error record is moved into an error state and the data is not replicated\.
  + `STOP_TASK` – The task stops and manual intervention is required\.
  + `INSERT_RECORD` – If there is an existing target record with the same primary key as the inserted source record, the target record is updated\.
+ `ApplyErrorUpdatePolicy` – Determines what action AWS DMS takes when there is a conflict with an UPDATE operation\. The default is `LOG_ERROR`\.
  + `IGNORE_RECORD` – The task continues and the data for that record is ignored\. The error counter for the `ApplyErrorEscalationCount` property is incremented so that if you set a limit on errors for a table, this error will count toward that limit\. 
  + `LOG_ERROR` – The task continues and the error is written to the task log\.
  + `SUSPEND_TABLE` – The task continues but data from the table with the error record is moved into an error state and the data is not replicated\.
  + `STOP_TASK` – The task stops and manual intervention is required\.
  + `UPDATE_RECORD` – If the target record is missing, the missing target record will be inserted into the target table\. Selecting this option requires full supplemental logging to be enabled for all the source table columns when Oracle is the source database\.
+ `ApplyErrorEscalationPolicy` – Determines what action AWS DMS takes when the maximum number of errors \(set using the `ApplyErrorsEscalationCount` parameter\) is reached\.
  + `LOG_ERROR` – The task continues and the error is written to the task log\.
  + `SUSPEND_TABLE` – The task continues but data from the table with the error record is moved into an error state and the data is not replicated\.
  + `STOP_TASK` – The task stops and manual intervention is required\.
+ `ApplyErrorEscalationCount` – Sets the maximum number of APPLY conflicts that can occur for a specific table during a change process operation\. When this number is reached, the data for the table is handled according to the policy set in the `ApplyErrorEscalationPolicy` parameter\. The default is 0\. 
+ `ApplyErrorFailOnTruncationDdl` – Set this to `true` to cause the task to fail when a truncation is performed on any of the tracked tables during CDC\. The failure message will be: “Truncation DDL detected\.” The default is `false`\. 

  This does not work with PostgreSQL or any other source endpoint the does not replicate DDL table truncation\.
+ `FailOnNoTablesCaptured` – Set this to `true` to cause a task to fail when the transformation rules defined for a task find no tables when the task starts\. The default is `false`\. 
+ `FailOnTransactionConsistencyBreached` – This option applies to tasks using Oracle as a source with CDC\. Set this to `true` to cause a task to fail when a transaction is open for more time than the specified timeout and could be dropped\. 

  When a CDC task starts with Oracle AWS DMS waits for a limited time for the oldest open transaction to close before starting CDC\. If the oldest open transaction doesn't close until the timeout is reached, then we normally start CDC anyway, ignoring that transaction\. if this setting is set to `true`, the task fails\.
+ `FullLoadIgnoreConflicts` – Set this to `false` to have AWS DMS ignore "zero rows affected" and "duplicates" errors when applying cached events\. If set to `true`, AWS DMS reports all errors instead of ignoring them\. The default is `false`\. 