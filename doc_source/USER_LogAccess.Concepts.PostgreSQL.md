# PostgreSQL database log files<a name="USER_LogAccess.Concepts.PostgreSQL"></a>

RDS for PostgreSQL generates query and error logs\. You can use log messages to troubleshoot performance and auditing issues while using the database\.

To view, download, and watch file\-based database logs, see [Monitoring Amazon RDS log files](USER_LogAccess.md)\. 

**Topics**
+ [Overview of PostgreSQL logs](#USER_LogAccess.Concepts.PostgreSQL.overview)
+ [Enabling query logging](#USER_LogAccess.Concepts.PostgreSQL.Query_Logging)
+ [Publishing PostgreSQL logs to Amazon CloudWatch Logs](#USER_LogAccess.Concepts.PostgreSQL.PublishtoCloudWatchLogs)

## Overview of PostgreSQL logs<a name="USER_LogAccess.Concepts.PostgreSQL.overview"></a>

PostgreSQL generates event log files that contain useful information for DBAs\.

### Log contents<a name="USER_LogAccess.Concepts.PostgreSQL.overview.log-contents"></a>

The default logging level captures errors that affect your server\. By default, Amazon RDS PostgreSQL logging parameters capture all server errors, including the following:
+ Query failures
+ Login failures
+ Fatal server errors
+ Deadlocks

To identify application issues, you can look for query failures, login failures, deadlocks, and fatal server errors in the log\. For example, if you converted a legacy application from Oracle to Amazon RDS PostgreSQL, some queries may not convert correctly\. These incorrectly formatted queries generate error messages in the logs, which you can use to identify the problematic code\.

You can modify PostgreSQL logging parameters to capture additional information based on the following categories:
+ Connections and disconnections
+ Checkpoints
+ Schema modification queries
+ Queries waiting for locks
+ Queries consuming temporary disk storage
+ Backend autovacuum process consuming resources

By logging information for various categories such as shown in the list, you can troubleshoot potential performance and auditing issues\. For more information, see [Error reporting and logging](https://www.postgresql.org/docs/current/runtime-config-logging.html) in the PostgreSQL documentation\. For a useful AWS blog about PostgreSQL logging, see [Working with RDS and Aurora PostgreSQL logs: Part 1](http://aws.amazon.com/blogs/database/working-with-rds-and-aurora-postgresql-logs-part-1/) and [ Working with RDS and Aurora PostgreSQL logs: Part 2](http://aws.amazon.com/blogs/database/working-with-rds-and-aurora-postgresql-logs-part-2/)\.

### Parameters that affect logging behavior<a name="USER_LogAccess.Concepts.PostgreSQL.overview.parameter-groups"></a>

Each Amazon RDS PostgreSQL instance has a *parameter group* that specifies its configuration, including various aspects of logging\. The default parameter group settings apply to every RDS for PostgreSQL DB instance in a given AWS Region\. You can't change the defaults because they apply to all instances of a given engine, even those that aren't yours\. To modify any parameter values, you create a custom parameter group and modify its settings\. For example, to set or change logging parameters, you make changes in the custom parameter group associated with your RDS for PostgreSQL DB instance\. To learn how, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

For an RDS for PostgreSQL DB instance, the parameters that affect logging behavior include the following:
+ `rds.log_retention_period` – PostgreSQL logs that are older than the specified number of minutes are deleted\. The default value of 4320 minutes deletes log files after 3 days\. For more information, see [Setting the log retention period](#USER_LogAccess.Concepts.PostgreSQL.log_retention_period)\. 
+ `log_rotation_age` – Specifies number of minutes after which Amazon RDS automatically rotates the logs\. The default is 60 minutes, but you can specify anywhere from 1 to 1440 minutes\. For more information, see [Setting log file rotation](#USER_LogAccess.Concepts.PostgreSQL.log_rotation)\. 
+ `log_rotation_size` – Sets the size, in kilobytes, at which the Amazon RDS should automatically rotate the logs\. There is no value by default because the logs are rotated based on age alone, as specified by the `log_rotation_age` parameter\. For more information, see [Setting log file rotation](#USER_LogAccess.Concepts.PostgreSQL.log_rotation)\.
+ `log_line_prefix` – Specifies the information that gets prefixed in front of each line that gets logged\. The default string for this parameter is `%t:%r:%u@%d:[%p]:`, which notes the time \(%t\) and other distinguishing characteristics such as the database name \(%d\) for the log entry\. You can't change this parameter\. It applies to the `stderr` messages that get logged\. 
+ `log_destination` – Sets the output format for server logs\. The default value for this parameter is standard error \(stderr\), but csvlog \(comma\-separated value log files\) is also supported\. For more information, see [Setting the log destination](#USER_LogAccess.Concepts.PostgreSQL.Log_Format)\. 

### Setting the log retention period<a name="USER_LogAccess.Concepts.PostgreSQL.log_retention_period"></a>

To set the retention period for system logs, use the `rds.log_retention_period` parameter\. You can find `rds.log_retention_period` in the DB parameter group associated with your DB instance \. The unit for this parameter is minutes\. For example, a setting of 1,440 retains logs for one day\. The default value is 4,320 \(three days\)\. The maximum value is 10,080 \(seven days\)\. Your instance needs enough allocated storage to contain the retained log files\.

We recommend that you have your logs routinely published to Amazon CloudWatch Logs, so you can view and analyze system data long after the logs have been removed from your RDS for PostgreSQL DB instance\. For more information, see [Publishing PostgreSQL logs to Amazon CloudWatch Logs](#USER_LogAccess.Concepts.PostgreSQL.PublishtoCloudWatchLogs)\.  

### Setting log file rotation<a name="USER_LogAccess.Concepts.PostgreSQL.log_rotation"></a>

New log files are created by Amazon RDS every hour by default\. The timing is controlled by the `log_rotation_age` parameter\. This parameter has a default value of 60 \(minutes\), but you can set to anywhere from 1 minute to 24 hours \(1,440 minutes\)\. When it's time for rotation, a new distinct log file is created\. The file is named according to the pattern specified by the `log_filename` parameter\. 

Log files can also be rotated according to their size, as specified in the `log_rotation_size` parameter\. This parameter specifies that the log should be rotated when it reaches the size \(in kilobytes\)\. For an RDS for PostgreSQL DB instance, `log_rotation_size` is unset, that is, there is no value specified\. However, the parameter allows setting from 0\-2097151 kB \(kilobytes\)\.  

The log file names are based on the file name pattern of the [https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-FILENAME](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-FILENAME) parameter\. You can specify file names per hour or per minute, as follows:
+ `postgresql.log.%Y-%m-%d-%H%M` – Minute format for log file name\. Sets the granularity of the log to less than an hour\. Supported by PostgreSQL version and higher only\. 
+ `postgresql.log.%Y-%m-%d-%H` – Hour format for log file name\. Sets the granularity of log to hours\.

If you set `log_rotation_age` parameter to less than 60 minutes, be sure to also set the `log_filename` parameter to the minute format\.

For more information, see [https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ROTATION-AGE](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ROTATION-AGE) and [https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ROTATION-SIZE](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ROTATION-SIZE) in the *PostgreSQL documentation*\.

### Setting the log destination<a name="USER_LogAccess.Concepts.PostgreSQL.Log_Format"></a>

By default, Amazon RDS PostgreSQL generates logs in standard error \(stderr\) format\. This is the default setting for the `log_destination` parameter\. This format prefixes each log message with the time, database, and other details specified by the `log_line_prefix` parameter\. The `log_line_prefix` is set to the following text string, which can't be changed:

```
%t:%r:%u@%d:[%p]:t
```

This parameter specifies the following details for each log entry:
+ `%t` – Time of log entry\. 
+  `%r` – Remote host address\. 
+  `%u@%d` – User name @ database name\. 
+  `[%p]` – Process ID if available\. 

For example, the following error message results from querying a column using the wrong name\.

```
2019-03-10 03:54:59 UTC:10.0.0.123(52834):postgres@tstdb:[20175]:ERROR: column "wrong" does not exist at character 8
```

RDS for PostgreSQL can generate the logs in `csvlog` format in addition to the default `stderr` specified by the `log_destination` parameter\. The `csvlog` is useful for analyzing the log data as CSV data\. For example, say that you use the `log_fdw` extension to work with your logs as foreign tables\. The foreign table created on `stderr` log files contains a single column with log event data\. For the CSV formatted log file, the foreign table has multiple columns, so you can sort and analyze your logs much more easily\. To learn how to use the `log_fdw` with `csvlog`, see [Using the log\_fdw extension to access the DB log using SQL](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md#CHAP_PostgreSQL.Extensions.log_fdw)\.

You must be using a custom parameter group so that you change the `log_destination` setting\. The `log_destination` parameter is dynamic, that is, the change takes effect immediately, without rebooting\. 

If you do change this parameter, you need to be aware that `csvlog` files are generated in addition to the `stderr` logs\. We recommend that you pay attention to the storage consumed by the logs, taking into account the `rds.log_retention_period` and other settings that affect log storage and turnover\. Using both `stderr` and `csvlog` more than doubles the storage consumed by the logs\. 

If you do set the `log_destination` to include `csvlog` and you later decide that you want to revert to the default only \(`stderr`\), you can open the custom parameter group for your instance using the AWS Management Console, choose the `log_destination` parameter from the list, choose **Edit parameter** and then choose **Reset**\. This reverts the `log_destination` parameter to its default setting, `stderr`\. 

For more information about configuring logging, see [ Working with Amazon RDS and Aurora PostgreSQL logs: Part 1](http://aws.amazon.com/blogs/database/working-with-rds-and-aurora-postgresql-logs-part-1/)\.

## Enabling query logging<a name="USER_LogAccess.Concepts.PostgreSQL.Query_Logging"></a>

To enable query logging for your PostgreSQL DB instance, set two parameters in the DB parameter group associated with your DB instance: `log_statement` and `log_min_duration_statement`\. 

The `log_statement` parameter controls which SQL statements are logged\. The default value is `none`\. We recommend that when you debug issues in your DB instance, set this parameter to `all` to log all statements\. To log all data definition language \(DDL\) statements \(CREATE, ALTER, DROP, and so on\), set this value to `ddl`\. To log all DDL and data modification language \(DML\) statements \(INSERT, UPDATE, DELETE, and so on\), set the value to `mod`\.

**Warning**  
Sensitive information such as passwords can be exposed if you set the `log_statement` parameter to `ddl`, `mod`, or `all`\. To avoid this risk, set the `log_statement` to `none`\. Also consider the following solutions:  
Encrypt the sensitive information on the client side and use the `ENCRYPTED` and `UNENCRYPTED` options of the `CREATE` and `ALTER` statements\.
Restrict access to the CloudWatch logs\.
Use stronger authentication mechanisms such as IAM\. 
For auditing, you can use the PostgreSQL `pgAudit` extension because it redacts the sensitive information for CREATE and ALTER commands\.

The `log_min_duration_statement` parameter sets the limit in milliseconds of a statement to be logged\. All SQL statements that run longer than the parameter setting are logged\. This parameter is disabled and set to \-1 by default\. Enabling this parameter can help you find unoptimized queries\. 

To set up query logging, take the following steps:

1. Set the `log_statement` parameter to `all`\. The following example shows the information that is written to the `postgresql.log` file\.

   ```
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  received SIGHUP, reloading configuration files
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  parameter "log_statement" changed to "all"
   ```

   Additional information is written to the postgresql\.log file when you run a query\. The following example shows the type of information written to the file after a query\.

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

1. Set the `log_min_duration_statement` parameter\. The following example shows the information that is written to the `postgresql.log` file when the parameter is set to `1`\.

   ```
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  received SIGHUP, reloading configuration files
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  parameter "log_min_duration_statement" changed to "1"
   ```

   Additional information is written to the `postgresql.log` file when you run a query that exceeds the duration parameter setting\. The following example shows the type of information written to the file after a query\.

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

## Publishing PostgreSQL logs to Amazon CloudWatch Logs<a name="USER_LogAccess.Concepts.PostgreSQL.PublishtoCloudWatchLogs"></a>

To store your PostgreSQL log records in highly durable storage, you can use Amazon CloudWatch Logs\. With CloudWatch Logs, you can also perform real\-time analysis of log data and use CloudWatch to view metrics and create alarms\. For example, if you set `log_statements` to `ddl`, you can set up an alarm to alert whenever a DDL statement is executed\.

To work with CloudWatch Logs, configure your RDS for PostgreSQL DB instance to publish log data to a log group\.

**Note**  
Publishing log files to CloudWatch Logs is supported for PostgreSQL version 9\.6\.6 and higher, PostgreSQL 10\.4 and higher, and for all higher releases\.

You can publish the following log types to CloudWatch Logs for RDS for PostgreSQL: 
+ Postgresql log
+ Upgrade log \(not available for Aurora PostgreSQL\)

After you complete the configuration, Amazon RDS publishes the log events to log streams within a CloudWatch log group\. For example, the PostgreSQL log data is stored within the log group `/aws/rds/instance/my_instance/postgresql`\. To view your logs, open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

### Console<a name="USER_LogAccess.Concepts.PostgreSQL.PublishtoCloudWatchLogs.CON"></a>

**To publish PostgreSQL logs to CloudWatch Logs using the console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to modify, and then choose **Modify**\.

1. In the **Log exports** section, choose the logs that you want to start publishing to CloudWatch Logs\.

   The **Log exports** section is available only for PostgreSQL versions that support publishing to CloudWatch Logs\. 

1. Choose **Continue**, and then choose **Modify DB Instance** on the summary page\.

### AWS CLI<a name="USER_LogAccess.Concepts.PostgreSQL.PublishtoCloudWatchLogs.CLI"></a>

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

### RDS API<a name="USER_LogAccess.Concepts.PostgreSQL.PublishtoCloudWatchLogs.API"></a>

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

 