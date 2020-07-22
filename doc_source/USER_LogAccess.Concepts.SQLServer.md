# Microsoft SQL Server Database Log Files<a name="USER_LogAccess.Concepts.SQLServer"></a>

You can access Microsoft SQL Server error logs, agent logs, trace files, and dump files by using the Amazon RDS console, AWS CLI, or RDS API\. For more information about viewing, downloading, and watching file\-based database logs, see [Amazon RDS Database Log Files](USER_LogAccess.md)\. 

## Retention Schedule<a name="USER_LogAccess.Concepts.SQLServer.Retention"></a>

Log files are rotated each day and whenever your DB instance is restarted\. The following is the retention schedule for Microsoft SQL Server logs on Amazon RDS\. 


****  

| Log Type | Retention Schedule | 
| --- | --- | 
|  Error logs  |  A maximum of 30 error logs are retained\. Amazon RDS may delete error logs older than 7 days\.    | 
|  Agent logs  |  A maximum of 10 agent logs are retained\. Amazon RDS may delete agent logs older than 7 days\.    | 
|  Trace files  |  Trace files are retained according to the trace file retention period of your DB instance\. The default trace file retention period is 7 days\. To modify the trace file retention period for your DB instance, see [Setting the Retention Period for Trace and Dump Files](Appendix.SQLServer.CommonDBATasks.TraceFiles.md#Appendix.SQLServer.CommonDBATasks.TraceFiles.PurgeTraceFiles)\.   | 
|  Dump files  |  Dump files are retained according to the dump file retention period of your DB instance\. The default dump file retention period is 7 days\. To modify the dump file retention period for your DB instance, see [Setting the Retention Period for Trace and Dump Files](Appendix.SQLServer.CommonDBATasks.TraceFiles.md#Appendix.SQLServer.CommonDBATasks.TraceFiles.PurgeTraceFiles)\.   | 

## Viewing the SQL Server Error Log by Using the rds\_read\_error\_log Procedure<a name="USER_LogAccess.Concepts.SQLServer.Proc"></a>

You can use the Amazon RDS stored procedure `rds_read_error_log` to view error logs and agent logs\. For more information, see [Viewing Error and Agent Logs](Appendix.SQLServer.CommonDBATasks.Logs.md#Appendix.SQLServer.CommonDBATasks.Logs.SP)\. 

## Publishing SQL Server Logs to Amazon CloudWatch Logs<a name="USER_LogAccess.SQLServer.PublishtoCloudWatchLogs"></a>

With Amazon RDS for SQL Server, you can publish error and agent log events directly to Amazon CloudWatch Logs\. Analyze the log data with CloudWatch Logs, then use CloudWatch to create alarms and view metrics\.

With CloudWatch Logs, you can do the following:
+ Store logs in highly durable storage space with a retention period that you define\.
+ Search and filter log data\.
+ Share log data between accounts\.
+ Export logs to Amazon S3\.
+ Stream data to Amazon Elasticsearch Service\.
+ Process log data in real time with Amazon Kinesis Data Streams\.

 Amazon RDS publishes each SQL Server database log as a separate database stream in the log group\. For example, if you publish error logs, error data is stored in an error log stream in the `/aws/rds/instance/my_instance/error` log group\. 

**Note**  
Publishing SQL Server logs to CloudWatch Logs isn't enabled by default\. Publishing trace and dump files isn't supported\. Publishing SQL Server logs to CloudWatch Logs is supported in all regions, except for Asia Pacific \(Hong Kong\)\.

### Console<a name="USER_LogAccess.SQLServer.PublishtoCloudWatchLogs.console"></a>

**To publish SQL Server DB logs to CloudWatch Logs from the AWS Management Console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to modify\.

1. Choose **Modify**\.

1. In the **Log exports** section, choose the logs that you want to start publishing to CloudWatch Logs\.

   You can choose **error**, **agent**, or both\.

1. Choose **Continue**, and then choose **Modify DB Instance** on the summary page\.

### AWS CLI<a name="USER_LogAccess.SQLServer.PublishtoCloudWatchLogs.CLI"></a>

To publish SQL Server logs, you can use the [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the following parameters: 
+ `--db-instance-identifier`
+ `--cloudwatch-logs-export-configuration`

**Note**  
A change to the `--cloudwatch-logs-export-configuration` option is always applied to the DB instance immediately\. Therefore, the `--apply-immediately` and `--no-apply-immediately` options have no effect\.

You can also publish SQL Server logs using the following commands: 
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html)
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html)

**Example**  
The following example creates an SQL Server DB instance with CloudWatch Logs publishing enabled\. The `--enable-cloudwatch-logs-exports` value is a JSON array of strings that can include `error`, `agent`, or both\.  
For Linux, macOS, or Unix:  

```
aws rds create-db-instance \
    --db-instance-identifier mydbinstance \
    --enable-cloudwatch-logs-exports '["error","agent"]' \
    --db-instance-class db.m4.large \
    --engine sqlserver-se
```
For Windows:  

```
aws rds create-db-instance ^
    --db-instance-identifier mydbinstance ^
    --enable-cloudwatch-logs-exports "[\"error\",\"agent\"]" ^
    --db-instance-class db.m4.large ^
    --engine sqlserver-se
```
When using the Windows command prompt, you must escape double quotes \("\) in JSON code by prefixing them with a backslash \(\\\)\.

**Example**  
The following example modifies an existing SQL Server DB instance to publish log files to CloudWatch Logs\. The `--cloudwatch-logs-export-configuration` value is a JSON object\. The key for this object is `EnableLogTypes`, and its value is an array of strings that can include `error`, `agent`, or both\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --cloudwatch-logs-export-configuration '{"EnableLogTypes":["error","agent"]}'
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier mydbinstance ^
    --cloudwatch-logs-export-configuration "{\"EnableLogTypes\":[\"error\",\"agent\"]}"
```
When using the Windows command prompt, you must escape double quotes \("\) in JSON code by prefixing them with a backslash \(\\\)\.

**Example**  
The following example modifies an existing SQL Server DB instance to disable publishing agent log files to CloudWatch Logs\. The `--cloudwatch-logs-export-configuration` value is a JSON object\. The key for this object is `DisableLogTypes`, and its value is an array of strings that can include `error`, `agent`, or both\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --cloudwatch-logs-export-configuration '{"DisableLogTypes":["agent"]}'
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier mydbinstance ^
    --cloudwatch-logs-export-configuration "{\"DisableLogTypes\":[\"agent\"]}"
```
When using the Windows command prompt, you must escape double quotes \("\) in JSON code by prefixing them with a backslash \(\\\)\.