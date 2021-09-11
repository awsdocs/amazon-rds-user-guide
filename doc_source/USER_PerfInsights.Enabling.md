# Enabling and disabling Performance Insights<a name="USER_PerfInsights.Enabling"></a>

To use Performance Insights, enable it on your DB instance\. If needed, you can disable it later\. Enabling and disabling Performance Insights doesn't cause downtime, a reboot, or a failover\.

**Note**  
Performance Schema is an optional performance tool used by Amazon RDS for MariaDB or MySQL\. If you turn Performance Schema on or off, you need to reboot\. If you turn Performance Insights on or off, however, you don't need to reboot\.

The Performance Insights agent consumes limited CPU and memory on the DB host\. When the DB load is high, the agent limits the performance impact by collecting data less frequently\.



## Console<a name="USER_PerfInsights.Enabling.Console"></a>

In the console, you can enable or disable Performance Insights when you create or modify a new DB instance\.

### Enabling or disabling Performance Insights when creating an instance<a name="USER_PerfInsights.Console.Creating"></a>

When you create a new DB instance, enable Performance Insights by choosing **Enable Performance Insights** in the **Performance Insights** section\. Or choose **Disable Performance Insights**\.

To create a DB instance, follow the instructions for your DB engine in [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.

The following screenshot shows the **Performance Insights** section\.

![\[Enable Performance Insights during DB instance creation with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_enabling.png)

If you choose **Enable Performance Insights**, you have the following options:
+ **Retention** – The amount of time to retain Performance Insights data\. Choose either 7 days \(the default\) or 2 years\.
+ **AWS KMS key** – Specify your AWS KMS key\. Performance Insights encrypts all potentially sensitive data using your KMS key\. Data is encrypted in flight and at rest\. For more information, see [Configuring an AWS KMS policy for Performance Insights](USER_PerfInsights.access-control.md#USER_PerfInsights.access-control.cmk-policy)\.

### Enabling or disabling Performance Insights when modifying an instance<a name="USER_PerfInsights.Enabling.Console.Modifying"></a>

In the console, you can modify a DB instance to enable or disable Performance Insights using the console\.

**To enable or disable Performance Insights for a DB instance using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Databases**\.

1. Choose a DB instance, and choose **Modify**\.

1. In the **Performance Insights** section, choose either **Enable Performance Insights** or **Disable Performance Insights**\.

   If you choose **Enable Performance Insights**, you have the following options:
   + **Retention** – The amount of time to retain Performance Insights data\. Choose either 7 days \(the default\) or 2 years\.
   + **AWS KMS key** – Specify your KMS key\. Performance Insights encrypts all potentially sensitive data using your KMS key\. Data is encrypted in flight and at rest\. For more information, see [Encrypting Amazon RDS resources](Overview.Encryption.md)\.

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

   [CreateDBInstanceReadReplica](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html) 
+  [RestoreDBInstanceFromS3](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromS3.html) 

When you enable Performance Insights, you can optionally specify the amount of time, in days, to retain Performance Insights data with the `PerformanceInsightsRetentionPeriod` parameter\. Valid values are 7 \(the default\) or 731 \(2 years\)\.