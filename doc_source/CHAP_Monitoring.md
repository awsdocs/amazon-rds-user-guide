# Monitoring Amazon RDS<a name="CHAP_Monitoring"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of Amazon RDS and your AWS solutions\. You should collect monitoring data from all of the parts of your AWS solution so that you can more easily debug a multi\-point failure if one occurs\. Before you start monitoring Amazon RDS, we recommend that you create a monitoring plan that includes answers to the following questions:

+ What are your monitoring goals?

+ What resources will you monitor?

+ How often will you monitor these resources?

+ What monitoring tools will you use?

+ Who will perform the monitoring tasks?

+ Who should be notified when something goes wrong?

The next step is to establish a baseline for normal Amazon RDS performance in your environment, by measuring performance at various times and under different load conditions\. As you monitor Amazon RDS, you should consider storing historical monitoring data\. This stored data will give you a baseline to compare against with current performance data, identify normal performance patterns and performance anomalies, and devise methods to address issues\.

For example, with Amazon RDS, you can monitor network throughput, I/O for read, write, and/or metadata operations, client connections, and burst credit balances for your DB instances\. When performance falls outside your established baseline, you might need change the instance class of your DB instance or the number of DB instances and Read Replicas that are available for clients in order to optimize your database availability for your workload\.

In general, acceptable values for performance metrics depend on what your baseline looks like and what your application is doing\. Investigate consistent or trending variances from your baseline\. Advice about specific types of metrics follows: 

+  **High CPU or RAM consumption** – High values for CPU or RAM consumption might be appropriate, provided that they are in keeping with your goals for your application \(like throughput or concurrency\) and are expected\. 

+  **Disk space consumption** – Investigate disk space consumption if space used is consistently at or above 85 percent of the total disk space\. See if it is possible to delete data from the instance or archive data to a different system to free up space\. 

+  **Network traffic** – For network traffic, talk with your system administrator to understand what expected throughput is for your domain network and Internet connection\. Investigate network traffic if throughput is consistently lower than expected\. 

+  **Database connections** – Consider constraining database connections if you see high numbers of user connections in conjunction with decreases in instance performance and response time\. The best number of user connections for your DB instance will vary based on your instance class and the complexity of the operations being performed\. You can determine the number of database connections by associating your DB instance with a parameter group where the `User Connections` parameter is set to a value other than 0 \(unlimited\)\. You can either use an existing parameter group or create a new one\. For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\. 

+  **IOPS metrics** – The expected values for IOPS metrics depend on disk specification and server configuration, so use your baseline to know what is typical\. Investigate if values are consistently different than your baseline\. For best IOPS performance, make sure your typical working set will fit into memory to minimize read and write operations\. 

## Monitoring Tools<a name="monitoring_automated_manual"></a>

AWS provides various tools that you can use to monitor Amazon RDS\. You can configure some of these tools to do the monitoring for you, while some of the tools require manual intervention\. We recommend that you automate monitoring tasks as much as possible\.

### Automated Monitoring Tools<a name="monitoring_automated_tools"></a>

You can use the following automated monitoring tools to watch Amazon RDS and report when something is wrong:

