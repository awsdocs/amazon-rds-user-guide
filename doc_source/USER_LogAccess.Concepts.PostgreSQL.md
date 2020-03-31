# PostgreSQL Database Log Files<a name="USER_LogAccess.Concepts.PostgreSQL"></a>

Amazon RDS for PostgreSQL generates query and error logs\. RDS PostgreSQL writes autovacuum information and rds\_admin actions to the error log\. PostgreSQL also logs connections, disconnections, and checkpoints to the error log\. For more information, see the [Error Reporting and Logging](https://www.postgresql.org/docs/current/runtime-config-logging.html) in the PostgreSQL documentation\.

To set logging parameters for a DB instance, set the parameters in a DB parameter group and associate that parameter group with the DB instance\. For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

**Topics**
+ [Setting the Log Retention Period](#USER_LogAccess.PostgreSQL.log_retention_period)
+ [Using Query Logging](#USER_LogAccess.PostgreSQL.Query_Logging)
+ [Publishing PostgreSQL Logs to CloudWatch Logs](#USER_LogAccess.PostgreSQL.PublishtoCloudWatchLogs)

## Setting the Log Retention Period<a name="USER_LogAccess.PostgreSQL.log_retention_period"></a>

To set the retention period for system logs, use the `rds.log_retention_period` parameter\. You can find `rds.log_retention_period` in the DB parameter group associated with your DB instance\. The unit for this parameter is minutes\. For example, a setting of 1,440 retains logs for one day\. The default value is 4,320 \(three days\)\. The maximum value is 10,080 \(seven days\)\. Your instance must have enough allocated storage to contain the retained log files\. 

To retain older logs, publish them to Amazon CloudWatch Logs\. For more information, see [Publishing PostgreSQL Logs to CloudWatch Logs](#USER_LogAccess.PostgreSQL.PublishtoCloudWatchLogs)\.  

## Using Query Logging<a name="USER_LogAccess.PostgreSQL.Query_Logging"></a>

To enable query logging for your PostgreSQL DB instance, set two parameters in the DB parameter group associated with your DB instance: `log_statement` and `log_min_duration_statement`\. 

The `log_statement` parameter controls which SQL statements are logged\. We recommend that you set this parameter to `all` to log all statements when you debug issues in your DB instance\. The default value is `none`\. To log all data definition language \(DDL\) statements \(CREATE, ALTER, DROP, and so on\), set this value to `ddl`\. To log all DDL and data modification language \(DML\) statements \(INSERT, UPDATE, DELETE, and so on\), set this value to `mod`\.

The `log_min_duration_statement` parameter sets the limit in milliseconds of a statement to be logged\. All SQL statements that run longer than the parameter setting are logged\. This parameter is disabled and set to \-1 by default\. Enabling this parameter can help you find unoptimized queries\. 

To set up query logging, take the following steps:

1. Set the `log_statement` parameter to `all`\. The following example shows the information that is written to the `postgres.log` file\.

   ```
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  received SIGHUP, reloading configuration files
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  parameter "log_statement" changed to "all"
   ```

   Additional information is written to the postgres\.log file when you run a query\. The following example shows the type of information written to the file after a query\.

   ```
   2013-11-05 16:41:07 UTC::@:[2955]:LOG:  checkpoint starting: time
   2013-11-05 16:41:07 UTC::@:[2955]:LOG:  checkpoint complete: wrote 1 buffers (0.3%); 0 transaction log file(s) added, 0 removed, 1 recycled; write=0.000 s, sync=0.003 s, total=0.012 s; sync files=1, longest=0.003 s, average=0.003 s
   2013-11-05 16:45:14 UTC:[local]:master@postgres:[8839]:LOG:  statement: SELECT d.datname as "Name",
   	       pg_catalog.pg_get_userbyid(d.datdba) as "Owner",
   	       pg_catalog.pg_encoding_to_char(d.encoding) as "Encoding",
   	       d.datcollate as "Collate",
   	       d.datctype as "Ctype",
   	       pg_catalog.array_to_string(d.datacl, E'\n') AS "Access privileges"
   	FROM pg_catalog.pg_database d
   	ORDER BY 1;
   2013-11-05 16:45:
   ```

1. Set the `log_min_duration_statement` parameter\. The following example shows the information that is written to the `postgres.log` file when the parameter is set to `1`\.

   ```
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  received SIGHUP, reloading configuration files
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  parameter "log_min_duration_statement" changed to "1"
   ```

   Additional information is written to the `postgres.log` file when you run a query that exceeds the duration parameter setting\. The following example shows the type of information written to the file after a query\.

   ```
   2013-11-05 16:51:10 UTC:[local]:master@postgres:[9193]:LOG:  statement: SELECT c2.relname, i.indisprimary, i.indisunique, i.indisclustered, i.indisvalid, pg_catalog.pg_get_indexdef(i.indexrelid, 0, true),
   	  pg_catalog.pg_get_constraintdef(con.oid, true), contype, condeferrable, condeferred, c2.reltablespace
   	FROM pg_catalog.pg_class c, pg_catalog.pg_class c2, pg_catalog.pg_index i
   	  LEFT JOIN pg_catalog.pg_constraint con ON (conrelid = i.indrelid AND conindid = i.indexrelid AND contype IN ('p','u','x'))
   	WHERE c.oid = '1255' AND c.oid = i.indrelid AND i.indexrelid = c2.oid
   	ORDER BY i.indisprimary DESC, i.indisunique DESC, c2.relname;
   2013-11-05 16:51:10 UTC:[local]:master@postgres:[9193]:LOG:  duration: 3.367 ms
   2013-11-05 16:51:10 UTC:[local]:master@postgres:[9193]:LOG:  statement: SELECT c.oid::pg_catalog.regclass FROM pg_catalog.pg_class c, pg_catalog.pg_inherits i WHERE c.oid=i.inhparent AND i.inhrelid = '1255' ORDER BY inhseqno;
   2013-11-05 16:51:10 UTC:[local]:master@postgres:[9193]:LOG:  duration: 1.002 ms
   2013-11-05 16:51:10 UTC:[local]:master@postgres:[9193]:LOG:  statement: SELECT c.oid::pg_catalog.regclass FROM pg_catalog.pg_class c, pg_catalog.pg_inherits i WHERE c.oid=i.inhrelid AND i.inhparent = '1255' ORDER BY c.oid::pg_catalog.regclass::pg_catalog.text;
   2013-11-05 16:51:18 UTC:[local]:master@postgres:[9193]:LOG:  statement: select proname from pg_proc;
   2013-11-05 16:51:18 UTC:[local]:master@postgres:[9193]:LOG:  duration: 3.469 ms
   ```

## Publishing PostgreSQL Logs to CloudWatch Logs<a name="USER_LogAccess.PostgreSQL.PublishtoCloudWatchLogs"></a>

To store your PostgreSQL log records in highly durable storage, you can use CloudWatch Logs\. With CloudWatch Logs, you can also perform real\-time analysis of log data and use CloudWatch to view metrics and create alarms\. 

To work with CloudWatch Logs, configure your RDS for PostgreSQL DB instance to publish log data to a log group\.

**Note**  
Publishing log files to CloudWatch Logs is supported only for PostgreSQL versions 9\.6\.6 and later and 10\.4 and later\.

You can publish the following log types to CloudWatch Logs for RDS for PostgreSQL: 
+ Postgresql log
+ Upgrade log \(not available for Aurora PostgreSQL\)

After you complete the configuration, Amazon RDS publishes the log events to log streams within a CloudWatch log group\. For example, the PostgreSQL log data is stored within the log group `/aws/rds/instance/my_instance/postgresql`\. To view your logs, open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

### Console<a name="USER_LogAccess.PostgreSQL.PublishtoCloudWatchLogs.CON"></a>

**To publish PostgreSQL logs to CloudWatch Logs using the console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to modify, and then choose **Modify**\.

1. In the **Log exports** section, choose the logs that you want to start publishing to CloudWatch Logs\.

   The **Log exports** section is available only for PostgreSQL versions that support publishing to CloudWatch Logs\. 

1. Choose **Continue**, and then choose **Modify DB Instance** on the summary page\.

### AWS CLI<a name="USER_LogAccess.PostgreSQL.PublishtoCloudWatchLogs.CLI"></a>

You can publish PostgreSQL logs with the AWS CLI\. You can call the [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the following parameters: 
+ `--db-instance-identifier`
+ `--cloudwatch-logs-export-configuration`

**Note**  
A change to the `--cloudwatch-logs-export-configuration` option is always applied to the DB instance immediately\. Therefore, the `--apply-immediately` and `--no-apply-immediately` options have no effect\.

You can also publish PostgreSQL logs by calling the following CLI commands: 
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html)
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html)

Run one of these CLI commands with the following options: 
+ `--db-instance-identifier`
+ `--enable-cloudwatch-logs-exports`
+ `--db-instance-class`
+ `--engine`

Other options might be required depending on the CLI command you run\.

**Example Modify an instance to publish logs to CloudWatch Logs**  
The following example modifies an existing PostgreSQL DB instance to publish log files to CloudWatch Logs\. The `--cloudwatch-logs-export-configuration` value is a JSON object\. The key for this object is `EnableLogTypes`, and its value is an array of strings with any combination of `postgresql` and `upgrade`\.  
For Linux, macOS, or Unix:  

```
1. aws rds modify-db-instance \
2.     --db-instance-identifier mydbinstance \
3.     --cloudwatch-logs-export-configuration '{"EnableLogTypes":["postgresql", "upgrade"]}'
```
For Windows:  

```
1. aws rds modify-db-instance ^
2.     --db-instance-identifier mydbinstance ^
3.     --cloudwatch-logs-export-configuration '{"EnableLogTypes":["postgresql","upgrade"]}'
```

**Example Create an instance to publish logs to CloudWatch Logs**  
The following example creates a PostgreSQL DB instance and publishes log files to CloudWatch Logs\. The `--enable-cloudwatch-logs-exports` value is a JSON array of strings\. The strings can be any combination of `postgresql` and `upgrade`\.  
For Linux, macOS, or Unix:  

```
1. aws rds create-db-instance \
2.     --db-instance-identifier mydbinstance \
3.     --enable-cloudwatch-logs-exports '["postgresql","upgrade"]' \
4.     --db-instance-class db.m4.large \
5.     --engine postgres
```
For Windows:  

```
1. aws rds create-db-instance ^
2.     --db-instance-identifier mydbinstance ^
3.     --enable-cloudwatch-logs-exports '["postgresql","upgrade"]' ^
4.     --db-instance-class db.m4.large ^
5.     --engine postgres
```

### RDS API<a name="USER_LogAccess.PostgreSQL.PublishtoCloudWatchLogs.API"></a>

You can publish PostgreSQL logs with the RDS API\. You can call the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) action with the following parameters: 
+ `DBInstanceIdentifier`
+ `CloudwatchLogsExportConfiguration`

**Note**  
A change to the `CloudwatchLogsExportConfiguration` parameter is always applied to the DB instance immediately\. Therefore, the `ApplyImmediately` parameter has no effect\.

You can also publish PostgreSQL logs by calling the following RDS API operations: 
+ [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html)
+ [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html)
+ [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html)

Run one of these RDS API operations with the following parameters: 
+ `DBInstanceIdentifier`
+ `EnableCloudwatchLogsExports`
+ `Engine`
+ `DBInstanceClass`

Other parameters might be required depending on the operation that you run\.