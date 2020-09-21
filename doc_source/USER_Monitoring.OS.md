# Enhanced Monitoring<a name="USER_Monitoring.OS"></a>

Amazon RDS provides metrics in real time for the operating system \(OS\) that your DB instance runs on\. You can view the metrics for your DB instance using the console\. Also, you can consume the Enhanced Monitoring JSON output from Amazon CloudWatch Logs in a monitoring system of your choice\.

By default, Enhanced Monitoring metrics are stored for 30 days in the CloudWatch Logs, which are different from typical CloudWatch metrics\. To modify the amount of time the metrics are stored in the CloudWatch Logs, change the retention for the `RDSOSMetrics` log group in the CloudWatch console\. For more information, see [Change Log Data Retention in CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html#SettingLogRetention) in the *Amazon CloudWatch Logs User Guide*\.

Because Enhanced Monitoring metrics are stored in the CloudWatch logs instead of in Cloudwatch metrics, the cost of Enhanced Monitoring depends on several factors:
+ You are only charged for Enhanced Monitoring that exceeds the free tier provided by Amazon CloudWatch Logs\. 

  For more information about pricing, see [Amazon CloudWatch Pricing](https://aws.amazon.com/cloudwatch/pricing/)\. 
+ A smaller monitoring interval results in more frequent reporting of OS metrics and increases your monitoring cost\. 
+ Usage costs for Enhanced Monitoring are applied for each DB instance that Enhanced Monitoring is enabled for\. Monitoring a large number of DB instances is more expensive than monitoring only a few\.
+ DB instances that support a more compute\-intensive workload have more OS process activity to report and higher costs for Enhanced Monitoring\.

## Enhanced Monitoring Availability<a name="USER_Monitoring.OS.Availability"></a>

Enhanced Monitoring is available for the following database engines:
+ MariaDB
+ Microsoft SQL Server
+ MySQL version 5\.5 or later
+ Oracle
+ PostgreSQL

Enhanced Monitoring is available for all DB instance classes except for db\.m1\.small, all db\.m6g instance classes, and all db\.r6g instance classes\. 

## Differences Between CloudWatch and Enhanced Monitoring Metrics<a name="USER_Monitoring.OS.CloudWatchComparison"></a>

CloudWatch gathers metrics about CPU utilization from the hypervisor for a DB instance, and Enhanced Monitoring gathers its metrics from an agent on the instance\. As a result, you might find differences between the measurements, because the hypervisor layer performs a small amount of work\. The differences can be greater if your DB instances use smaller instance classes, because then there are likely more virtual machines \(VMs\) that are managed by the hypervisor layer on a single physical instance\. Enhanced Monitoring metrics are useful when you want to see how different processes or threads on a DB instance use the CPU\.

## Setting Up for and Enabling Enhanced Monitoring<a name="USER_Monitoring.OS.Enabling"></a>

To set up for and enable Enhanced Monitoring, take the steps listed following\.

### Before You Begin<a name="USER_Monitoring.OS.Enabling.Prerequisites"></a>

Enhanced Monitoring requires permission to act on your behalf to send OS metric information to CloudWatch Logs\. You grant Enhanced Monitoring the required permissions using an AWS Identity and Access Management \(IAM\) role\. 

The first time that you enable Enhanced Monitoring in the console, you can select the **Default** option for the **Monitoring Role** property to have RDS create the required IAM role\. RDS then automatically creates a role named `rds-monitoring-role` for you, and uses it for the specified DB instance or read replica\.

You can also create the required role before you enable Enhanced Monitoring, and then specify your new role's name when you enable Enhanced Monitoring\. You must create this required role if you enable Enhanced Monitoring using the AWS CLI or the RDS API\.

To create the appropriate IAM role to permit Amazon RDS to communicate with the Amazon CloudWatch Logs service on your behalf, take the following steps\.

The user that enables Enhanced Monitoring must be granted the `PassRole` permission\. For more information, see Example 2 in [Granting a User Permissions to Pass a Role to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_passrole.html) in the *IAM User Guide*\.<a name="USER_Monitoring.OS.IAMRole"></a>

**To create an IAM role for Amazon RDS Enhanced Monitoring**

1. Open the [IAM Console](https://console.aws.amazon.com/iam/home?#home) at [https://console\.aws\.amazon\.com](https://console.aws.amazon.com/)\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. Choose the **AWS service** tab, and then choose **RDS** from the list of services\.

1. Choose **RDS \- Enhanced Monitoring**, and then choose **Next: Permissions**\.

1. Ensure that the **Attached permissions policy** page shows **AmazonRDSEnhancedMonitoringRole**, and then choose **Next: Tags**\.

1. On the **Add tags** page, choose **Next: Review**\.

1. For **Role Name**, enter a name for your role, for example **emaccess**, and then choose **Create role**\.

### Enabling and Disabling Enhanced Monitoring<a name="USER_Monitoring.OS.Enabling.Procedure"></a>

You can enable and disable Enhanced Monitoring using the AWS Management Console, AWS CLI, or RDS API\.

#### Console<a name="USER_Monitoring.OS.Enabling.Procedure.Console"></a>

You can enable Enhanced Monitoring when you create a DB instance or read replica, or when you modify a DB instance\. If you modify a DB instance to enable Enhanced Monitoring, you don't need to reboot your DB instance for the change to take effect\. 

You can enable Enhanced Monitoring in the RDS console when you do one of the following actions: 
+ **Create a DB instance** – You can enable Enhanced Monitoring in the **Monitoring** section under **Additional configuration**\.
+ **Create a read replica** – You can enable Enhanced Monitoring in the **Monitoring** section\.
+ **Modify a DB instance** – You can enable Enhanced Monitoring in the **Monitoring** section\.

To enable Enhanced Monitoring by using the RDS console, scroll to the **Monitoring** section and do the following: 

1. Choose **Enable enhanced monitoring** for your DB instance or read replica\.

1. Set the **Monitoring Role** property to the IAM role that you created to permit Amazon RDS to communicate with Amazon CloudWatch Logs for you, or choose **Default** to have RDS create a role for you named `rds-monitoring-role`\.

1. Set the **Granularity** property to the interval, in seconds, between points when metrics are collected for your DB instance or read replica\. The **Granularity** property can be set to one of the following values: `1`, `5`, `10`, `15`, `30`, or `60`\.

To disable Enhanced Monitoring, choose **Disable enhanced monitoring**\.

![\[Enable Enhanced Monitoring\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/metrics3.png)

Enabling Enhanced Monitoring doesn't require your DB instance to restart\.

**Note**  
The fastest that the RDS console refreshes is every 5 seconds\. If you set the granularity to 1 second in the RDS console, you still see updated metrics only every 5 seconds\. You can retrieve 1\-second metric updates by using CloudWatch Logs\.

#### AWS CLI<a name="USER_Monitoring.OS.Enabling.Procedure.CLI"></a>

To enable Enhanced Monitoring using the AWS CLI, in the following commands, set the `--monitoring-interval` option to a value other than `0` and set the `--monitoring-role-arn` option to the role you created in [Before You Begin](#USER_Monitoring.OS.Enabling.Prerequisites)\.
+ [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)
+ [create\-db\-instance\-read\-replica](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html)
+ [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)

The `--monitoring-interval` option specifies the interval, in seconds, between points when Enhanced Monitoring metrics are collected\. Valid values for the option are `0`, `1`, `5`, `10`, `15`, `30`, and `60`\.

To disable Enhanced Monitoring using the AWS CLI, set the `--monitoring-interval` option to `0` in the these commands\.

**Example**  
The following example enables Enhanced Monitoring for a DB instance:  
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --monitoring-interval 30 \
    --monitoring-role-arn arn:aws:iam::123456789012:role/emaccess
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier mydbinstance ^
    --monitoring-interval 30 ^
    --monitoring-role-arn arn:aws:iam::123456789012:role/emaccess
```

#### RDS API<a name="USER_Monitoring.OS.Enabling.Procedure.API"></a>

To enable Enhanced Monitoring using the RDS API, in the following operations, set the `MonitoringInterval` parameter to a value other than `0` and set the `MonitoringRoleArn` parameter to the role you created in [Before You Begin](#USER_Monitoring.OS.Enabling.Prerequisites)\.
+ [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html)
+ [CreateDBInstanceReadReplica](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)
+ [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html)

The `MonitoringInterval` parameter specifies the interval, in seconds, between points when Enhanced Monitoring metrics are collected\. Valid values for the parameter are `0`, `1`, `5`, `10`, `15`, `30`, and `60`\.

To disable Enhanced Monitoring using the RDS API, set the `MonitoringInterval` parameter to `0` in the these operations\.

## Viewing Enhanced Monitoring<a name="USER_Monitoring.OS.Viewing"></a>

You can view OS metrics reported by Enhanced Monitoring in the RDS console by choosing **Enhanced monitoring** for **Monitoring**\.

The Enhanced Monitoring page is shown following\.

![\[Dashboard view\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/metrics1.png)

Some DB instances use more than one disk for the DB instance's data storage volume\. On those DB instances, the **Physical Devices** graphs show metrics for each one of the disks\. For example, the following graph shows metrics for four disks\.

![\[Graph with multiple disks\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/enhanced-monitoring-multiple-disks.png)

**Note**  
Currently, **Physical Devices** graphs are not available for Microsoft SQL Server DB instances\.

When you are viewing aggregated **Disk I/O** and **File system** graphs, the **rdsdev** device relates to the `/rdsdbdata` file system, where all database files and logs are stored\. The **filesystem** device relates to the `/` file system \(also known as root\), where files related to the operating system are stored\.

![\[Graph showing file system usage\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/enhanced-monitoring-filesystem.png)

If the DB instance is a Multi\-AZ deployment, you can view the OS metrics for the primary DB instance and its Multi\-AZ standby replica\. In the **Enhanced monitoring** view, choose **primary** to view the OS metrics for the primary DB instance, or choose **secondary** to view the OS metrics for the standby replica\.

![\[Primary and secondary choice for Enhanced Monitoring\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/enhanced-monitoring-primary-secondary.png)

For more information about Multi\-AZ deployments, see [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)\.

**Note**  
Currently, viewing OS metrics for a Multi\-AZ standby replica is not supported for MariaDB or Microsoft SQL Server DB instances\.

If you want to see details for the processes running on your DB instance, choose **OS process list** for **Monitoring**\.

The **Process List** view is shown following\.

![\[Process list view\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/metrics2.png)

The Enhanced Monitoring metrics shown in the **Process list** view are organized as follows:
+ **RDS child processes** – Shows a summary of the RDS processes that support the DB instance, for example `mysqld` for MySQL DB instances\. Process threads appear nested beneath the parent process\. Process threads show CPU utilization only as other metrics are the same for all threads for the process\. The console displays a maximum of 100 processes and threads\. The results are a combination of the top CPU consuming and memory consuming processes and threads\. If there are more than 50 processes and more than 50 threads, the console displays the top 50 consumers in each category\. This display helps you identify which processes are having the greatest impact on performance\.
+ **RDS processes** – Shows a summary of the resources used by the RDS management agent, diagnostics monitoring processes, and other AWS processes that are required to support RDS DB instances\.
+ **OS processes** – Shows a summary of the kernel and system processes, which generally have minimal impact on performance\.

The items listed for each process are:
+ **VIRT** – Displays the virtual size of the process\.
+ **RES** – Displays the actual physical memory being used by the process\.
+ **CPU%** – Displays the percentage of the total CPU bandwidth being used by the process\.
+ **MEM%** – Displays the percentage of the total memory being used by the process\.

The monitoring data that is shown in the RDS console is retrieved from Amazon CloudWatch Logs\. You can also retrieve the metrics for a DB instance as a log stream from CloudWatch Logs\. For more information, see [Viewing Enhanced Monitoring by Using CloudWatch Logs](#USER_Monitoring.OS.CloudWatchLogs)\.

Enhanced Monitoring metrics are not returned during the following: 
+ A failover of the DB instance\.
+ Changing the instance class of the DB instance \(scale compute\)\.

Enhanced Monitoring metrics are returned during a reboot of a DB instance because only the database engine is rebooted\. Metrics for the operating system are still reported\.

## Viewing Enhanced Monitoring by Using CloudWatch Logs<a name="USER_Monitoring.OS.CloudWatchLogs"></a>

After you have enabled Enhanced Monitoring for your DB instance, you can view the metrics for your DB instance using CloudWatch Logs, with each log stream representing a single DB instance being monitored\. The log stream identifier is the resource identifier \(`DbiResourceId`\) for the DB instance\.

**To view Enhanced Monitoring log data**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. If necessary, choose the region that your DB instance is in\. For more information, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/index.html?rande.html) in the *Amazon Web Services General Reference*\.

1. Choose **Logs** in the navigation pane\.

1. Choose **RDSOSMetrics** from the list of log groups\.

   In a Multi\-AZ deployment, log files with `-secondary` appended to the name are for the Multi\-AZ standby replica\.  
![\[Multi-AZ standby replica log file\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/enhanced-monitoring-cloudwatch-secondary.png)

1. Choose the log stream that you want to view from the list of log streams\.

### Available OS Metrics<a name="USER_Monitoring-Available-OS-Metrics"></a>

The following tables list the OS metrics available using Amazon CloudWatch Logs\.

#### Metrics for MariaDB, MySQL, Oracle, and PostgreSQL DB instances<a name="w121aac21c17c21b7b5"></a>

<a name="cloudwatch-os-metrics"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Monitoring.OS.html)

#### Metrics for Microsoft SQL Server DB instances<a name="w121aac21c17c21b7b9"></a>

<a name="cloudwatch-sql-server-metrics"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Monitoring.OS.html)