+ **Amazon CloudWatch Alarms** – Watch a single metric over a time period that you specify, and perform one or more actions based on the value of the metric relative to a given threshold over a number of time periods\. The action is a notification sent to an Amazon Simple Notification Service \(Amazon SNS\) topic or Auto Scaling policy\. CloudWatch alarms do not invoke actions simply because they are in a particular state; the state must have changed and been maintained for a specified number of periods\. For more information, see [Monitoring with Amazon CloudWatch](#monitoring-cloudwatch)\.

+ **Amazon CloudWatch Logs** – Monitor, store, and access your log files from AWS CloudTrail or other sources\. For more information, see [Monitoring Log Files](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatchLogs.html) in the *Amazon CloudWatch User Guide*\.

+ **Amazon RDS Enhanced Monitoring** — provides metrics in real time for the operating system that your DB instance or DB cluster runs on\. For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.

+ **Amazon CloudWatch Events** – Match events and route them to one or more target functions or streams to make changes, capture state information, and take corrective action\. For more information, see [What is Amazon CloudWatch Events](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatchEvents.html) in the *Amazon CloudWatch User Guide*\.

+ **AWS CloudTrail Log Monitoring** – Share log files between accounts, monitor CloudTrail log files in real time by sending them to CloudWatch Logs, write log processing applications in Java, and validate that your log files have not changed after delivery by CloudTrail\. For more information, see [Working with CloudTrail Log Files](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-working-with-log-files.html) in the *AWS CloudTrail User Guide*\. 

  For information on using AWS CloudTrail Log Monitoring with Amazon RDS, see [Logging Amazon RDS API Calls Using AWS CloudTrail](USER_Auditing.md)\.

+ **Amazon RDS Events** – Subscribe to Amazon RDS events to be notified when changes occur with a DB instance, DB cluster, DB snapshot, DB cluster snapshot, DB parameter group, or DB security group\. For more information, see [Using Amazon RDS Event Notification](USER_Events.md)\.

+ **Database log files** – View, download, or watch database log files using the Amazon RDS console or Amazon RDS APIs\. You can also query some database log files that are loaded into database tables\. For more information, see [Amazon RDS Database Log Files](USER_LogAccess.md)\.

### Manual Monitoring Tools<a name="monitoring_manual_tools"></a>

Another important part of monitoring Amazon RDS involves manually monitoring those items that the CloudWatch alarms don't cover\. The Amazon RDS, CloudWatch, AWS Trusted Advisor and other AWS console dashboards provide an at\-a\-glance view of the state of your AWS environment\. We recommend that you also check the log files on your DB instance\.

+ From the Amazon RDS console, you can monitor the following items for your resources:

  + The number of connections to a DB instance

  + The amount of read and write operations to a DB instance

  + The amount of storage that a DB instance is currently utilizing

  + The amount of memory and CPU being utilized for a DB instance

  + The amount of network traffic to and from a DB instance

+ From the AWS Trusted Advisor dashboard, you can review the following cost optimization, security, fault tolerance, and performance improvement checks:

  + Amazon RDS Idle DB Instances

  + Amazon RDS Security Group Access Risk

  + Amazon RDS Backups

  + Amazon RDS Multi\-AZ

  + Amazon Aurora DB Instance Accessibility

  For more information on these checks, see [Trusted Advisor Best Practices \(Checks\)](https://aws.amazon.com/premiumsupport/trustedadvisor/best-practices/)\.

+ CloudWatch home page shows:

  + Current alarms and status

  + Graphs of alarms and resources

  + Service health status

  In addition, you can use CloudWatch to do the following: 

  + Create [customized dashboards](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/CloudWatch_Dashboards.html) to monitor the services you care about

  + Graph metric data to troubleshoot issues and discover trends

  + Search and browse all your AWS resource metrics

  + Create and edit alarms to be notified of problems

## Monitoring with Amazon CloudWatch<a name="monitoring-cloudwatch"></a>

You can monitor DB instance using CloudWatch, which collects and processes raw data from Amazon RDS into readable, near real\-time metrics\. These statistics are recorded for a period of two weeks, so that you can access historical information and gain a better perspective on how your web application or service is performing\. By default, Amazon RDS metric data is automatically sent to Amazon CloudWatch in 1\-minute periods\. For more information about Amazon CloudWatch, see [What Are Amazon CloudWatch, Amazon CloudWatch Events, and Amazon CloudWatch Logs?](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\.

### Amazon RDS Metrics and Dimensions<a name="metrics_dimensions"></a>

When you use Amazon RDS resources, Amazon RDS sends metrics and dimensions to Amazon CloudWatch every minute\. You can use the following procedures to view the metrics for Amazon RDS\.

**To view metrics using the Amazon CloudWatch console**

Metrics are grouped first by the service namespace, and then by the various dimension combinations within each namespace\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. If necessary, change the region\. From the navigation bar, select the region where your AWS resources reside\. For more information, see [Regions and Endpoints](http://docs.aws.amazon.com/general/latest/gr/rande.html)\.

1. In the navigation pane, choose **Metrics**\. Choose the **RDS** metric namespace\.  
![\[Choose metric namespace\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds-monitoring-01.png)

1. Select a metric dimension, for example, **By Database Class**\.  
![\[Choose metric dimension\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds-monitoring-02.png)

1. To sort the metrics, use the column heading\. To graph a metric, select the check box next to the metric\. To filter by resource, choose the resource ID and then choose **Add to search**\. To filter by metric, choose the metric name and then choose **Add to search**\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds-monitoring-03.png)

**To view metrics using the AWS CLI**

+ At a command prompt, use the following command:

  ```
  1. aws cloudwatch list-metrics --namespace AWS/RDS
  ```

#### Amazon RDS Metrics<a name="rds-metrics"></a>

The `AWS/RDS` namespace includes the following metrics\.


| Metric | Description | 
| --- | --- | 
| BinLogDiskUsage |  The amount of disk space occupied by binary logs on the master\. Applies to MySQL read replicas\. Units: Bytes  | 
| BurstBalance |  The percent of General Purpose SSD \(gp2\) burst\-bucket I/O credits available\.  Units: Percent  | 
| CPUUtilization |  The percentage of CPU utilization\. Units: Percent  | 
| CPUCreditUsage |  \[T2 instances\] The number of CPU credits used by the instance for CPU utilization\. One CPU credit equals one vCPU running at 100% utilization for one minute or an equivalent combination of vCPUs, utilization, and time \(for example, one vCPU running at 50% utilization for two minutes or two vCPUs running at 25% utilization for two minutes\)\. CPU credit metrics are available at a five\-minute frequency only\. If you specify a period greater than five minutes, use the `Sum` statistic instead of the `Average` statistic\. Units: Credits \(vCPU\-minutes\)  | 
| CPUCreditBalance |  \[T2 instances\] The number of earned CPU credits accumulated since the instance was launched, less the credits used, up to a maximum number based on the instance size\. Credits are stored in the credit balance after they are earned, and removed from the credit balance when they are used\. The credit balance has a maximum limit, determined by the instance size\. If the credit balance has reached the limit, additional earned credits are not added to the balance\. The credits in the `CPUCreditBalance` are available for the instance to use to burst beyond its baseline CPU utilization\. Credits on a running instance do not expire\. However, if you stop an instance, it loses all the credits in the credit balance\. CPU credit metrics are available at a five\-minute frequency only\. Units: Credits \(vCPU\-minutes\)  | 
| CPUSurplusCreditBalance  |  \[T2 instances\] The number of surplus credits that have been used by a T2 Unlimited instance when its `CPUCreditBalance` is zero\. The `CPUSurplusCreditBalance` is paid down by earned CPU credits\. Units: Credits \(vCPU\-minutes\)   | 
| CPUSurplusCreditsCharged |  \[T2 instances\] The number of surplus credits that have been used by a T2 Unlimited instance that are not offset by earned CPU credits\. `CPUSurplusCreditsCharged` tracks the surplus credits that incur an additional charge, and represents the difference between `CPUSurplusCreditBalance` and `CPUCreditBalance`\. Units: Credits \(vCPU\-minutes\)   | 
| DatabaseConnections |  The number of database connections in use\. Units: Count  | 
| DiskQueueDepth |  The number of outstanding IOs \(read/write requests\) waiting to access the disk\. Units: Count  | 
| FreeableMemory |  The amount of available random access memory\. Units: Bytes  | 
| FreeStorageSpace |  The amount of available storage space\. Units: Bytes  | 
| MaximumUsedTransactionIDs |  The maximum transaction ID that has been used\. Applies to PostgreSQL\. Units: Count  | 
| NetworkReceiveThroughput |  The incoming \(Receive\) network traffic on the DB instance, including both customer database traffic and Amazon RDS traffic used for monitoring and replication\. Units: Bytes/second  | 
| NetworkTransmitThroughput |  The outgoing \(Transmit\) network traffic on the DB instance, including both customer database traffic and Amazon RDS traffic used for monitoring and replication\. Units: Bytes/second  | 
| OldestReplicationSlotLag |  The lagging size of the replica lagging the most in terms of WAL data received\. Applies to PostgreSQL\. Units: Megabytes  | 
| ReadIOPS |  The average number of disk I/O operations per second during the polling period\. Units: Count/Second  | 
| ReadLatency |  The average amount of time taken per disk I/O operation\. Units: Seconds  | 
| ReadThroughput |  The average number of bytes read from disk per second\. Units: Bytes/Second  | 
| ReplicaLag |  The amount of time a Read Replica DB instance lags behind the source DB instance\. Applies to MySQL, MariaDB, and PostgreSQL Read Replicas\. Units: Seconds  | 
| ReplicationSlotDiskUsage |  The disk space used by replication slot files\. Applies to PostgreSQL\. Units: Megabytes  | 
| SwapUsage |  The amount of swap space used on the DB instance\. Units: Bytes  | 
| TransactionLogsDiskUsage |  The disk space used by transaction logs\. Applies to PostgreSQL\. Units: Megabytes  | 
| TransactionLogsGeneration |  The size of transaction logs generated per second\. Applies to PostgreSQL\. Units: Megabytes/second  | 
| WriteIOPS |  The average number of disk I/O operations per second\. Units: Count/Second  | 
| WriteLatency |  The average amount of time taken per disk I/O operation\. Units: Seconds  | 
| WriteThroughput |  The average number of bytes written to disk per second\. Units: Bytes/Second  | 

#### Amazon RDS Dimensions<a name="dimensions"></a>

Amazon RDS metrics data can be filtered by using any of the dimensions in the following table:


****  

|  Dimension  |  Description  | 
| --- | --- | 
|  DBInstanceIdentifier  |  This dimension filters the data you request for a specific DB instance\.  | 
|  DBClusterIdentifier  |  This dimension filters the data you request for a specific Amazon Aurora DB cluster\.  | 
|  DBClusterIdentifier, Role  |  This dimension filters the data you request for a specific Amazon Aurora DB cluster, aggregating the metric by instance role \(WRITER/READER\)\. For example, you can aggregate metrics for all READER instances that belong to a cluster\.  | 
|  DatabaseClass  |  This dimension filters the data you request for all instances in a database class\. For example, you can aggregate metrics for all instances that belong to the database class `db.m1.small`  | 
|  EngineName  |  This dimension filters the data you request for the identified engine name only\. For example, you can aggregate metrics for all instances that have the engine name `mysql`\.  | 

### Creating CloudWatch Alarms to Monitor Amazon RDS<a name="creating_alarms"></a>

You can create a CloudWatch alarm that sends an Amazon SNS message when the alarm changes state\. An alarm watches a single metric over a time period you specify, and performs one or more actions based on the value of the metric relative to a given threshold over a number of time periods\. The action is a notification sent to an Amazon SNS topic or Auto Scaling policy\.

Alarms invoke actions for sustained state changes only\. CloudWatch alarms will not invoke actions simply because they are in a particular state, the state must have changed and been maintained for a specified number of periods\. The following procedures outlines how to create alarms for Amazon RDS\.

**To set alarms using the CloudWatch console**

1. Sign in to the AWS Management Console and open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Choose **Alarms** and then choose **Create Alarm**\. This launches the **Create Alarm Wizard**\. 

1. Choose **RDS Metrics** and scroll through the Amazon RDS metrics to locate the metric you want to place an alarm on\. To display just the Amazon RDS metrics in this dialog box, search for the identifier of your resource\. Select the metric to create an alarm on and then choose **Next**\.

1. Fill in the **Name**, **Description**, **Whenever** values for the metric\. 

1. If you want CloudWatch to send you an email when the alarm state is reached, in the **Whenever this alarm:** field, choose **State is ALARM**\. In the **Send notification to:** field, choose an existing SNS topic\. If you select **Create topic**, you can set the name and email addresses for a new email subscription list\. This list is saved and appears in the field for future alarms\. 
**Note**  
If you use **Create topic** to create a new Amazon SNS topic, the email addresses must be verified before they receive notifications\. Emails are only sent when the alarm enters an alarm state\. If this alarm state change happens before the email addresses are verified, they do not receive a notification\.

1. At this point, the **Alarm Preview** area gives you a chance to preview the alarm you’re about to create\. Choose **Create Alarm**\. 

**To set an alarm using the AWS CLI**

+ Call [http://docs.aws.amazon.com/cli/latest/reference/put-metric-alarm.html](http://docs.aws.amazon.com/cli/latest/reference/put-metric-alarm.html)\. For more information, see *[AWS Command Line Interface Reference](http://docs.aws.amazon.com/cli/latest/reference/)*\.

**To set an alarm using the CloudWatch API**

+ Call [http://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_PutMetricAlarm.html](http://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_PutMetricAlarm.html)\. For more information, see *[Amazon CloudWatch API Reference](http://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/)* 

## Viewing DB Instance Metrics<a name="USER_Monitoring"></a>

Amazon RDS provides metrics so that you can monitor the health of your DB instances and DB clusters\. You can monitor both DB instance metrics and operating system \(OS\) metrics\.

This section provides details on how you can view metrics for your DB instance using the RDS console and CloudWatch\. For information on monitoring metrics for the operating system of your DB instance in real time using CloudWatch Logs, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.

### Viewing Metrics by Using the Console<a name="USER_Monitoring.CON"></a>

**To view DB and OS metrics for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **DB Instances**\.

1. Select the check box to the left of the DB cluster you need information about\. For **Show Monitoring**, choose the option for how you want to view your metrics from these:

   + **Show Multi\-Graph View** – Shows a summary of DB instance metrics available from Amazon CloudWatch\. Each metric includes a graph showing the metric monitored over a specific time span\.

   + **Show Single Graph View** – Shows a single metric at a time with more detail\. Each metric includes a graph showing the metric monitored over a specific time span\.

   + **Show Latest Metrics View** – Shows a summary of DB instance metrics without graphs\. **Full Monitoring View** includes an option for full\-screen viewing\.

   + **Enhanced Monitoring** – Shows a summary of OS metrics available for a DB instance with Enhanced Monitoring enabled\. Each metric includes a graph showing the metric monitored over a specific time span\.  
![\[RDS metrics viewing options\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/metrics0.png)
**Tip**  
To select the time range of the metrics represented by the graphs, use **Time Range**\.  
You can choose any graph to bring up a more detailed view of the graph, using which you can apply metric\-specific filters to the metric data\.   
**Time Range** is not available for the Enhanced Monitoring Dashboard\.

### DB Instance Metrics<a name="USER_Monitoring.DB"></a>

Amazon RDS integrates with CloudWatch metrics to provide a variety of DB instance metrics\. You can view CloudWatch metrics using the RDS console, AWS CLI, or API\.

 For a complete list of Amazon RDS metrics, go to [ Amazon RDS Dimensions and Metrics](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/rds-metricscollected.html) in the *Amazon CloudWatch User Guide*\. 

#### Viewing DB Metrics by Using the CloudWatch CLI<a name="USER_Monitoring.CLI"></a>

**Note**  
The following CLI example requires the CloudWatch command line tools\. For more information on CloudWatch and to download the developer tools, go to the [Amazon CloudWatch product page](https://aws.amazon.com/cloudwatch)\. Note that the `StartTime` and `EndTime` values supplied in this example are for illustrative purposes\. You must substitute appropriate start and end time values for your DB instance\.

**To view usage and performance statistics for a DB instance**

+ Use the CloudWatch command `mon-get-stats` with the following parameters:

  ```
  1. PROMPT>mon-get-stats FreeStorageSpace --dimensions="DBInstanceIdentifier=mydbinstance" --statistics= Average 
  2.   --namespace="AWS/RDS" --start-time 2009-10-16T00:00:00 --end-time 2009-10-16T00:02:00
  ```

#### Viewing DB Metrics by Using the CloudWatch API<a name="USER_Monitoring.API"></a>

Note that the `StartTime` and `EndTime` values supplied in this example are for illustrative purposes\. You must substitute appropriate start and end time values for your DB instance\.

**To view usage and performance statistics for a DB instance**

+ Call the CloudWatch API `GetMetricStatistics` with the following parameters:

  + `Statistics.member.1` = `Average`

  + `Namespace` = `AWS/RDS`

  + `StartTime` = `2009-10-16T00:00:00`

  + `EndTime` = `2009-10-16T00:02:00`

  + `Period` = `60`

  + `MeasureName` = `FreeStorageSpace`  
**Example**  

  ```
   1. http://monitoring.amazonaws.com/
   2. 	?SignatureVersion=2
   3. 	&Action=GetMetricStatistics
   4. 	&Version=2009-05-15
   5. 	&StartTime=2009-10-16T00:00:00
   6. 	&EndTime=2009-10-16T00:02:00
   7. 	&Period=60
   8. 	&Statistics.member.1=Average
   9. 	&Dimensions.member.1="DBInstanceIdentifier=mydbinstance"
  10. 	&Namespace=AWS/RDS
  11. 	&MeasureName=FreeStorageSpace						
  12. 	&Timestamp=2009-10-15T17%3A48%3A21.746Z
  13. 	&AWSAccessKeyId=<AWS Access Key ID>
  14. 	&Signature=<Signature>
  ```

## Related Topics<a name="CHAP_Monitoring.related"></a>

+ [Using Amazon RDS Event Notification](USER_Events.md)

+ [Viewing Amazon RDS Events](USER_ListEvents.md)

+ [Amazon RDS Database Log Files](USER_LogAccess.md)

+ [Logging Amazon RDS API Calls Using AWS CloudTrail](USER_Auditing.md)

+ [What Is Amazon Relational Database Service \(Amazon RDS\)?](Welcome.md)