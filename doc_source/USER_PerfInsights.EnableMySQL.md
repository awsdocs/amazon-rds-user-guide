# Enabling the Performance Schema for Performance Insights on Amazon RDS for MariaDB or MySQL<a name="USER_PerfInsights.EnableMySQL"></a>

The Performance Schema is an optional feature for monitoring Amazon RDS for MariaDB or MySQL runtime performance at a low level\. You can use Performance insights with or without the Performance Schema\. The Performance Schema is designed to have minimal impact on database performance\.

**Topics**
+ [Overview of the Performance Schema](#USER_PerfInsights.EnableMySQL.overview)
+ [Options for enabling Performance Schema](#USER_PerfInsights.EnableMySQL.options)
+ [Configuring the Performance Schema for automatic management](#USER_PerfInsights.EnableMySQL.RDS)

## Overview of the Performance Schema<a name="USER_PerfInsights.EnableMySQL.overview"></a>

The Performance Schema monitors server events\. In this context, an event is a server action that consumes time\. Performance Schema events are distinct from binlog events and scheduler events\. 

The `PERFORMANCE_SCHEMA` storage engine collects event data using instrumentation in the database source code\. The engine stores collected events in tables in the `performance_schema` database\. You can query `performance_schema` just as you can query any other tables\. For more information, see [MySQL Performance Schema](https://dev.mysql.com/doc/refman/8.0/en/performance-schema.html) in *MySQL Reference Manual*\.

When the Performance Schema is enabled for Amazon RDS for MariaDB or MySQL, Performance Insights uses it to provide more detailed information\. For example, Performance Insights displays DB load categorized by detailed wait events\. You can use wait events to identify bottlenecks\. Without the Performance Schema, Performance Insights reports user states such as inserting and sending, which don't help you identify bottlenecks\.

## Options for enabling Performance Schema<a name="USER_PerfInsights.EnableMySQL.options"></a>

You have the following options for enabling the Performance Schema:
+ Allow Performance Insights to manage required Performance Schema parameters automatically\.

  When you create an Amazon RDS for MariaDB or MySQL DB instance with Performance Insights enabled, the Performance Schema is also enabled\. In this case, Performance Insights automatically manages your Performance Schema parameters\. 

  For automatic management, the `performance_schema` must be set to `0` and the **Source** must be set to a value other than `0`\. By default, **Source** is `engine-default`\. If you change the `performance_schema` value manually, and then later want to revert to automatic management, see [Configuring the Performance Schema for automatic management](#USER_PerfInsights.EnableMySQL.RDS)\.
**Important**  
When Performance Insights enables the Performance Schema, it doesn't change the parameter group values\. However, the values are changed on the instances that are running\. The only way to see the changed values is to run the `SHOW GLOBAL VARIABLES` command\.
+ Set the required Performance Schema parameters yourself\.

  For Performance Insights to list wait events, set all Performance Schema parameters as shown in the following table\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PerfInsights.EnableMySQL.html)

**Note**  
If you enable or disable the Performance Schema, you must reboot the database\. If you enable or disable Performance Insights, you don't need to reboot the database\.

For more information, see [Performance Schema Command Options](https://dev.mysql.com/doc/refman/5.6/en/performance-schema-options.html#option_mysqld_performance-schema-consumer-events-stages-current) and [Performance Schema Option and Variable Reference](https://dev.mysql.com/doc/refman/8.0/en/performance-schema-option-variable-reference.html) in the MySQL documentation\.

## Configuring the Performance Schema for automatic management<a name="USER_PerfInsights.EnableMySQL.RDS"></a>

The following table shows the difference in settings when Performance Insights is and isn't managing the Performance Schema\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PerfInsights.EnableMySQL.html)

**To let Performance Insights manage the Performance Schema automatically**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Parameter groups**\.

1. Select the name of the parameter group for your DB instance\.

1. Enter **performance\_schema** in the search bar\.

1. Select the `performance_schema` parameter\.  
![\[Select performance_schema\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_schema.png)

1. Check whether **Source** is **system** and **Values** is **0**\. If so, Performance Insights is managing the Performance Schema automatically\. If not, proceed to the next step\.  
![\[Shows the settings for the performance_schema parameter\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_schema_user.png)

1. Choose **Edit parameters**\.

1. In **Values**, choose **0**\.

1. Select **Reset**\. When you reset, Amazon RDS sets **Source** to **system** and **Values** to **0**\.  
![\[Shows the settings for the performance_schema parameter\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/reset.png)

   The **Reset parameters in DB parameter group** page appears\.

1. Select **Reset parameters**\.

1. Restart the DB instance\.
**Important**  
Whenever you enable or disable the Performance Schema, you must restart the DB instance\.

For more information about modifying instance parameters, see [Modifying parameters in a DB parameter group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\. For more information about the dashboard, see [Analyzing metrics with the Performance Insights dashboard](USER_PerfInsights.UsingDashboard.md)\. For more information about the MySQL performance schema, see [MySQL 8\.0 Reference Manual](https://dev.mysql.com/doc/refman/8.0/en/performance-schema.html)\.