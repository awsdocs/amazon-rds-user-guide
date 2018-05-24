# Publishing Amazon Aurora MySQL Logs to Amazon CloudWatch Logs<a name="AuroraMySQL.Integrating.CloudWatch"></a>

You can configure your Aurora MySQL DB cluster to publish general, slow, audit, and error log data to a log group in Amazon CloudWatch Logs\. With CloudWatch Logs, you can perform real\-time analysis of the log data, and use CloudWatch to create alarms and view metrics\. You can use CloudWatch Logs to store your log records in highly durable storage\.

To publish logs to CloudWatch Logs, the respective logs must be enabled\. Error logs are enabled by default, but you must enable the other types of logs explicitly\. For information about enabling logs in MySQL, see [Selecting General Query and Slow Query Log Output Destinations](https://dev.mysql.com/doc/refman/5.6/en/log-destinations.html) in the MySQL documentation\. For more information about enabling audit logs, see [Enabling Advanced Auditing](AuroraMySQL.Auditing.md#AuroraMySQL.Auditing.Enable)\.

**Note**  
Be aware of the following:  
If exporting log data is disabled, Aurora doesn't delete existing log groups or log streams\. If exporting log data is disabled, existing log data remains available in CloudWatch Logs, depending on log retention, and you still incur charges for stored audit log data\. You can delete log streams and log groups using the CloudWatch Logs console, the AWS CLI, or the CloudWatch Logs API\.
An alternative way to publish audit logs to CloudWatch Logs is by enabling advanced auditing and setting the cluster\-level DB parameter `server_audit_logs_upload` to `1`\. The default for the `server_audit_logs_upload` parameter is `0`\.
If you don't want to export audit logs to CloudWatch Logs, make sure that all methods of exporting audit logs are disabled\. These methods are the AWS Management Console, the AWS CLI, the RDS API, and the `server_audit_logs_upload` parameter\.

## Publishing Aurora MySQL Logs to CloudWatch Logs with the AWS Management Console<a name="AuroraMySQL.Integrating.CloudWatch.Console"></a>

You can publish Aurora MySQL logs to CloudWatch Logs with the console\.

**To publish Aurora MySQL logs from the console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Clusters**\.

1. Choose the Aurora MySQL DB cluster that you want to publish the log data for\.

1. For **Actions**, choose **Modify cluster**\.

1. In the **Log exports** section, choose the logs that you want to start publishing to CloudWatch Logs\.

1. Choose **Continue**, and then choose **Modify DB Cluster** on the summary page\.

## Publishing Aurora MySQL Logs to CloudWatch Logs with the CLI<a name="AuroraMySQL.Integrating.CloudWatch.CLI"></a>

You can publish Aurora MySQL logs with the AWS CLI\. You can run the [modify\-db\-cluster](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command with the following options: 
+ `--db-cluster-identifier`—The DB cluster identifier\.
+ `--cloudwatch-logs-export-configuration`—The configuration setting for the log types to be enabled for export to CloudWatch Logs for the DB cluster\.

You can also publish Aurora MySQL logs by running one of the following AWS CLI commands: 
+ [create\-db\-cluster](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html)
+ [restore\-db\-cluster\-from\-s3](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-s3.html)
+ [restore\-db\-cluster\-from\-snapshot](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-snapshot.html)
+ [restore\-db\-cluster\-to\-point\-in\-time](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html)

Run one of these AWS CLI commands with the following options:
+ `--db-cluster-identifier`—The DB cluster identifier\.
+ `--engine`—The database engine\.
+ `--enable-cloudwatch-logs-exports`—The configuration setting for the log types to be enabled for export to CloudWatch Logs for the DB cluster\.

Other options might be required depending on the AWS CLI command that you run\.

**Example**  
The following command modifies an existing Aurora MySQL DB cluster to publish log files to CloudWatch Logs\.  
For Linux, OS X, or Unix:  

```
1. aws rds modify-db-cluster \
2.     --db-cluster-identifier mydbcluster \
3.     --cloudwatch-logs-export-configuration '{"EnableLogTypes":["error","general","slowquery","audit"]}'
```
For Windows:  

```
1. aws rds modify-db-cluster ^
2.     --db-cluster-identifier mydbcluster ^
3.     --cloudwatch-logs-export-configuration '{"EnableLogTypes":["error","general","slowquery","audit"]}'
```

**Example**  
The following command creates an Aurora MySQL DB cluster to publish log files to CloudWatch Logs\.  
For Linux, OS X, or Unix:  

```
1. aws rds create-db-cluster \
2.     --db-cluster-identifier mydbcluster \
3.     --engine aurora \
4.     --enable-cloudwatch-logs-exports '["error","general","slowquery","audit"]'
```
For Windows:  

```
1. aws rds create-db-cluster ^
2.     --db-cluster-identifier mydbcluster ^
3.     --engine aurora ^
4.     --enable-cloudwatch-logs-exports '["error","general","slowquery","audit"]'
```

## Publishing Aurora MySQL Logs to CloudWatch Logs with the RDS API<a name="AuroraMySQL.Integrating.CloudWatch.API"></a>

You can publish Aurora MySQL logs with the RDS API\. You can run the [ModifyDBCluster](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) action with the following options: 
+ `DBClusterIdentifier`—The DB cluster identifier\.
+ `CloudwatchLogsExportConfiguration`—The configuration setting for the log types to be enabled for export to CloudWatch Logs for the DB cluster\.

You can also publish Aurora MySQL logs with the RDS API by running one of the following RDS API actions: 
+ [CreateDBCluster](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html)
+ [RestoreDBClusterFromS3](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromS3.html)
+ [RestoreDBClusterFromSnapshot](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromSnapshot.html)
+ [RestoreDBClusterToPointInTime](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html)

Run the RDS API action with the following parameters: 
+ `DBClusterIdentifier`—The DB cluster identifier\.
+ `Engine`—The database engine\.
+ `EnableCloudwatchLogsExports`—The configuration setting for the log types to be enabled for export to CloudWatch Logs for the DB cluster\.

Other parameters might be required depending on the AWS CLI command that you run\.

## Monitoring Log Events in Amazon CloudWatch<a name="AuroraMySQL.Integrating.CloudWatch.Monitor"></a>

After enabling Aurora MySQL log events, you can monitor the events in Amazon CloudWatch Logs\. A new log group is automatically created for the Aurora DB cluster under the following prefix, in which `cluster-name` represents the DB cluster name, and `log_type` represents the log type\.

```
/aws/rds/cluster/cluster-name/log_type
```

For example, if you configure the export function to include the slow query log for a DB cluster named `mydbcluster`, slow query data is stored in the `/aws/rds/cluster/mydbcluster/slowquery` log group\.

All of the events from all of the DB instances in a DB cluster are pushed to a log group using different log streams\.

If a log group with the specified name exists, Aurora uses that log group to export log data for the Aurora DB cluster\. You can use automated configuration, such as AWS CloudFormation, to create log groups with predefined log retention periods, metric filters, and customer access\. Otherwise, a new log group is automatically created using the default log retention period, **Never Expire**, in CloudWatch Logs\. You can use the CloudWatch Logs console, the AWS CLI, or the CloudWatch Logs API to change the log retention period\. For more information about changing log retention periods in CloudWatch Logs, see [Change Log Data Retention in CloudWatch Logs](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/SettingLogRetention.html)\.

You can use the CloudWatch Logs console, the AWS CLI, or the CloudWatch Logs API to search for information within the log events for a DB cluster\. For more information about searching and filtering log data, see [Searching and Filtering Log Data](http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html)\.