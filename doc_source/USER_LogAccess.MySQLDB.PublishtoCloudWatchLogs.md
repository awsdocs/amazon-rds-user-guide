# Publishing MySQL logs to Amazon CloudWatch Logs<a name="USER_LogAccess.MySQLDB.PublishtoCloudWatchLogs"></a>

You can configure your MySQL DB instance to publish log data to a log group in Amazon CloudWatch Logs\. With CloudWatch Logs, you can perform real\-time analysis of the log data, and use CloudWatch to create alarms and view metrics\. You can use CloudWatch Logs to store your log records in highly durable storage\. 

Amazon RDS publishes each MySQL database log as a separate database stream in the log group\. For example, if you configure the export function to include the slow query log, slow query data is stored in a slow query log stream in the `/aws/rds/instance/my_instance/slowquery` log group\. 

The error log is enabled by default\. The following table summarizes the requirements for the other MySQL logs\.


| Log | Requirement | 
| --- | --- | 
|  Audit log  |  The DB instance must use a custom option group with the `MARIADB_AUDIT_PLUGIN` option\.  | 
|  General log  |  The DB instance must use a custom parameter group with the parameter setting `general_log = 1` to enable the general log\.  | 
|  Slow query log  |  The DB instance must use a custom parameter group with the parameter setting `slow_query_log = 1` to enable the slow query log\.  | 
|  Log output  |  The DB instance must use a custom parameter group with the parameter setting `log_output = FILE` to write logs to the file system and publish them to CloudWatch Logs\.  | 

## Console<a name="USER_LogAccess.MySQL.PublishtoCloudWatchLogs.CON"></a>

**To publish MySQL logs to CloudWatch Logs using the console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to modify\.

1. Choose **Modify**\.

1. In the **Log exports** section, choose the logs that you want to start publishing to CloudWatch Logs\.

1. Choose **Continue**, and then choose **Modify DB Instance** on the summary page\.

## AWS CLI<a name="USER_LogAccess.MySQL.PublishtoCloudWatchLogs.CLI"></a>

 You can publish MySQL logs with the AWS CLI\. You can call the [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the following parameters: 
+ `--db-instance-identifier`
+ `--cloudwatch-logs-export-configuration`

**Note**  
A change to the `--cloudwatch-logs-export-configuration` option is always applied to the DB instance immediately\. Therefore, the `--apply-immediately` and `--no-apply-immediately` options have no effect\.

You can also publish MySQL logs by calling the following AWS CLI commands: 
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html)
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-s3.html](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-s3.html)
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html)

Run one of these AWS CLI commands with the following options: 
+ `--db-instance-identifier`
+ `--enable-cloudwatch-logs-exports`
+ `--db-instance-class`
+ `--engine`

Other options might be required depending on the AWS CLI command you run\.

**Example**  
The following example modifies an existing MySQL DB instance to publish log files to CloudWatch Logs\. The `--cloudwatch-logs-export-configuration` value is a JSON object\. The key for this object is `EnableLogTypes`, and its value is an array of strings with any combination of `audit`, `error`, `general`, and `slowquery`\.  
For Linux, macOS, or Unix:  

```
1. aws rds modify-db-instance \
2.     --db-instance-identifier mydbinstance \
3.     --cloudwatch-logs-export-configuration '{"EnableLogTypes":["audit","error","general","slowquery"]}'
```
For Windows:  

```
1. aws rds modify-db-instance ^
2.     --db-instance-identifier mydbinstance ^
3.     --cloudwatch-logs-export-configuration '{"EnableLogTypes":["audit","error","general","slowquery"]}'
```

**Example**  
The following example creates a MySQL DB instance and publishes log files to CloudWatch Logs\. The `--enable-cloudwatch-logs-exports` value is a JSON array of strings\. The strings can be any combination of `audit`, `error`, `general`, and `slowquery`\.  
For Linux, macOS, or Unix:  

```
1. aws rds create-db-instance \
2.     --db-instance-identifier mydbinstance \
3.     --enable-cloudwatch-logs-exports '["audit","error","general","slowquery"]' \
4.     --db-instance-class db.m4.large \
5.     --engine MySQL
```
For Windows:  

```
1. aws rds create-db-instance ^
2.     --db-instance-identifier mydbinstance ^
3.     --enable-cloudwatch-logs-exports '["audit","error","general","slowquery"]' ^
4.     --db-instance-class db.m4.large ^
5.     --engine MySQL
```

## RDS API<a name="USER_LogAccess.MySQL.PublishtoCloudWatchLogs.API"></a>

You can publish MySQL logs with the RDS API\. You can call the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) action with the following parameters: 
+ `DBInstanceIdentifier`
+ `CloudwatchLogsExportConfiguration`

**Note**  
A change to the `CloudwatchLogsExportConfiguration` parameter is always applied to the DB instance immediately\. Therefore, the `ApplyImmediately` parameter has no effect\.

You can also publish MySQL logs by calling the following RDS API operations: 
+ [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html)
+ [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html)
+ [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromS3.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromS3.html)
+ [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html)

Run one of these RDS API operations with the following parameters: 
+ `DBInstanceIdentifier`
+ `EnableCloudwatchLogsExports`
+ `Engine`
+ `DBInstanceClass`

Other parameters might be required depending on the AWS CLI command you run\.