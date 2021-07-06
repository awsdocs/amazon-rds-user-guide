# Enabling the Performance Schema for Performance Insights on Amazon RDS for MariaDB or MySQL<a name="USER_PerfInsights.EnableMySQL"></a>

The Performance Schema is an optional feature for monitoring Amazon RDS for MariaDB or MySQL runtime performance at a low level\. The Performance Schema is designed to have minimal impact on database performance\.

**Topics**
+ [Overview of the Performance Schema](#USER_PerfInsights.EnableMySQL.overview)
+ [Options for enabling Performance Schema](#USER_PerfInsights.EnableMySQL.options)
+ [Enabling the Performance Schema manually](#USER_PerfInsights.EnableMySQL.RDS)

## Overview of the Performance Schema<a name="USER_PerfInsights.EnableMySQL.overview"></a>

The Performance Schema monitors server events\. In this context, an event is a server action that consumes time\. Performance Schema events are distinct from binlog events and scheduler events\. The `PERFORMANCE_SCHEMA` storage engine collects event data using instrumentation in the database source code\. Collected events are stored in tables in the `performance_schema` database, which can be queried like any other tables\. For more information, see [MySQL Performance Schema](https://dev.mysql.com/doc/refman/8.0/en/performance-schema.html) in *MySQL Reference Manual*\.

When the Performance Schema is enabled for Amazon RDS for MariaDB or MySQL, Performance Insights uses it to provide more detailed information\. For example, Performance Insights displays DB load categorized by detailed wait events\. You can use wait events to identify bottlenecks\. Without the Performance Schema, Performance Insights reports user states such as inserting and sending, which don't help you identify bottlenecks\.

## Options for enabling Performance Schema<a name="USER_PerfInsights.EnableMySQL.options"></a>

You have the following options for enabling the Performance Schema:
+ Allow Performance Insights to manage required parameters automatically\.

  When you create an Amazon RDS for MariaDB or MySQL DB instance with Performance Insights enabled, the Performance Schema is enabled automatically\. In this case, Performance Insights automatically manages your parameters\.
**Important**  
In this scenario, Performance Insights changes schema\-related parameters on the DB instance\. These changes aren't visible in the parameter group associated with the DB instance\. However, these changes are visible in the output of the `SHOW GLOBAL VARIABLES` command\.
+ Set the required parameters yourself\.

  For Performance Insights to list wait events, set all parameters as shown in the following table\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PerfInsights.EnableMySQL.html)

**Note**  
Enabling or disabling the Performance Schema requires a reboot\.

For more information, see [Performance Schema Command Options](https://dev.mysql.com/doc/refman/5.6/en/performance-schema-options.html#option_mysqld_performance-schema-consumer-events-stages-current) and [Performance Schema Option and Variable Reference](https://dev.mysql.com/doc/refman/8.0/en/performance-schema-option-variable-reference.html) in the MySQL documentation\.

## Enabling the Performance Schema manually<a name="USER_PerfInsights.EnableMySQL.RDS"></a>

Performance Schema is *not* enabled when both the following conditions are true:
+ The `performance_schema` parameter is set to `0` or `1`\.
+ The **Source** column for the `performance_schema` parameter is set to `user`\.

**To enable the Performance Schema manually**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Parameter groups**\.

1. Select the name of the parameter group for your DB instance\.

1. Choose **Edit parameters**\.

1. Enter **perf** in the search bar\.

1. Select the `performance_schema` parameter\.  
![\[Select performance_schema\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_schema.png)

1. Choose **Reset**\.

1. Choose **Reset parameters**\.

1. Restart the DB instance\.

For more information about modifying instance parameters, see [Modifying parameters in a DB parameter group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\. For more information about the dashboard, see [Monitoring with the Performance Insights dashboard](USER_PerfInsights.UsingDashboard.md)\. For more information about the MySQL performance schema, see [MySQL 8\.0 Reference Manual](https://dev.mysql.com/doc/refman/8.0/en/performance-schema.html)\.