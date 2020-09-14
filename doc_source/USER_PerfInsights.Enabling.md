# Enabling and Disabling Performance Insights<a name="USER_PerfInsights.Enabling"></a>

To use Performance Insights, enable it on your DB instance\. If needed, you can disable it later\. Enabling and disabling Performance Insights doesn't cause downtime, a reboot, or a failover\.

The Performance Insights agent consumes limited CPU and memory on the DB host\. When the DB load is high, the agent limits the performance impact by collecting data less frequently\.

## Console<a name="USER_PerfInsights.Enabling.Console"></a>

In the console, you can enable or disable Performance Insights when you create or modify a new DB instance\.

### Enabling or Disabling Performance Insights When Creating an Instance<a name="USER_PerfInsights.Console.Creating"></a>

When you create a new DB instance, enable Performance Insights by choosing **Enable Performance Insights** in the **Performance Insights** section\. Or choose **Disable Performance Insights**\.

To create a DB instance, follow the instructions for your DB engine in [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.

The following screenshot shows the **Performance Insights** section\.

![\[Enable Performance Insights during DB instance creation with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_enabling.png)

If you choose **Enable Performance Insights**, you have the following options:
+ **Retention** – The amount of time to retain Performance Insights data\. Choose either 7 days \(the default\) or 2 years\.
+ **Master key** – Specify your AWS Key Management Service \(AWS KMS\) customer master key \(CMK\)\. Performance Insights encrypts all potentially sensitive data using your AWS KMS CMK\. Data is encrypted in flight and at rest\. For more information, see [Encrypting Amazon RDS Resources](Overview.Encryption.md)\.

### Enabling or Disabling Performance Insights When Modifying an Instance<a name="USER_PerfInsights.Enabling.Console.Modifying"></a>

In the console, you can modify a DB instance to enable or disable Performance Insights using the console\.

**To enable or disable Performance Insights for a DB instance using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Databases**\.

1. Choose a DB instance, and choose **Modify**\.

1. In the **Performance Insights** section, choose either **Enable Performance Insights** or **Disable Performance Insights**\.

   If you choose **Enable Performance Insights**, you have the following options:
   + **Retention** – The amount of time to retain Performance Insights data\. Choose either 7 days \(the default\) or 2 years\.
   + **Master key** – Specify your AWS Key Management Service \(AWS KMS\) customer master key \(CMK\)\. Performance Insights encrypts all potentially sensitive data using your AWS KMS CMK\. Data is encrypted in flight and at rest\. For more information, see [Encrypting Amazon RDS Resources](Overview.Encryption.md)\.

1. Choose **Continue**\.

1. For **Scheduling of Modifications**, choose one of the following:
   + **Apply during the next scheduled maintenance window** – Wait to apply the **Performance Insights** modification until the next maintenance window\.
   + **Apply immediately** – Apply the **Performance Insights** modification as soon as possible\.

1. Choose **Modify instance**\.

## AWS CLI<a name="USER_PerfInsights.Enabling.CLI"></a>

When you use the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command, enable Performance Insights by specifying `--enable-performance-insights`\. Or disable Performance Insights by specifying `--no-enable-performance-insights`\.

You can also specify these values using the following AWS CLI commands:
+  [create\-db\-instance\-read\-replica](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) 
+  [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) 
+  [restore\-db\-instance\-from\-s3](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-s3.html) 

The following procedure describes how to enable or disable Performance Insights for a DB instance using the AWS CLI\.

**To enable or disable Performance Insights for a DB instance using the AWS CLI**
+ Call the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) AWS CLI command and supply the following values:
  + `--db-instance-identifier` – The name of the DB instance\.
  + `--enable-performance-insights` to enable or `--no-enable-performance-insights` to disable

  The following example enables Performance Insights for `sample-db-instance`\.

  For Linux, macOS, or Unix:

  ```
  aws rds modify-db-instance \
      --db-instance-identifier sample-db-instance \
      --enable-performance-insights
  ```

  For Windows:

  ```
  aws rds modify-db-instance ^
      --db-instance-identifier sample-db-instance ^
      --enable-performance-insights
  ```

When you enable Performance Insights, you can optionally specify the amount of time, in days, to retain Performance Insights data with the `--performance-insights-retention-period` option\. Valid values are 7 \(the default\) or 731 \(2 years\)\.

The following example enables Performance Insights for `sample-db-instance` and specifies that Performance Insights data is retained for two years\.

For Linux, macOS, or Unix:

```
aws rds modify-db-instance \
    --db-instance-identifier sample-db-instance \
    --enable-performance-insights \
    --performance-insights-retention-period 731
```

For Windows:

```
aws rds modify-db-instance ^
    --db-instance-identifier sample-db-instance ^
    --enable-performance-insights ^
    --performance-insights-retention-period 731
```

## RDS API<a name="USER_PerfInsights.Enabling.API"></a>

When you create a new DB instance using the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) operation Amazon RDS API operation, enable Performance Insights by setting `EnablePerformanceInsights` to `True`\. To disable Performance Insights, set `EnablePerformanceInsights` to `False`\.

You can also specify the `EnablePerformanceInsights` value using the following API operations:
+  [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) 
+  [CreateDBInstanceReadReplica](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html) 
+  [RestoreDBInstanceFromS3](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromS3.html) 

When you enable Performance Insights, you can optionally specify the amount of time, in days, to retain Performance Insights data with the `PerformanceInsightsRetentionPeriod` parameter\. Valid values are 7 \(the default\) or 731 \(2 years\)\.

## Enabling the Performance Schema for Performance Insights on Amazon RDS for MariaDB or MySQL<a name="USER_PerfInsights.EnableMySQL"></a>

When the Performance Schema feature is enabled for Amazon RDS for MariaDB or MySQL, Performance Insights provides more detailed information\. For example, Performance Insights displays DB load categorized by detailed wait events\. Without the Performance Schema enabled, Performance Insights displays DB load categorized by the list state of the MySQL process\.

Performance Schema is enabled automatically when you create an Amazon RDS for MariaDB or MySQL DB instance with Performance Insights enabled\. In this case, Performance Insights automatically manages the parameters in the following table\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PerfInsights.Enabling.html)

**Important**  
When Performance Schema is enabled automatically, Performance Insights changes schema\-related parameters on the DB instance\. These changes aren't visible in the parameter group associated with the DB instance\.

For more information, see [Performance Schema Command Options](https://dev.mysql.com/doc/refman/5.6/en/performance-schema-options.html#option_mysqld_performance-schema-consumer-events-stages-current) and [Performance Schema Option and Variable Reference](https://dev.mysql.com/doc/refman/8.0/en/performance-schema-option-variable-reference.html) in the MySQL documentation\.

### Enabling the Performance Schema Manually<a name="USER_PerfInsights.EnableMySQL.RDS"></a>

Performance Schema is *not* enabled when both the following conditions are true:
+ The `performance_schema` parameter is set to `0` or `1`\.
+ The `performance_schema` parameter `source` is set to `user`\.

**To enable the Performance Schema manually**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Parameter groups**\.

1. Select the parameter group for your DB instance\.

1. Select the `performance_schema` parameter\.

1. Select **Edit parameters**\.

1. Select **Reset**\.

1. Select **Reset parameters**\.

1. Restart the DB instance\.

For more information about modifying instance parameters, see [Modifying Parameters in a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\. For more information about the dashboard, see [Monitoring with the Performance Insights Dashboard](USER_PerfInsights.UsingDashboard.md)\. For more information about the MySQL performance schema, see [MySQL 8\.0 Reference Manual](https://dev.mysql.com/doc/refman/8.0/en/performance-schema.html)\.