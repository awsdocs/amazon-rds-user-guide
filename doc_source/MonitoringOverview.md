# Overview of Monitoring Amazon RDS<a name="MonitoringOverview"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of Amazon RDS and your AWS solutions\. You should collect monitoring data from all of the parts of your AWS solution so that you can more easily debug a multi\-point failure if one occurs\. Before you start monitoring Amazon RDS, we recommend that you create a monitoring plan that includes answers to the following questions:
+ What are your monitoring goals?
+ What resources will you monitor?
+ How often will you monitor these resources?
+ What monitoring tools will you use?
+ Who will perform the monitoring tasks?
+ Who should be notified when something goes wrong?

The next step is to establish a baseline for normal Amazon RDS performance in your environment\. To do this, you measure performance at various times and under different load conditions\. As you monitor Amazon RDS, consider storing historical monitoring data\. This stored data gives you a baseline to compare against with current performance data, identify normal performance patterns and performance anomalies, and devise methods to address issues\.

For example, with Amazon RDS, you can monitor network throughput, client connections, I/O for read, write, or metadata operations, and burst credit balances for your DB instances\. When performance falls outside your established baseline, you might need to make changes to optimize your database availability for your workload\. For example, you might need to change the instance class of your DB instance\. Or you might need to change the number of DB instances and read replicas that are available for clients\. 

In general, acceptable values for performance metrics depend on what your baseline looks like and what your application is doing\. Investigate consistent or trending variances from your baseline\. Advice about specific types of metrics follows: 
+  **High CPU or RAM consumption** – High values for CPU or RAM consumption might be appropriate, if they're in keeping with your goals for your application \(like throughput or concurrency\) and are expected\. 
+  **Disk space consumption** – Investigate disk space consumption if space used is consistently at or above 85 percent of the total disk space\. See if it is possible to delete data from the instance or archive data to a different system to free up space\. 
+  **Network traffic** – For network traffic, talk with your system administrator to understand what expected throughput is for your domain network and internet connection\. Investigate network traffic if throughput is consistently lower than expected\. 
+  **Database connections** – If you see high numbers of user connections and also decreases in instance performance and response time, consider constraining database connections\. The best number of user connections for your DB instance varies based on your instance class and the complexity of the operations being performed\. To determine the number of database connections, associate your DB instance with a parameter group where the `User Connections` parameter is set to a value other than 0 \(unlimited\)\. You can either use an existing parameter group or create a new one\. For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\. 
+  **IOPS metrics** – The expected values for IOPS metrics depend on disk specification and server configuration, so use your baseline to know what is typical\. Investigate if values are consistently different than your baseline\. For best IOPS performance, make sure that your typical working set fits into memory to minimize read and write operations\. 

## Monitoring Tools<a name="monitoring_automated_manual"></a>

AWS provides various tools that you can use to monitor Amazon RDS\. You can configure some of these tools to do the monitoring for you, and other tools require manual intervention\. We recommend that you automate monitoring tasks as much as possible\.

### Automated Monitoring Tools<a name="monitoring_automated_tools"></a>

You can use the following automated monitoring tools to watch Amazon RDS and report when something is wrong:
+ **Amazon RDS Events** – Subscribe to Amazon RDS events to be notified when changes occur with a DB instance, DB snapshot, DB parameter group, or DB security group\. For more information, see [Using Amazon RDS Event Notification](USER_Events.md)\.
+ **Database log files** – View, download, or watch database log files using the Amazon RDS console or Amazon RDS API operations\. You can also query some database log files that are loaded into database tables\. For more information, see [Amazon RDS Database Log Files](USER_LogAccess.md)\.
+ **Amazon RDS Enhanced Monitoring** — Look at metrics in real time for the operating system\. For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.
+ **Amazon RDS Performance Insights** — Assess the load on your database, and determine when and where to take action\. For more information, see [Using Amazon RDS Performance Insights](USER_PerfInsights.md)\.
+ **Amazon RDS Recommendations** — Look at automated recommendations for database resources, such as DB instances, read replicas, and DB parameter groups\. For more information, see [Using Amazon RDS Recommendations](USER_Recommendations.md)\.

