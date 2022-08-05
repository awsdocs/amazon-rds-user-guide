# Oracle database log files<a name="USER_LogAccess.Concepts.Oracle"></a>

You can access Oracle alert logs, audit files, and trace files by using the Amazon RDS console or API\. For more information about viewing, downloading, and watching file\-based database logs, see [Monitoring Amazon RDS log files](USER_LogAccess.md)\. 

The Oracle audit files provided are the standard Oracle auditing files\. Amazon RDS supports the Oracle fine\-grained auditing \(FGA\) feature\. However, log access doesn't provide access to FGA events that are stored in the `SYS.FGA_LOG$` table and that are accessible through the `DBA_FGA_AUDIT_TRAIL` view\. 

The [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBLogFiles.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBLogFiles.html) API operation that lists the Oracle log files that are available for a DB instance ignores the `MaxRecords` parameter and returns up to 1,000 records\. The call returns `LastWritten` as a POSIX date in milliseconds\.

**Topics**
+ [Retention schedule](#USER_LogAccess.Concepts.Oracle.Retention)
+ [Working with Oracle trace files](#USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles)
+ [Publishing Oracle logs to Amazon CloudWatch Logs](#USER_LogAccess.Oracle.PublishtoCloudWatchLogs)
+ [Previous methods for accessing alert logs and listener logs](#USER_LogAccess.Concepts.Oracle.AlertLogAndListenerLog)

## Retention schedule<a name="USER_LogAccess.Concepts.Oracle.Retention"></a>

The Oracle database engine might rotate log files if they get very large\. To retain audit or trace files, download them\. If you store the files locally, you reduce your Amazon RDS storage costs and make more space available for your data\. 

The following table shows the retention schedule for Oracle alert logs, audit files, and trace files on Amazon RDS\. 


****  

| Log type | Retention schedule | 
| --- | --- | 
|  Alert logs  |   The text alert log is rotated daily with 30\-day retention managed by Amazon RDS\. The XML alert log is retained for at least seven days\. You can access this log by using the `ALERTLOG` view\.    | 
|  Audit files  |   The default retention period for audit files is seven days\. Amazon RDS might delete audit files older than seven days\.    | 
|  Trace files  |  The default retention period for trace files is seven days\. Amazon RDS might delete trace files older than seven days\.    | 
|  Listener logs  |   The default retention period for the listener logs is seven days\. Amazon RDS might delete listener logs older than seven days\.    | 

**Note**  
Audit files and trace files share the same retention configuration\.

## Working with Oracle trace files<a name="USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles"></a>

Following, you can find descriptions of Amazon RDS procedures to create, refresh, access, and delete trace files\.

**Topics**
+ [Listing files](#USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles.ViewingBackgroundDumpDest)
+ [Generating trace files and tracing a session](#USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles.Generating)
+ [Retrieving trace files](#USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles.Retrieving)
+ [Purging trace files](#USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles.Purging)

### Listing files<a name="USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles.ViewingBackgroundDumpDest"></a>

You can use either of two procedures to allow access to any file in the `background_dump_dest` path\. The first procedure refreshes a view containing a listing of all files currently in `background_dump_dest`\. 

```
1. EXEC rdsadmin.manage_tracefiles.refresh_tracefile_listing;
```

After the view is refreshed, query the following view to access the results\.

```
1. SELECT * FROM rdsadmin.tracefile_listing;
```

An alternative to the previous process is to use `FROM table` to stream nonrelational data in a table\-like format to list database directory contents\.

```
1. SELECT * FROM TABLE(rdsadmin.rds_file_util.listdir('BDUMP'));
```

The following query shows the text of a log file\.

```
1. SELECT text FROM TABLE(rdsadmin.rds_file_util.read_text_file('BDUMP','alert_dbname.log.date'));
```

On a read replica, get the name of the BDUMP directory by querying `V$DATABASE.DB_UNIQUE_NAME`\. If the unique name is `DATABASE_B`, then the BDUMP directory is `BDUMP_B`\. The following example queries the BDUMP name on a replica and then uses this name to query the contents of `alert_DATABASE.log.2020-06-23`\.

```
1. SELECT 'BDUMP' || (SELECT regexp_replace(DB_UNIQUE_NAME,'.*(_[A-Z])', '\1') FROM V$DATABASE) AS BDUMP_VARIABLE FROM DUAL;
2. 
3. BDUMP_VARIABLE
4. --------------
5. BDUMP_B
6. 
7. SELECT TEXT FROM table(rdsadmin.rds_file_util.read_text_file('BDUMP_B','alert_DATABASE.log.2020-06-23'));
```

### Generating trace files and tracing a session<a name="USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles.Generating"></a>

Because there are no restrictions on `ALTER SESSION`, many standard methods to generate trace files in Oracle remain available to an Amazon RDS DB instance\. The following procedures are provided for trace files that require greater access\. 


****  

|  Oracle method  |  Amazon RDS method | 
| --- | --- | 
|  `oradebug hanganalyze 3 `  |  `EXEC rdsadmin.manage_tracefiles.hanganalyze; `  | 
|  `oradebug dump systemstate 266 `  |  `EXEC rdsadmin.manage_tracefiles.dump_systemstate;`  | 

You can use many standard methods to trace individual sessions connected to an Oracle DB instance in Amazon RDS\. To enable tracing for a session, you can run subprograms in PL/SQL packages supplied by Oracle, such as `DBMS_SESSION` and `DBMS_MONITOR`\. For more information, see [ Enabling tracing for a session](https://docs.oracle.com/database/121/TGSQL/tgsql_trace.htm#GUID-F872D6F9-E015-481F-80F6-8A7036A6AD29) in the Oracle documentation\. 

### Retrieving trace files<a name="USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles.Retrieving"></a>

You can retrieve any trace file in `background_dump_dest` using a standard SQL query on an Amazon RDS–managed external table\. To use this method, you must execute the procedure to set the location for this table to the specific trace file\. 

For example, you can use the `rdsadmin.tracefile_listing` view mentioned preceding to list all of the trace files on the system\. You can then set the `tracefile_table` view to point to the intended trace file using the following procedure\. 

```
1. EXEC rdsadmin.manage_tracefiles.set_tracefile_table_location('CUST01_ora_3260_SYSTEMSTATE.trc');
```

The following example creates an external table in the current schema with the location set to the file provided\. You can retrieve the contents into a local file using a SQL query\. 

```
1. SPOOL /tmp/tracefile.txt
2. SELECT * FROM tracefile_table;
3. SPOOL OFF;
```

### Purging trace files<a name="USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles.Purging"></a>

Trace files can accumulate and consume disk space\. Amazon RDS purges trace files by default and log files that are older than seven days\. You can view and set the trace file retention period using the `show_configuration` procedure\. You should run the command `SET SERVEROUTPUT ON` so that you can view the configuration results\. 

The following example shows the current trace file retention period, and then sets a new trace file retention period\. 

```
 1. # Show the current tracefile retention
 2. SQL> EXEC rdsadmin.rdsadmin_util.show_configuration;
 3. NAME:tracefile retention
 4. VALUE:10080
 5. DESCRIPTION:tracefile expiration specifies the duration in minutes before tracefiles in bdump are automatically deleted.
 6. 		
 7. # Set the tracefile retention to 24 hours:
 8. SQL> EXEC rdsadmin.rdsadmin_util.set_configuration('tracefile retention',1440);
 9. SQL> commit;
10. 
11. #show the new tracefile retention
12. SQL> EXEC rdsadmin.rdsadmin_util.show_configuration;
13. NAME:tracefile retention
14. VALUE:1440
15. DESCRIPTION:tracefile expiration specifies the duration in minutes before tracefiles in bdump are automatically deleted.
```

In addition to the periodic purge process, you can manually remove files from the `background_dump_dest`\. The following example shows how to purge all files older than five minutes\. 

```
EXEC rdsadmin.manage_tracefiles.purge_tracefiles(5);
```

You can also purge all files that match a specific pattern \(if you do, don't include the file extension, such as \.trc\)\. The following example shows how to purge all files that start with `SCHPOC1_ora_5935`\. 

```
1. EXEC rdsadmin.manage_tracefiles.purge_tracefiles('SCHPOC1_ora_5935');
```

## Publishing Oracle logs to Amazon CloudWatch Logs<a name="USER_LogAccess.Oracle.PublishtoCloudWatchLogs"></a>

You can configure your Amazon RDS for Oracle DB instance to publish log data to a log group in Amazon CloudWatch Logs\. With CloudWatch Logs, you can analyze the log data, and use CloudWatch to create alarms and view metrics\. You can use CloudWatch Logs to store your log records in highly durable storage\. 

Amazon RDS publishes each Oracle database log as a separate database stream in the log group\. For example, if you configure the export function to include the audit log, audit data is stored in an audit log stream in the `/aws/rds/instance/my_instance/audit` log group\. RDS for Oracle supports the following logs:
+ Alert log
+ Trace log
+ Audit log
+ Listener log
+ Oracle Management Agent log

This Oracle Management Agent log consists of the log groups shown in the following table\.


****  

| Log name | CloudWatch log group | 
| --- | --- | 
| emctl\.log | oemagent\-emctl | 
| emdctlj\.log | oemagent\-emdctlj | 
| gcagent\.log | oemagent\-gcagent | 
| gcagent\_errors\.log | oemagent\-gcagent\-errors | 
| emagent\.nohup | oemagent\-emagent\-nohup | 
| secure\.log | oemagent\-secure | 

For more information, see [Locating Management Agent Log and Trace Files](https://docs.oracle.com/en/enterprise-manager/cloud-control/enterprise-manager-cloud-control/13.4/emadm/locating-management-agent-log-and-trace-files1.html#GUID-9C710D78-6AA4-42E4-83CD-47B5FF4892DF) in the Oracle documentation\.

### Console<a name="USER_LogAccess.Oracle.PublishtoCloudWatchLogs.console"></a>

**To publish Oracle DB logs to CloudWatch Logs from the AWS Management Console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to modify\.

1. Choose **Modify**\.

1. In the **Log exports** section, choose the logs that you want to start publishing to CloudWatch Logs\.

1. Choose **Continue**, and then choose **Modify DB Instance** on the summary page\.

### AWS CLI<a name="USER_LogAccess.Oracle.PublishtoCloudWatchLogs.CLI"></a>

To publish Oracle logs, you can use the [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the following parameters: 
+ `--db-instance-identifier`
+ `--cloudwatch-logs-export-configuration`

**Note**  
A change to the `--cloudwatch-logs-export-configuration` option is always applied to the DB instance immediately\. Therefore, the `--apply-immediately` and `--no-apply-immediately` options have no effect\.

You can also publish Oracle logs using the following commands: 
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html)
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-s3.html](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-s3.html)
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html)

**Example**  
The following example creates an Oracle DB instance with CloudWatch Logs publishing enabled\. The `--cloudwatch-logs-export-configuration` value is a JSON array of strings\. The strings can be any combination of `alert`, `audit`, `listener`, and `trace`\.  
For Linux, macOS, or Unix:  

```
aws rds create-db-instance \
    --db-instance-identifier mydbinstance \
    --cloudwatch-logs-export-configuration '["trace","audit","alert","listener","oemagent"]' \
    --db-instance-class db.m5.large \
    --allocated-storage 20 \
    --engine oracle-ee \
    --engine-version 12.1.0.2.v18 \
    --license-model bring-your-own-license \
    --master-username myadmin \
    --master-user-password mypassword
```
For Windows:  

```
aws rds create-db-instance ^
    --db-instance-identifier mydbinstance ^
    --cloudwatch-logs-export-configuration trace alert audit listener oemagent ^
    --db-instance-class db.m5.large ^
    --allocated-storage 20 ^
    --engine oracle-ee ^
    --engine-version 12.1.0.2.v18 ^
    --license-model bring-your-own-license ^
    --master-username myadmin ^
    --master-user-password mypassword
```

**Example**  
The following example modifies an existing Oracle DB instance to publish log files to CloudWatch Logs\. The `--cloudwatch-logs-export-configuration` value is a JSON object\. The key for this object is `EnableLogTypes`, and its value is an array of strings with any combination of `alert`, `audit`, `listener`, and `trace`\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --cloudwatch-logs-export-configuration '{"EnableLogTypes":["trace","alert","audit","listener","oemagent"]}'
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier mydbinstance ^
    --cloudwatch-logs-export-configuration EnableLogTypes=\"trace\",\"alert\",\"audit\",\"listener\",\"oemagent\"
```

**Example**  
The following example modifies an existing Oracle DB instance to disable publishing audit and listener log files to CloudWatch Logs\. The `--cloudwatch-logs-export-configuration` value is a JSON object\. The key for this object is `DisableLogTypes`, and its value is an array of strings with any combination of `alert`, `audit`, `listener`, and `trace`\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --cloudwatch-logs-export-configuration '{"DisableLogTypes":["audit","listener"]}'
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier mydbinstance ^
    --cloudwatch-logs-export-configuration DisableLogTypes=\"audit\",\"listener\"
```

### RDS API<a name="USER_LogAccess.Oracle.PublishtoCloudWatchLogs.API"></a>

You can publish Oracle DB logs with the RDS API\. You can call the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) action with the following parameters: 
+ `DBInstanceIdentifier`
+ `CloudwatchLogsExportConfiguration`

**Note**  
A change to the `CloudwatchLogsExportConfiguration` parameter is always applied to the DB instance immediately\. Therefore, the `ApplyImmediately` parameter has no effect\.

You can also publish Oracle logs by calling the following RDS API operations: 
+ [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html)
+ [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html)
+ [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromS3.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromS3.html)
+ [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html)

Run one of these RDS API operations with the following parameters: 
+ `DBInstanceIdentifier`
+ `EnableCloudwatchLogsExports`
+ `Engine`
+ `DBInstanceClass`

Other parameters might be required depending on the RDS operation that you run\.

## Previous methods for accessing alert logs and listener logs<a name="USER_LogAccess.Concepts.Oracle.AlertLogAndListenerLog"></a>

You can view the alert log using the Amazon RDS console\. You can also use the following SQL statement to access the alert log\.

```
1. SELECT message_text FROM alertlog;
```

The `listenerlog` view contains entries for Oracle Database version 12\.1\.0\.2 and earlier\. To access the listener log for these database versions, use the following query\.

```
1. SELECT message_text FROM listenerlog;
```

For Oracle Database versions 12\.2\.0\.1 and later, access the listener log using Amazon CloudWatch Logs\.

**Note**  
Oracle rotates the alert and listener logs when they exceed 10 MB, at which point they are unavailable from Amazon RDS views\.