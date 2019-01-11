# PostgreSQL Database Log Files<a name="USER_LogAccess.Concepts.PostgreSQL"></a>

RDS PostgreSQL generates query and error logs\. We write auto\-vacuum information and rds\_admin actions to the error log\. PostgreSQL also logs connections, disconnections, and checkpoints to the error log\. For more information, see the [Error Reporting and Logging](https://www.postgresql.org/docs/current/runtime-config-logging.html) in the PostgreSQL documentation\.

You can set the retention period for system logs using the `rds.log_retention_period` parameter in the DB parameter group associated with your DB instance\. The unit for this parameter is minutes\. For example, a setting of 1440 would retain logs for one day\. The default value is 4320 \(three days\)\. The maximum value is 10080 \(seven days\)\. Note that your instance must have enough allocated storage to contain the retained log files\. 

You can enable query logging for your PostgreSQL DB instance by setting two parameters in the DB parameter group associated with your DB instance: `log_statement` and `log_min_duration_statement`\. The `log_statement` parameter controls which SQL statements are logged\. We recommend setting this parameter to *all* to log all statements when debugging issues in your DB instance\. The default value is *none*\. Alternatively, you can set this value to *ddl* to log all data definition language \(DDL\) statements \(CREATE, ALTER, DROP, and so on\) or to *mod* to log all DDL and data modification language \(DML\) statements \(INSERT, UPDATE, DELETE, and so on\)\.

The `log_min_duration_statement` parameter sets the limit in milliseconds of a statement to be logged\. All SQL statements that run longer than the parameter setting are logged\. This parameter is disabled and set to minus 1 \(\-1\) by default\. Enabling this parameter can help you find unoptimized queries\. 

If you are new to setting parameters in a DB parameter group and associating that parameter group with a DB instance, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)

The following steps show how to set up query logging:

1. Set the `log_statement` parameter to *all*\. The following example shows the information that is written to the postgres\.log file:

   ```
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  received SIGHUP, reloading configuration files
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  parameter "log_min_duration_statement" changed to "1"
   ```

   Additional information is written to the postgres\.log file when you execute a query\. The following example shows the type of information written to the file after a query:

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

1. Set the `log_min_duration_statement` parameter\. The following example shows the information that is written to the postgres\.log file when the parameter is set to *1*:

   ```
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  received SIGHUP, reloading configuration files
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  parameter "log_min_duration_statement" changed to "1"
   ```

   Additional information is written to the postgres\.log file when you execute a query that exceeds the duration parameter setting\. The following example shows the type of information written to the file after a query:

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

You can configure your Amazon RDS for PostgreSQL DB instance to publish log data to a log group in Amazon CloudWatch Logs\. With CloudWatch Logs, you can perform real\-time analysis of the log data, and use CloudWatch to create alarms and view metrics\. You can use CloudWatch Logs to store your log records in highly durable storage\. 

**Note**  
Publishing log files to CloudWatch Logs is only supported for PostgreSQL versions 9\.6\.6 and above and 10\.4 and above\.

Following are the log types that can be published to CloudWatch Logs for Amazon RDS for PostgreSQL\. 
+ Postgresql log
+ Upgrade log

After you complete the configuration, Amazon RDS publishes the log events to log streams within a CloudWatch log group\. For example, the PostgreSQL log data is stored within the log group `/aws/rds/instance/my_instance/postgresql`\. To view your Amazon CloudWatch Logs, open [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

### AWS Management Console<a name="USER_LogAccess.PostgreSQL.PublishtoCloudWatchLogs.CON"></a>

**To publish PostgreSQL logs to CloudWatch Logs using the console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Select the DB instance that you want to modify, and then choose **Modify**\.

1. In the **Log exports** section, choose the logs you want to start publishing to CloudWatch Logs\.

1. Choose **Continue**, and then choose **Modify DB Instance** on the summary page\.

### AWS CLI<a name="USER_LogAccess.PostgreSQL.PublishtoCloudWatchLogs.CLI"></a>

You can publish PostgreSQL logs with the AWS CLI\. You can call the [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the following parameters: 
+ `--db-instance-identifier`
+ `--cloudwatch-logs-export-configuration`

**Note**  
A change to the `--cloudwatch-logs-export-configuration` option is always applied to the DB instance immediately\. Therefore, the `--apply-immediately` and `--no-apply-immediately` options have no effect\.

You can also publish PostgreSQL logs by calling the following AWS CLI commands: 
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html)
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html)

Run one of these AWS CLI commands with the following options: 
+ `--db-instance-identifier`
+ `--enable-cloudwatch-logs-exports`
+ `--db-instance-class`
+ `--engine`

Other options might be required depending on the AWS CLI command you run\.

**Modify an instance to publish logs to CloudWatch Logs**

**Example**  
The following example modifies an existing PostgreSQL DB instance to publish log files to CloudWatch Logs\. The `--cloudwatch-logs-export-configuration` value is a JSON object\. The key for this object is `EnableLogTypes`, and its value is an array of strings with any combination of `postgresql` and `upgrade`\.  
For Linux, OS X, or Unix:  

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

**Example**  
**Create an instance to publish logs to CloudWatch Logs**  
The following example creates a PostgreSQL DB instance and publishes log files to CloudWatch Logs\. The `--enable-cloudwatch-logs-exports` value is a JSON array of strings\. The strings can be any combination of `postgresql` and `upgrade`\.  
For Linux, OS X, or Unix:  

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

You can also publish PostgreSQL logs by calling the following RDS API actions: 
+ [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html)
+ [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html)
+ [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html)

Run one of these RDS API actions with the following parameters: 
+ `DBInstanceIdentifier`
+ `EnableCloudwatchLogsExports`
+ `Engine`
+ `DBInstanceClass`

Other parameters might be required depending on the action you run\.