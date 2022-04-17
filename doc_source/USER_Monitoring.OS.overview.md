# Overview of Enhanced Monitoring<a name="USER_Monitoring.OS.overview"></a>

Amazon RDS provides metrics in real time for the operating system \(OS\) that your DB instance runs on\. You can view all the system metrics and process information for your RDS DB instances on the console\. You can manage which metrics you want to monitor for each instance and customize the dashboard according to your requirements\. For descriptions of the Enhanced Monitoring metrics, see [OS metrics in Enhanced Monitoring](USER_Monitoring-Available-OS-Metrics.md)\.

RDS delivers the metrics from Enhanced Monitoring into your Amazon CloudWatch Logs account\. You can create metrics filters in CloudWatch from CloudWatch Logs and display the graphs on the CloudWatch dashboard\. You can consume the Enhanced Monitoring JSON output from CloudWatch Logs in a monitoring system of your choice\. For more information, see [Enhanced Monitoring](https://aws.amazon.com/rds/faqs/#Enhanced_Monitoring) in the Amazon RDS FAQs\.

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

For descriptions of the Enhanced Monitoring metrics, see [OS metrics in Enhanced Monitoring](USER_Monitoring-Available-OS-Metrics.md)\. For more information about CloudWatch metrics, see the *[Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html)*\.

## Retention of Enhanced Monitoring metrics<a name="USER_Monitoring.OS.retention"></a>

By default, Enhanced Monitoring metrics are stored for 30 days in the CloudWatch Logs\. This retention period is different from typical CloudWatch metrics\.

To modify the amount of time the metrics are stored in the CloudWatch Logs, change the retention for the `RDSOSMetrics` log group in the CloudWatch console\. For more information, see [Change log data retention in CloudWatch logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html#SettingLogRetention) in the *Amazon CloudWatch Logs User Guide*\.

## Cost of Enhanced Monitoring<a name="USER_Monitoring.OS.cost"></a>

Enhanced Monitoring metrics are stored in the CloudWatch Logs instead of in CloudWatch metrics\. The cost of Enhanced Monitoring depends on the following factors:
+ You are charged for Enhanced Monitoring only if you exceed the free tier provided by Amazon CloudWatch Logs\. Charges are based on CloudWatch Logs data transfer and storage rates\.
+ The amount of information transferred for an RDS instance is directly proportional to the defined granularity for the Enhanced Monitoring feature\. A smaller monitoring interval results in more frequent reporting of OS metrics and increases your monitoring cost\. To manage costs, set different granularities for different instances in your accounts\.
+ Usage costs for Enhanced Monitoring are applied for each DB instance that Enhanced Monitoring is enabled for\. Monitoring a large number of DB instances is more expensive than monitoring only a few\.
+ DB instances that support a more compute\-intensive workload have more OS process activity to report and higher costs for Enhanced Monitoring\.

For more information about pricing, see [Amazon CloudWatch pricing](https://aws.amazon.com/cloudwatch/pricing/)\.