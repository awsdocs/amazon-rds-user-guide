# Overview of Enhanced Monitoring<a name="USER_Monitoring.OS.overview"></a>

Amazon RDS provides metrics in real time for the operating system \(OS\) that your DB instance runs on\. You can view the metrics for your DB instance using the console\. Also, you can consume the Enhanced Monitoring JSON output from Amazon CloudWatch Logs in a monitoring system of your choice\.

**Topics**
+ [Enhanced Monitoring availability](#USER_Monitoring.OS.Availability)
+ [Differences between CloudWatch and Enhanced Monitoring metrics](#USER_Monitoring.OS.CloudWatchComparison)
+ [Retention of Enhanced Monitoring metrics](#USER_Monitoring.OS.retention)
+ [Cost of Enhanced Monitoring](#USER_Monitoring.OS.cost)

## Enhanced Monitoring availability<a name="USER_Monitoring.OS.Availability"></a>

Enhanced Monitoring is available for the following database engines:
+ MariaDB
+ Microsoft SQL Server
+ MySQL
+ Oracle
+ PostgreSQL

Enhanced Monitoring is available for all DB instance classes except for the db\.m1\.small instance class\. 

## Differences between CloudWatch and Enhanced Monitoring metrics<a name="USER_Monitoring.OS.CloudWatchComparison"></a>

A *hypervisor* creates and runs virtual machines \(VMs\)\. Using a hypervisor, an instance can support multiple guest VMs by virtually sharing memory and CPU\. CloudWatch gathers metrics about CPU utilization from the hypervisor for a DB instance\. In contrast, Enhanced Monitoring gathers its metrics from an agent on the DB instance\.

You might find differences between the CloudWatch and Enhanced Monitoring measurements, because the hypervisor layer performs a small amount of work\. The differences can be greater if your DB instances use smaller instance classes\. In this scenario, more virtual machines \(VMs\) are probably managed by the hypervisor layer on a single physical instance\.

## Retention of Enhanced Monitoring metrics<a name="USER_Monitoring.OS.retention"></a>

By default, Enhanced Monitoring metrics are stored for 30 days in the CloudWatch Logs\. This retention period is different from typical CloudWatch metrics\.

To modify the amount of time the metrics are stored in the CloudWatch Logs, change the retention for the `RDSOSMetrics` log group in the CloudWatch console\. For more information, see [Change log data retention in CloudWatch logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html#SettingLogRetention) in the *Amazon CloudWatch Logs User Guide*\.

## Cost of Enhanced Monitoring<a name="USER_Monitoring.OS.cost"></a>

Enhanced Monitoring metrics are stored in the CloudWatch logs instead of in Cloudwatch metrics\. For this reason, the cost of Enhanced Monitoring depends on several factors:
+ You are only charged for Enhanced Monitoring that exceeds the free tier provided by Amazon CloudWatch Logs\. 
+ A smaller monitoring interval results in more frequent reporting of OS metrics and increases your monitoring cost\. 
+ Usage costs for Enhanced Monitoring are applied for each DB instance that Enhanced Monitoring is enabled for\. Monitoring a large number of DB instances is more expensive than monitoring only a few\.
+ DB instances that support a more compute\-intensive workload have more OS process activity to report and higher costs for Enhanced Monitoring\.

For more information about pricing, see [Amazon CloudWatch pricing](https://aws.amazon.com/cloudwatch/pricing/)\.