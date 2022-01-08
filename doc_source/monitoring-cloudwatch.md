# Monitoring Amazon RDS metrics with Amazon CloudWatch<a name="monitoring-cloudwatch"></a>

Amazon CloudWatch is a metrics repository\. The repository collects and processes raw data from Amazon RDS into readable, near real\-time metrics\. 

By default, Amazon RDS automatically sends metric data to CloudWatch in 1\-minute periods\. Data points with a period of 60 seconds \(1 minute\) are available for 15 days\. This means that you can access historical information and see how your web application or service is performing\.

![\[RDS metrics in AWS CloudWatch\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds-cloudwatch.png)

For more information about CloudWatch, see [ What is Amazon CloudWatch?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\. For more information about CloudWatch metrics retention, see [Metrics retention](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/cloudwatch_concepts.html#metrics-retention)\.

**Note**  
If you are using Amazon RDS Performance Insights, additional metrics are available\. For more information, see [Performance Insights metrics published to Amazon CloudWatch](USER_PerfInsights.Cloudwatch.md)\.

## Amazon RDS metrics<a name="rds-metrics"></a>

The `AWS/RDS` namespace includes the following metrics\.

**Note**  
The Amazon RDS console might display metrics in units that are different from the units sent to Amazon CloudWatch\. For example, the Amazon RDS console might display a metric in megabytes \(MB\), while the metric is sent to Amazon CloudWatch in bytes\.


| Metric | Console name | Description | Units | 
| --- | --- | --- | --- | 
| BinLogDiskUsage |   **Binary Log Disk Usage \(MB\)**   |  The amount of disk space occupied by binary logs\. If autobackups are enabled for MySQL and MariaDB instances, including read replicas, binary logs are created\.  |  Bytes  | 
| BurstBalance |   **Burst Balance \(Percent\)**   |  The percent of General Purpose SSD \(gp2\) burst\-bucket I/O credits available\.   |  Percent  | 
| CPUUtilization |   **CPU Utilization \(Percent\)**   |  The percentage of CPU utilization\.  |  Percent  | 
| CPUCreditUsage |  **CPU Credit Usage \(Count\)**   |  \(T2 instances\) The number of CPU credits spent by the instance for CPU utilization\. One CPU credit equals one vCPU running at 100 percent utilization for one minute or an equivalent combination of vCPUs, utilization, and time\. For example, you might have one vCPU running at 50 percent utilization for two minutes or two vCPUs running at 25 percent utilization for two minutes\. CPU credit metrics are available at a five\-minute frequency only\. If you specify a period greater than five minutes, use the `Sum` statistic instead of the `Average` statistic\.  |  Credits \(vCPU\-minutes\)  | 
| CPUCreditBalance |  **CPU Credit Balance \(Count\)**   | \(T2 instances\) The number of earned CPU credits that an instance has accrued since it was launched or started\. For T2 Standard, the CPUCreditBalance also includes the number of launch credits that have been accrued\.Credits are accrued in the credit balance after they are earned, and removed from the credit balance when they are spent\. The credit balance has a maximum limit, determined by the instance size\. After the limit is reached, any new credits that are earned are discarded\. For T2 Standard, launch credits don't count towards the limit\.The credits in the `CPUCreditBalance` are available for the instance to spend to burst beyond its baseline CPU utilization\.When an instance is running, credits in the `CPUCreditBalance` don't expire\. When the instance stops, the `CPUCreditBalance` does not persist, and all accrued credits are lost\.CPU credit metrics are available at a five\-minute frequency only\. |  Credits \(vCPU\-minutes\)  | 
| DatabaseConnections |  **DB Connections \(Count\)**   |  The number of client network connections to the database instance\. The number of database sessions can be higher than the metric value because the metric value doesn't include the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/monitoring-cloudwatch.html)  |  Count  | 
| DiskQueueDepth |  **Queue Depth \(Count\)**   |  The number of outstanding I/Os \(read/write requests\) waiting to access the disk\.  |  Count  | 
| EBSByteBalance% |  **EBS Byte Balance \(percent\)**  |  The percentage of throughput credits remaining in the burst bucket of your RDS database\. This metric is available for basic monitoring only\.  To find the instance sizes that support this metric, see the instance sizes with an asterisk \(\*\) in the [EBS optimized by default](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-optimized.html#current) table in *Amazon EC2 User Guide for Linux Instances*\. The `Sum` statistic is not applicable to this metric\.  |  Percent  | 
| EBSIOBalance% |  **EBS IO Balance \(percent\)**  |  The percentage of I/O credits remaining in the burst bucket of your RDS database\. This metric is available for basic monitoring only\. To find the instance sizes that support this metric, see the instance sizes with an asterisk \(\*\) in the [EBS optimized by default](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-optimized.html#current) table in *Amazon EC2 User Guide for Linux Instances*\. The `Sum` statistic is not applicable to this metric\. This metric is different from `BurstBalance`\. To learn how to use this metric, see [Improving application performance and reducing costs with Amazon EBS\-Optimized Instance burst capability](http://aws.amazon.com/blogs/compute/improving-application-performance-and-reducing-costs-with-amazon-ebs-optimized-instance-burst-capability/)\.   |  Percent  | 
| FailedSQLServerAgentJobsCount  |   **Failed SQL Server Agent Jobs Count \(Count/Minute\)**   |  The number of failed Microsoft SQL Server Agent jobs during the last minute\.  |  Count/Minute  | 
| FreeableMemory |   **Freeable Memory \(MB\)**   |  The amount of available random access memory\.  For MariaDB, MySQL, Oracle, and PostgreSQL DB instances, this metric reports the value of the `MemAvailable` field of `/proc/meminfo`\.    |  Bytes  | 
| FreeStorageSpace |   **Free Storage Space \(MB\)**   |  The amount of available storage space\.  |  Bytes  | 
| MaximumUsedTransactionIDs |   **Maximum Used Transaction IDs \(Count\)**   |  The maximum transaction IDs that have been used\. Applies to PostgreSQL\.   |  Count  | 
| NetworkReceiveThroughput |   **Network Receive Throughput \(MB/Second\)**   |  The incoming \(receive\) network traffic on the DB instance, including both customer database traffic and Amazon RDS traffic used for monitoring and replication\.  |  Bytes/Second  | 
| NetworkTransmitThroughput |   **Network Transmit Throughput \(MB/Second\)**   |  The outgoing \(transmit\) network traffic on the DB instance, including both customer database traffic and Amazon RDS traffic used for monitoring and replication\.  |  Bytes/Second  | 
| OldestReplicationSlotLag |   **Oldest Replication Slot Lag \(MB\)**   |  The lagging size of the replica lagging the most in terms of write\-ahead log \(WAL\) data received\. Applies to PostgreSQL\.  |  Bytes  | 
| ReadIOPS |   **Read IOPS \(Count/Second\)**   |  The average number of disk read I/O operations per second\.  |  Count/Second  | 
| ReadLatency |   **Read Latency \(Milliseconds\)**   |  The average amount of time taken per disk I/O operation\.  |  Seconds  | 
| ReadThroughput |   **Read Throughput \(MB/Second\)**   |  The average number of bytes read from disk per second\.  |  Bytes/Second  | 
| ReplicaLag |   **Replica Lag \(Milliseconds\)**   |  The amount of time a read replica DB instance lags behind the source DB instance\. Applies to MySQL, MariaDB, Oracle, PostgreSQL, and SQL Server read replicas\.  |  Seconds  | 
| ReplicationSlotDiskUsage |   **Replica Slot Disk Usage \(MB\)**   |  The disk space used by replication slot files\. Applies to PostgreSQL\.  |  Bytes  | 
| SwapUsage |   **Swap Usage \(MB\)**   |  The amount of swap space used on the DB instance\. This metric is not available for SQL Server\.  |  Bytes  | 
| TransactionLogsDiskUsage |   **Transaction Logs Disk Usage \(MB\)**   |  The disk space used by transaction logs\. Applies to PostgreSQL\.  |  Bytes  | 
| TransactionLogsGeneration |   **Transaction Logs Generation \(MB/Second\)**   |  The size of transaction logs generated per second\. Applies to PostgreSQL\.  |  Bytes/Second  | 
| WriteIOPS |   **Write IOPS \(Count/Second\)**   |  The average number of disk write I/O operations per second\.  |  Count/Second  | 
| WriteLatency |   **Write Latency \(Milliseconds\)**   |  The average amount of time taken per disk I/O operation\.  |  Seconds  | 
| WriteThroughput |   **Write Throughput \(MB/Second\)**   |  The average number of bytes written to disk per second\.  |  Bytes/Second  | 

## Amazon RDS dimensions<a name="dimensions"></a>

You can filter Amazon RDS metrics data by using any dimension in the following table\.


|  Dimension  |  Filters the requested data for \. \. \.  | 
| --- | --- | 
|  DBInstanceIdentifier  |  A specific DB instance\.  | 
|  DatabaseClass  |  All instances in a database class\. For example, you can aggregate metrics for all instances that belong to the database class `db.r5.large`\.  | 
|  EngineName  |  The identified engine name only\. For example, you can aggregate metrics for all instances that have the engine name `mysql`\.  | 
|  SourceRegion  |  The specified Region only\. For example, you can aggregate metrics for all DB instances in the `us-east-1` Region\.  | 

## Viewing Amazon RDS metrics and dimensions<a name="metrics_dimensions"></a>

When you use Amazon RDS resources, Amazon RDS sends metrics and dimensions to Amazon CloudWatch every minute\. You can use the following procedures to view the metrics for Amazon RDS\.

### Console<a name="metrics_dimensions.console"></a>

**To view metrics using the Amazon CloudWatch console**

Metrics are grouped first by the service namespace, and then by the various dimension combinations within each namespace\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. If necessary, change the AWS Region\. From the navigation bar, choose the AWS Region where your AWS resources are\. For more information, see [Regions and endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)\.

1. In the navigation pane, choose **Metrics**\. Choose the **RDS** metric namespace\.  
![\[Choose metric namespace\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds-monitoring-01.png)

1. Choose a metric dimension, for example **By Database Class**\.

1. To sort the metrics, use the column heading\. To graph a metric, select the check box next to the metric\. To filter by resource, choose the resource ID, and then choose **Add to search**\. To filter by metric, choose the metric name, and then choose **Add to search**\.  
![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds-monitoring-03.png)

### AWS CLI<a name="metrics_dimensions.CLI"></a>

To obtain metric information by using the AWS CLI, use the CloudWatch command [https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html)\. In the following example, you list all metrics in the `AWS/RDS` namespace\.

```
aws cloudwatch list-metrics --namespace AWS/RDS
```

To obtain metric statistics, use the command [https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/get-metric-statistics.html](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/get-metric-statistics.html)\. The following command gets `CPUUtilization` statistics for instance `my-instance` over the specific 24\-hour period, with a 5\-minute granularity\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws cloudwatch get-metric-statistics --namespace AWS/RDS \
2.      --metric-name CPUUtilization \
3.      --start-time 2021-12-15T00:00:00Z \
4.      --end-time 2021-12-16T00:00:00Z \
5.      --period 360 \
6.      --statistics Minimum \
7.      --dimensions Name=DBInstanceIdentifier,Value=my-instance
```
For Windows:  

```
1. aws cloudwatch get-metric-statistics --namespace AWS/RDS ^
2.      --metric-name CPUUtilization ^
3.      --start-time 2021-12-15T00:00:00Z ^
4.      --end-time 2021-12-16T00:00:00Z ^
5.      --period 360 ^
6.      --statistics Minimum ^
7.      --dimensions Name=DBInstanceIdentifier,Value=my-instance
```
Sample output appears as follows:  

```
{
    "Datapoints": [
        {
            "Timestamp": "2021-12-15T18:00:00Z", 
            "Minimum": 8.7, 
            "Unit": "Percent"
        }, 
        {
            "Timestamp": "2021-12-15T23:54:00Z", 
            "Minimum": 8.12486458559024, 
            "Unit": "Percent"
        }, 
        {
            "Timestamp": "2021-12-15T17:24:00Z", 
            "Minimum": 8.841666666666667, 
            "Unit": "Percent"
        }, ...
        {
            "Timestamp": "2021-12-15T22:48:00Z", 
            "Minimum": 8.366248354248954, 
            "Unit": "Percent"
        }
    ], 
    "Label": "CPUUtilization"
}
```
For more information, see [Getting statistics for a metric](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/getting-metric-statistics.html) in the *Amazon CloudWatch User Guide*\.

## Creating CloudWatch alarms to monitor Amazon RDS<a name="creating_alarms"></a>

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
+ Call [https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html)\. For more information, see *[AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/)*\.

**To set an alarm using the CloudWatch API**
+ Call [https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_PutMetricAlarm.html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_PutMetricAlarm.html)\. For more information, see *[Amazon CloudWatch API Reference](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/)* 