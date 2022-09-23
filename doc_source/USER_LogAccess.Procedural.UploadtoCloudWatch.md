# Publishing database logs to Amazon CloudWatch Logs<a name="USER_LogAccess.Procedural.UploadtoCloudWatch"></a>

In an on\-premises database, the database logs reside on the file system\. Amazon RDS doesn't provide host access to the database logs on the file system of your DB instance\. For this reason, Amazon RDS lets you export database logs to [Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html)\. With CloudWatch Logs, you can perform real\-time analysis of the log data\. You can also store the data in highly durable storage and manage the data with the CloudWatch Logs Agent\. 

**Topics**
+ [Overview of RDS integration with CloudWatch Logs](#rds-integration-cw-logs)
+ [Deciding which logs to publish to CloudWatch Logs](#engine-specific-logs)
+ [Specifying the logs to publish to CloudWatch Logs](#integrating_cloudwatchlogs.configure)
+ [Searching and filtering your logs in CloudWatch Logs](#accessing-logs-in-cloudwatch)

## Overview of RDS integration with CloudWatch Logs<a name="rds-integration-cw-logs"></a>

In CloudWatch Logs, a *log stream* is a sequence of log events that share the same source\. Each separate source of logs in CloudWatch Logs makes up a separate log stream\. A *log group* is a group of log streams that share the same retention, monitoring, and access control settings\.

Amazon RDS continuously streams your DB instance log records to a log group\. For example, you have a log group `/aws/rds/instance/instance_name/log_type` for each type of log that you publish\. This log group is in the same AWS Region as the database instance that generates the log\.

AWS retains log data published to CloudWatch Logs for an indefinite time period unless you specify a retention period\. For more information, see [Change log data retention in CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html#SettingLogRetention)\. 

## Deciding which logs to publish to CloudWatch Logs<a name="engine-specific-logs"></a>

Each RDS database engine supports its own set of logs\. To learn about the options for your database engine, review the following topics:
+ [Publishing MariaDB logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.MariaDB.md#USER_LogAccess.MariaDB.PublishtoCloudWatchLogs)
+ [Publishing MySQL logs to Amazon CloudWatch Logs](USER_LogAccess.MySQLDB.PublishtoCloudWatchLogs.md)
+ [Publishing Oracle logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.Oracle.md#USER_LogAccess.Oracle.PublishtoCloudWatchLogs)
+ [Publishing PostgreSQL logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.PostgreSQL.md#USER_LogAccess.Concepts.PostgreSQL.PublishtoCloudWatchLogs)
+ [Publishing SQL Server logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.SQLServer.md#USER_LogAccess.SQLServer.PublishtoCloudWatchLogs)

## Specifying the logs to publish to CloudWatch Logs<a name="integrating_cloudwatchlogs.configure"></a>

You specify which logs to publish in the console\. Make sure that you have a service\-linked role in AWS Identity and Access Management \(IAM\)\. For more information about service\-linked roles, see [Using service\-linked roles for Amazon RDS](UsingWithRDS.IAM.ServiceLinkedRoles.md)\.

**To specify the logs to publish**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Do either of the following:
   + Choose **Create database**\.
   + Choose a database from the list, and then choose **Modify**\.

1. In **Logs exports**, choose which logs to publish\.

   The following example specifies the audit log, error logs, general log, and slow query log\.  
![\[Choose the logs to publish to CloudWatch Logs\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AddCWLogs.png)

## Searching and filtering your logs in CloudWatch Logs<a name="accessing-logs-in-cloudwatch"></a>

You can search for log entries that meet a specified criteria using the CloudWatch Logs console\. You can access the logs either through the RDS console, which leads you to the CloudWatch Logs console, or from the CloudWatch Logs console directly\.

**To search your RDS logs using the RDS console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose a DB instance\.

1. Choose **Configuration**\.

1. Under **Published logs**, choose the database log that you want to view\.

**To search your RDS logs using the CloudWatch Logs console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Log groups**\.

1. In the filter box, enter **/aws/rds**\.

1. For **Log Groups**, choose the name of the log group containing the log stream to search\.

1. For **Log Streams**, choose the name of the log stream to search\.

1. Under **Log events**, enter the filter syntax to use\.

For more information, see [Searching and filtering log data](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html) in the *Amazon CloudWatch Logs User Guide*\. For a blog tutorial explaining how to monitor RDS logs, see [Build proactive database monitoring for Amazon RDS with Amazon CloudWatch Logs, AWS Lambda, and Amazon SNS](http://aws.amazon.com/blogs/database/build-proactive-database-monitoring-for-amazon-rds-with-amazon-cloudwatch-logs-aws-lambda-and-amazon-sns/)\.