In addition, Amazon RDS integrates with Amazon CloudWatch, Amazon EventBridge, and AWS CloudTrail for additional monitoring capabilities:
+ **Amazon CloudWatch Metrics** – Amazon RDS automatically sends metrics to CloudWatch every minute for each active database\. You don't get additional charges for Amazon RDS metrics in CloudWatch\. For more information, see [Viewing DB Instance Metrics](#USER_Monitoring)\.
+ **Amazon CloudWatch Alarms** – You can watch a single Amazon RDS metric over a specific time period\. You can then perform one or more actions based on the value of the metric relative to a threshold that you set\. For more information, see [Monitoring with Amazon CloudWatch](#monitoring-cloudwatch)\.
+ **Amazon CloudWatch Logs** – Most DB engines enable you to monitor, store, and access your database log files in CloudWatch Logs\. For more information, see [Amazon CloudWatch Logs User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/)\.
+ **Amazon CloudWatch Events and Amazon EventBridge** – You can automate AWS services and respond to system events such as application availability issues or resource changes\. Events from AWS services are delivered to CloudWatch Events and EventBridge nearly in real time\. You can write simple rules to indicate which events interest you and what automated actions to take when an event matches a rule\. For more information, see [Getting CloudWatch Events and Amazon EventBridge Events for Amazon RDS](rds-cloud-watch-events.md)\.
+ **AWS CloudTrail** – You can view a record of actions taken by a user, role, or an AWS service in Amazon RDS\. CloudTrail captures all API calls for Amazon RDS as events\. These captures include calls from the Amazon RDS console and from code calls to the Amazon RDS API operations\. If you create a trail, you can enable continuous delivery of CloudTrail events to an Amazon S3 bucket, including events for Amazon RDS\. If you don't configure a trail, you can still view the most recent events in the CloudTrail console in **Event history**\. For more information, see [Working with AWS CloudTrail and Amazon RDS ](logging-using-cloudtrail.md)\.

### Manual Monitoring Tools<a name="monitoring_manual_tools"></a>

Another important part of monitoring Amazon RDS involves manually monitoring those items that the CloudWatch alarms don't cover\. The Amazon RDS, CloudWatch, AWS Trusted Advisor and other AWS console dashboards provide an at\-a\-glance view of the state of your AWS environment\. We recommend that you also check the log files on your DB instance\.
+ From the Amazon RDS console, you can monitor the following items for your resources:
  + The number of connections to a DB instance
  + The amount of read and write operations to a DB instance
  + The amount of storage that a DB instance is currently using
  + The amount of memory and CPU being used for a DB instance
  + The amount of network traffic to and from a DB instance
+ From the Trusted Advisor dashboard, you can review the following cost optimization, security, fault tolerance, and performance improvement checks:
  + Amazon RDS Idle DB Instances
  + Amazon RDS Security Group Access Risk
  + Amazon RDS Backups
  + Amazon RDS Multi\-AZ

  For more information on these checks, see [Trusted Advisor Best Practices \(Checks\)](https://aws.amazon.com/premiumsupport/trustedadvisor/best-practices/)\.
+ CloudWatch home page shows:
  + Current alarms and status
  + Graphs of alarms and resources
  + Service health status

  In addition, you can use CloudWatch to do the following: 
  + Create [customized dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/CloudWatch_Dashboards.html) to monitor the services that you care about\.
  + Graph metric data to troubleshoot issues and discover trends\.
  + Search and browse all your AWS resource metrics\.
  + Create and edit alarms to be notified of problems\.

## Monitoring with Amazon CloudWatch<a name="monitoring-cloudwatch"></a>

You can monitor DB instances using Amazon CloudWatch, which collects and processes raw data from Amazon RDS into readable, near real\-time metrics\. By default, Amazon RDS metric data is automatically sent to CloudWatch in 1\-minute periods\. For more information about CloudWatch, see [ What Is Amazon CloudWatch?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\.

Data points with a period of 60 seconds \(1 minute\) are available for 15 days\. This means that you can access historical information and gain a better perspective on how your web application or service is performing\. For more information about CloudWatch metrics retention, see [Metrics Retention](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/cloudwatch_concepts.html#metrics-retention) in the *Amazon CloudWatch User Guide*\.

**Note**  
If you are using Amazon RDS Performance Insights, additional metrics are available\. For more information, see [Performance Insights Metrics Published to Amazon CloudWatch](USER_PerfInsights.Cloudwatch.md)\.

### Amazon RDS Metrics and Dimensions<a name="metrics_dimensions"></a>

When you use Amazon RDS resources, Amazon RDS sends metrics and dimensions to Amazon CloudWatch every minute\. You can use the following procedures to view the metrics for Amazon RDS\.

**To view metrics using the Amazon CloudWatch console**

Metrics are grouped first by the service namespace, and then by the various dimension combinations within each namespace\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. If necessary, change the AWS Region\. From the navigation bar, choose the AWS Region where your AWS resources are\. For more information, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)\.

1. In the navigation pane, choose **Metrics**\. Choose the **RDS** metric namespace\.  
![\[Choose metric namespace\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds-monitoring-01.png)

1. Choose a metric dimension, for example **By Database Class**\.

1. To sort the metrics, use the column heading\. To graph a metric, select the check box next to the metric\. To filter by resource, choose the resource ID, and then choose **Add to search**\. To filter by metric, choose the metric name, and then choose **Add to search**\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds-monitoring-03.png)

**To view metrics using the AWS CLI**
+ At a command prompt, use the following command\.

  ```
  1. aws cloudwatch list-metrics --namespace AWS/RDS
  ```

#### Amazon RDS Metrics<a name="rds-metrics"></a>

The `AWS/RDS` namespace includes the following metrics\.

**Note**  
The Amazon RDS console might display metrics in units that are different from the units sent to Amazon CloudWatch\. For example, the Amazon RDS console might display a metric in megabytes \(MB\), while the metric is sent to Amazon CloudWatch in bytes\.


| Metric | Console Name | Description | 
| --- | --- | --- | 
| BinLogDiskUsage |   **Binary Log Disk Usage \(MB\)**   |  The amount of disk space occupied by binary logs on the primary\. Applies to MySQL read replicas\. Units: Bytes  | 
| BurstBalance |   **Burst Balance \(Percent\)**   |  The percent of General Purpose SSD \(gp2\) burst\-bucket I/O credits available\.  Units: Percent  | 
| CPUUtilization |   **CPU Utilization \(Percent\)**   |  The percentage of CPU utilization\. Units: Percent  | 
| CPUCreditUsage |   **CPU Credit Usage \(Count\)**   |  \(T2 instances\) The number of CPU credits spent by the instance for CPU utilization\. One CPU credit equals one vCPU running at 100 percent utilization for one minute or an equivalent combination of vCPUs, utilization, and time\. For example, you might have one vCPU running at 50 percent utilization for two minutes or two vCPUs running at 25 percent utilization for two minutes\. CPU credit metrics are available at a five\-minute frequency only\. If you specify a period greater than five minutes, use the `Sum` statistic instead of the `Average` statistic\. Units: Credits \(vCPU\-minutes\)  | 
| CPUCreditBalance |   **CPU Credit Balance \(Count\)**   | \(T2 instances\) The number of earned CPU credits that an instance has accrued since it was launched or started\. For T2 Standard, the CPUCreditBalance also includes the number of launch credits that have been accrued\.Credits are accrued in the credit balance after they are earned, and removed from the credit balance when they are spent\. The credit balance has a maximum limit, determined by the instance size\. After the limit is reached, any new credits that are earned are discarded\. For T2 Standard, launch credits don't count towards the limit\.The credits in the `CPUCreditBalance` are available for the instance to spend to burst beyond its baseline CPU utilization\.When an instance is running, credits in the `CPUCreditBalance` don't expire\. When the instance stops, the `CPUCreditBalance` does not persist, and all accrued credits are lost\.CPU credit metrics are available at a five\-minute frequency only\.Units: Credits \(vCPU\-minutes\) | 
| DatabaseConnections |   **DB Connections \(Count\)**   |  The number of database connections in use\. The metric value might not include broken database connections that haven't been cleaned up by your database yet\. So, the number of database connections recorded by your database might be higher than the metric value\. Units: Count  | 
| DiskQueueDepth |   **Queue Depth \(Count\)**   |  The number of outstanding I/Os \(read/write requests\) waiting to access the disk\. Units: Count  | 
| FailedSQLServerAgentJobsCount  |   **Failed SQL Server Agent Jobs Count \(Count/Minute\)**   |  The number of failed Microsoft SQL Server Agent jobs during the last minute\. Unit: Count/Minute  | 
| FreeableMemory |   **Freeable Memory \(MB\)**   |  The amount of available random access memory\.  For MariaDB, MySQL, Oracle, and PostgreSQL DB instances, this metric reports the value of the `MemAvailable` field of `/proc/meminfo`\.   Units: Bytes  | 
| FreeStorageSpace |   **Free Storage Space \(MB\)**   |  The amount of available storage space\. Units: Bytes  | 
| MaximumUsedTransactionIDs |   **Maximum Used Transaction IDs \(Count\)**   |  The maximum transaction IDs that have been used\. Applies to PostgreSQL\.  Units: Count  | 
| NetworkReceiveThroughput |   **Network Receive Throughput \(MB/Second\)**   |  The incoming \(receive\) network traffic on the DB instance, including both customer database traffic and Amazon RDS traffic used for monitoring and replication\. Units: Bytes/Second  | 
| NetworkTransmitThroughput |   **Network Transmit Throughput \(MB/Second\)**   |  The outgoing \(transmit\) network traffic on the DB instance, including both customer database traffic and Amazon RDS traffic used for monitoring and replication\. Units: Bytes/Second  | 
| OldestReplicationSlotLag |   **Oldest Replication Slot Lag \(MB\)**   |  The lagging size of the replica lagging the most in terms of write\-ahead log \(WAL\) data received\. Applies to PostgreSQL\. Units: Megabytes  | 
| ReadIOPS |   **Read IOPS \(Count/Second\)**   |  The average number of disk read I/O operations per second\. Units: Count/Second  | 
| ReadLatency |   **Read Latency \(Milliseconds\)**   |  The average amount of time taken per disk I/O operation\. Units: Seconds  | 
| ReadThroughput |   **Read Throughput \(MB/Second\)**   |  The average number of bytes read from disk per second\. Units: Bytes/Second  | 
| ReplicaLag |   **Replica Lag \(Milliseconds\)**   |  The amount of time a read replica DB instance lags behind the source DB instance\. Applies to MySQL, MariaDB, Oracle, PostgreSQL, and SQL Server read replicas\. Units: Seconds  | 
| ReplicationSlotDiskUsage |   **Replica Slot Disk Usage \(MB\)**   |  The disk space used by replication slot files\. Applies to PostgreSQL\. Units: Megabytes  | 
| SwapUsage |   **Swap Usage \(MB\)**   |  The amount of swap space used on the DB instance\. This metric is not available for SQL Server\. Units: Bytes  | 
| TransactionLogsDiskUsage |   **Transaction Logs Disk Usage \(MB\)**   |  The disk space used by transaction logs\. Applies to PostgreSQL\. Units: Megabytes  | 
| TransactionLogsGeneration |   **Transaction Logs Generation \(MB/Second\)**   |  The size of transaction logs generated per second\. Applies to PostgreSQL\. Units: Bytes/Second  | 
| WriteIOPS |   **Write IOPS \(Count/Second\)**   |  The average number of disk write I/O operations per second\. Units: Count/Second  | 
| WriteLatency |   **Write Latency \(Milliseconds\)**   |  The average amount of time taken per disk I/O operation\. Units: Seconds  | 
| WriteThroughput |   **Write Throughput \(MB/Second\)**   |  The average number of bytes written to disk per second\. Units: Bytes/Second  | 

#### Amazon RDS Dimensions<a name="dimensions"></a>

Amazon RDS metrics data can be filtered by using any of the dimensions in the following table:


|  Dimension  |  Description  | 
| --- | --- | 
|  DBInstanceIdentifier  |  This dimension filters the data that you request for a specific DB instance\.  | 
|  DBClusterIdentifier  |  This dimension filters the data that you request for a specific Amazon Aurora DB cluster\.  | 
|  DBClusterIdentifier, Role  |  This dimension filters the data that you request for a specific Aurora DB cluster, aggregating the metric by instance role \(WRITER/READER\)\. For example, you can aggregate metrics for all READER instances that belong to a cluster\.  | 
|  DatabaseClass  |  This dimension filters the data that you request for all instances in a database class\. For example, you can aggregate metrics for all instances that belong to the database class `db.r5.large`\.  | 
|  EngineName  |  This dimension filters the data that you request for the identified engine name only\. For example, you can aggregate metrics for all instances that have the engine name `mysql`\.  | 
|  SourceRegion  |  This dimension filters the data that you request for the specified region only\. For example, you can aggregate metrics for all instances in the `us-east-1` Region\.  | 

#### Creating CloudWatch Alarms to Monitor Amazon RDS<a name="creating_alarms"></a>

You can create a CloudWatch alarm that sends an Amazon SNS message when the alarm changes state\. An alarm watches a single metric over a time period that you specify\. The alarm can also perform one or more actions based on the value of the metric relative to a given threshold over a number of time periods\. The action is a notification sent to an Amazon SNS topic or Amazon EC2 Auto Scaling policy\.

Alarms invoke actions for sustained state changes only\. CloudWatch alarms don't invoke actions simply because they are in a particular state\. The state must have changed and have been maintained for a specified number of time periods\. The following procedures show how to create alarms for Amazon RDS\.

**To set alarms using the CloudWatch console**

1. Sign in to the AWS Management Console and open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Choose **Alarms** and then choose **Create Alarm**\. Doing this launches the Create Alarm Wizard\. 

1. Choose **RDS Metrics** and scroll through the Amazon RDS metrics to find the metric that you want to place an alarm on\. To display just Amazon RDS metrics, search for the identifier of your resource\. Choose the metric to create an alarm on and then choose **Next**\.

1. Enter **Name**, **Description**, and **Whenever** values for the metric\. 

1. If you want CloudWatch to send you an email when the alarm state is reached, for **Whenever this alarm**, choose **State is ALARM**\. For **Send notification to**, choose an existing SNS topic\. If you choose **Create topic**, you can set the name and email addresses for a new email subscription list\. This list is saved and appears in the field for future alarms\. 
**Note**  
If you use **Create topic** to create a new Amazon SNS topic, the email addresses must be verified before they receive notifications\. Emails are only sent when the alarm enters an alarm state\. If this alarm state change happens before the email addresses are verified, the addresses don't receive a notification\.

1. Preview the alarm that you're about to create in the **Alarm Preview** area, and then choose **Create Alarm**\. 

**To set an alarm using the AWS CLI**
+ Call [https://docs.aws.amazon.com/cli/latest/reference/put-metric-alarm.html](https://docs.aws.amazon.com/cli/latest/reference/put-metric-alarm.html)\. For more information, see *[AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/)*\.

**To set an alarm using the CloudWatch API**
+ Call [https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_PutMetricAlarm.html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_PutMetricAlarm.html)\. For more information, see *[Amazon CloudWatch API Reference](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/)* 

## Publishing Database Engine Logs to Amazon CloudWatch Logs<a name="publishing_cloudwatchlogs"></a>

You can configure your Amazon RDS database engine to publish log data to a log group in Amazon CloudWatch Logs\. With CloudWatch Logs, you can perform real\-time analysis of the log data, and use CloudWatch to create alarms and view metrics\. You can use CloudWatch Logs to store your log records in highly durable storage, which you can manage with the CloudWatch Logs Agent\. For example, you can determine when to rotate log records from a host to the log service, so you can access the raw logs when you need to\. 

For engine\-specific information, see the following topics:
+ [Publishing MariaDB Logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.MariaDB.md#USER_LogAccess.MariaDB.PublishtoCloudWatchLogs)
+ [Publishing MySQL Logs to CloudWatch Logs](USER_LogAccess.Concepts.MySQL.md#USER_LogAccess.MySQLDB.PublishtoCloudWatchLogs)
+ [Publishing Oracle Logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.Oracle.md#USER_LogAccess.Oracle.PublishtoCloudWatchLogs)
+ [Publishing PostgreSQL Logs to CloudWatch Logs](USER_LogAccess.Concepts.PostgreSQL.md#USER_LogAccess.PostgreSQL.PublishtoCloudWatchLogs)
+ [Publishing SQL Server Logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.SQLServer.md#USER_LogAccess.SQLServer.PublishtoCloudWatchLogs)

**Note**  
Before you enable log data publishing, make sure that you have a service\-linked role in AWS Identity and Access Management \(IAM\)\. For more information about service\-linked roles, see [Using Service\-Linked Roles for Amazon RDS](UsingWithRDS.IAM.ServiceLinkedRoles.md)\.

### Configuring CloudWatch Log Integration<a name="integrating_cloudwatchlogs.configure"></a>

To publish your database log files to CloudWatch Logs, choose which logs to publish\. Make this choice in the **Advanced Settings** section when you create a new DB instance\. You can also modify an existing DB instance to begin publishing\. 

![\[Add CloudWatch Logs\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AddCWLogs.png)

 After you have enabled publishing, Amazon RDS continuously streams all of the DB instance log records to a log group\. For example, you have a log group `/aws/rds/instance/log type` for each type of log that you publish\. This log group is in the same AWS Region as the database instance that generates the log\.

 After you have published log records, you can use CloudWatch Logs to search and filter the records\. For more information about searching and filtering logs, see [Searching and Filtering Log Data](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/MonitoringLogData.html)\. 

### Viewing DB Instance Metrics<a name="USER_Monitoring"></a>

Amazon RDS provides metrics so that you can monitor the health of your DB instances\. You can monitor both DB instance metrics and operating system \(OS\) metrics\.

Following, you can find details about how to view metrics for your DB instance using the RDS console and CloudWatch\. For information on monitoring metrics for your DB instance's operating system in real time using CloudWatch Logs, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.

#### Viewing Metrics by Using the Console<a name="USER_Monitoring.CON"></a>

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

#### Viewing DB Instance Metrics with the CLI or API<a name="USER_Monitoring.DB"></a>

Amazon RDS integrates with CloudWatch metrics to provide a variety of DB instance metrics\. You can view CloudWatch metrics using the RDS console, AWS CLI, or API\.

 For a complete list of Amazon RDS metrics, go to [ Amazon RDS Dimensions and Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/rds-metricscollected.html) in the *Amazon CloudWatch User Guide*\. 

##### Viewing DB Metrics by Using the CloudWatch CLI<a name="USER_Monitoring.CLI"></a>

**Note**  
The following CLI example requires the CloudWatch command line tools\. For more information on CloudWatch and to download the developer tools, see [Amazon CloudWatch](https://aws.amazon.com/cloudwatch) on the AWS website\. The `StartTime` and `EndTime` values supplied in this example are for illustration only\. Substitute appropriate start and end time values for your DB instance\.

**To view usage and performance statistics for a DB instance**
+ Use the CloudWatch command `mon-get-stats` with the following parameters\.

  ```
  1. PROMPT>mon-get-stats FreeStorageSpace --dimensions="DBInstanceIdentifier=mydbinstance" --statistics= Average 
  2.   --namespace="AWS/RDS" --start-time 2009-10-16T00:00:00 --end-time 2009-10-16T00:02:00
  ```

##### Viewing DB Metrics by Using the CloudWatch API<a name="USER_Monitoring.API"></a>

The `StartTime` and `EndTime` values supplied in this example are for illustration only\. Substitute appropriate start and end time values for your DB instance\.

**To view usage and performance statistics for a DB instance**
+ Call the CloudWatch API `GetMetricStatistics` with the following parameters:
  + `Statistics.member.1` = `Average`
  + `Namespace` = `AWS/RDS`
  + `StartTime` = `2009-10-16T00:00:00`
  + `EndTime` = `2009-10-16T00:02:00`
  + `Period` = `60`
  + `MeasureName` = `FreeStorageSpace`