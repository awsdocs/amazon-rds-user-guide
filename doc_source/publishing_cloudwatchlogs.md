# Publishing database engine logs to Amazon CloudWatch Logs<a name="publishing_cloudwatchlogs"></a>

You can configure your Amazon RDS database engine to publish log data to a log group in Amazon CloudWatch Logs\. With CloudWatch Logs, you can perform real\-time analysis of the log data, and use CloudWatch to create alarms and view metrics\. You can use CloudWatch Logs to store your log records in highly durable storage, which you can manage with the CloudWatch Logs Agent\. For example, you can determine when to rotate log records from a host to the log service, so you can access the raw logs when you need to\. 

For engine\-specific information, see the following topics:
+ [Publishing MariaDB logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.MariaDB.md#USER_LogAccess.MariaDB.PublishtoCloudWatchLogs)
+ [Publishing MySQL logs to CloudWatch Logs](USER_LogAccess.Concepts.MySQL.md#USER_LogAccess.MySQLDB.PublishtoCloudWatchLogs)
+ [Publishing Oracle logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.Oracle.md#USER_LogAccess.Oracle.PublishtoCloudWatchLogs)
+ 