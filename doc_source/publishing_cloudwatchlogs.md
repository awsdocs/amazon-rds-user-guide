# Publishing database engine logs to Amazon CloudWatch Logs<a name="publishing_cloudwatchlogs"></a>

You can configure your Amazon RDS database engine to publish log data to a log group in Amazon CloudWatch Logs\. With CloudWatch Logs, you can perform real\-time analysis of the log data, and use CloudWatch to create alarms and view metrics\. You can use CloudWatch Logs to store your log records in highly durable storage, which you can manage with the CloudWatch Logs Agent\. For example, you can determine when to rotate log records from a host to the log service, so you can access the raw logs when you need to\. 

For engine\-specific information, see the following topics:
+ [Publishing MariaDB logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.MariaDB.md#USER_LogAccess.MariaDB.PublishtoCloudWatchLogs)
+ [Publishing MySQL logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.MySQL.md#USER_LogAccess.MySQLDB.PublishtoCloudWatchLogs)
+ [Publishing Oracle logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.Oracle.md#USER_LogAccess.Oracle.PublishtoCloudWatchLogs)
+ [Publishing PostgreSQL logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.PostgreSQL.md#USER_LogAccess.Concepts.PostgreSQL.PublishtoCloudWatchLogs)
+ [Publishing SQL Server logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.SQLServer.md#USER_LogAccess.SQLServer.PublishtoCloudWatchLogs)

**Note**  
Before you enable log data publishing, make sure that you have a service\-linked role in AWS Identity and Access Management \(IAM\)\. For more information about service\-linked roles, see [Using service\-linked roles for Amazon RDS](UsingWithRDS.IAM.ServiceLinkedRoles.md)\.

## Configuring CloudWatch log integration<a name="integrating_cloudwatchlogs.configure"></a>

To publish your database log files to CloudWatch Logs, choose which logs to publish\. Make this choice in the **Advanced Settings** section when you create a new DB instance\. You can also modify an existing DB instance to begin publishing\. 

![\[Add CloudWatch Logs\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AddCWLogs.png)

 After you have enabled publishing, Amazon RDS continuously streams all of the DB instance log records to a log group\. For example, you have a log group `/aws/rds/instance/log type` for each type of log that you publish\. This log group is in the same AWS Region as the database instance that generates the log\.

After you have published log records, you can use CloudWatch Logs to search and filter the records\. For more information about searching and filtering logs, see [Searching and filtering log data](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html)\. For a tutorial explaining how to monitor RDS logs, see [Build proactive database monitoring for Amazon RDS with Amazon CloudWatch Logs, AWS Lambda, and Amazon SNS](https://aws.amazon.com/blogs/database/build-proactive-database-monitoring-for-amazon-rds-with-amazon-cloudwatch-logs-aws-lambda-and-amazon-sns/)\.

## Viewing DB instance metrics<a name="USER_Monitoring"></a>

Amazon RDS provides metrics so that you can monitor the health of your DB instances\. You can monitor both DB instance metrics and operating system \(OS\) metrics\.

Following, you can find details about how to view metrics for your DB instance using the RDS console and CloudWatch\. For information on monitoring metrics for your DB instance's operating system in real time using CloudWatch Logs, see [Using Enhanced Monitoring](USER_Monitoring.OS.md)\.

### Viewing metrics by using the console<a name="USER_Monitoring.CON"></a>

**To view DB and OS metrics for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the name of the DB instance that you need information about to show its details\.

1. Choose the **Monitoring** tab\.

1. For **Monitoring**, choose the option for how you want to view your metrics from these:
   + **CloudWatch** – Shows a summary of DB instance metrics available from Amazon CloudWatch\. Each metric includes a graph showing the metric monitored over a specific time span\.
   + **Enhanced monitoring** – Shows a summary of OS metrics available for a DB instance with Enhanced Monitoring enabled\. Each metric includes a graph showing the metric monitored over a specific time span\.
   + **OS Process list** – Shows details for each process running in the selected instance\.
   + **Performance Insights** – Opens the Amazon RDS Performance Insights console for your DB instance\.

     
![\[RDS metrics viewing options\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/metrics0.png)
**Tip**  
To choose the time range of the metrics represented by the graphs, you can use the time range list\.  
To bring up a more detailed view, you can choose any graph\. You can also apply metric\-specific filters to the data\. 

### Viewing DB instance metrics with the CLI or API<a name="USER_Monitoring.DB"></a>

Amazon RDS integrates with CloudWatch metrics to provide a variety of DB instance metrics\. You can view CloudWatch metrics using the RDS console, AWS CLI, or API\.

 For a complete list of Amazon RDS metrics, go to [ Amazon RDS dimensions and metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/rds-metricscollected.html) in the *Amazon CloudWatch User Guide*\. 

#### Viewing DB metrics by using the CloudWatch CLI<a name="USER_Monitoring.CLI"></a>

**Note**  
The following CLI example requires the CloudWatch command line tools\. For more information on CloudWatch and to download the developer tools, see [Amazon CloudWatch](https://aws.amazon.com/cloudwatch) on the AWS website\. The `StartTime` and `EndTime` values supplied in this example are for illustration only\. Substitute appropriate start and end time values for your DB instance\.

**To view usage and performance statistics for a DB instance**
+ Use the CloudWatch command `mon-get-stats` with the following parameters\.

  ```
  1. PROMPT>mon-get-stats FreeStorageSpace --dimensions="DBInstanceIdentifier=mydbinstance" --statistics= Average 
  2.   --namespace="AWS/RDS" --start-time 2009-10-16T00:00:00 --end-time 2009-10-16T00:02:00
  ```

#### Viewing DB metrics by using the CloudWatch API<a name="USER_Monitoring.API"></a>

The `StartTime` and `EndTime` values supplied in this example are for illustration only\. Substitute appropriate start and end time values for your DB instance\.

**To view usage and performance statistics for a DB instance**
+ Call the CloudWatch API `GetMetricStatistics` with the following parameters:
  + `Statistics.member.1` = `Average`
  + `Namespace` = `AWS/RDS`
  + `StartTime` = `2009-10-16T00:00:00`
  + `EndTime` = `2009-10-16T00:02:00`
  + `Period` = `60`
  + `MeasureName` = `FreeStorageSpace`