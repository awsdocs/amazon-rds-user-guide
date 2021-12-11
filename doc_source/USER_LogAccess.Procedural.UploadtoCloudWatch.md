# Publishing database logs to Amazon CloudWatch Logs<a name="USER_LogAccess.Procedural.UploadtoCloudWatch"></a>

In addition to viewing and downloading DB instance logs, you can publish logs to Amazon CloudWatch Logs\. With CloudWatch Logs, you can perform real\-time analysis of the log data, store the data in highly durable storage, and manage the data with the CloudWatch Logs Agent\. AWS retains log data published to CloudWatch Logs for an indefinite time period unless you specify a retention period\. For more information, see [Change log data retention in CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html#SettingLogRetention)\. 

**Topics**
+ [Configuring CloudWatch log integration](#integrating_cloudwatchlogs.configure)
+ [Engine\-specific log information](#engine-specific-logs)

## Configuring CloudWatch log integration<a name="integrating_cloudwatchlogs.configure"></a>

Before you enable log data publishing, make sure that you have a service\-linked role in AWS Identity and Access Management \(IAM\)\. For more information about service\-linked roles, see [Using service\-linked roles for Amazon RDS](UsingWithRDS.IAM.ServiceLinkedRoles.md)\.

To publish your database log files to CloudWatch Logs, choose which logs to publish\. Make this choice in the **Advanced Settings** section when you create a new DB instance\. You can also modify an existing DB instance to begin publishing\. 

![\[Add CloudWatch Logs\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AddCWLogs.png)

 After you have enabled publishing, Amazon RDS continuously streams all of the DB instance log records to a log group\. For example, you have a log group `/aws/rds/instance/instance_name/log_type` for each type of log that you publish\. This log group is in the same AWS Region as the database instance that generates the log\.

After you have published log records, you can use CloudWatch Logs to search and filter the records\. For more information about searching and filtering logs, see [Searching and filtering log data](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html)\. For a tutorial explaining how to monitor RDS logs, see [Build proactive database monitoring for Amazon RDS with Amazon CloudWatch Logs, AWS Lambda, and Amazon SNS](http://aws.amazon.com/blogs/database/build-proactive-database-monitoring-for-amazon-rds-with-amazon-cloudwatch-logs-aws-lambda-and-amazon-sns/)\.

## Engine\-specific log information<a name="engine-specific-logs"></a>

 For engine\-specific information, see the following:
+ [Publishing MariaDB logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.MariaDB.md#USER_LogAccess.MariaDB.PublishtoCloudWatchLogs)
+ [Publishing MySQL logs to Amazon CloudWatch Logs](USER_LogAccess.MySQLDB.PublishtoCloudWatchLogs.md)
+ [Publishing Oracle logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.Oracle.md#USER_LogAccess.Oracle.PublishtoCloudWatchLogs)
+ [Publishing PostgreSQL logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.PostgreSQL.md#USER_LogAccess.Concepts.PostgreSQL.PublishtoCloudWatchLogs)
+ [Publishing SQL Server logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.SQLServer.md#USER_LogAccess.SQLServer.PublishtoCloudWatchLogs)