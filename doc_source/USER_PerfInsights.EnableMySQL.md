# Turning on the Performance Schema for Performance Insights on Amazon RDS for MariaDB or MySQL<a name="USER_PerfInsights.EnableMySQL"></a>

The Performance Schema is an optional feature for monitoring Amazon RDS for MariaDB or MySQL runtime performance at a low level of detail\. The Performance Schema is designed to have minimal impact on database performance\. Performance Insights is a separate feature that you can use with or without the Performance Schema\.

**Topics**
+ [Overview of the Performance Schema](#USER_PerfInsights.EnableMySQL.overview)
+ [Performance Insights and the Performance Schema](#USER_PerfInsights.effect-of-pfs)
+ [Automatic management of the Performance Schema by Performance Insights](#USER_PerfInsights.EnableMySQL.options)
+ [Effect of a reboot on the Performance Schema](#USER_PerfInsights.EnableMySQL.reboot)
+ [Determining whether Performance Insights is managing the Performance Schema](#USER_PerfInsights.EnableMySQL.determining-status)
+ [Configuring the Performance Schema for automatic management](#USER_PerfInsights.EnableMySQL.RDS)

## Overview of the Performance Schema<a name="USER_PerfInsights.EnableMySQL.overview"></a>

The Performance Schema monitors events in MariaDB and MySQL databases\. An *event* is a database server action that consumes time and has been instrumented so that timing information can be collected\. Examples of events include the following:
+ Function calls
+ Waits for the operating system
+ Stages of SQL execution
+ Groups of SQL statements

The `PERFORMANCE_SCHEMA` storage engine is a mechanism for implementing the Performance Schema feature\. This engine collects event data using instrumentation in the database source code\. The engine stores events in memory\-only tables in the `performance_schema` database\. You can query `performance_schema` just as you can query any other tables\. For more information, see [MySQL Performance Schema](https://dev.mysql.com/doc/refman/8.0/en/performance-schema.html) in the *MySQL Reference Manual*\.

## Performance Insights and the Performance Schema<a name="USER_PerfInsights.effect-of-pfs"></a>

Performance Insights and the Performance Schema are separate features, but they are connected\. The behavior of Performance Insights for Amazon RDS for MariaDB or MySQL depends on whether the Performance Schema is turned on, and if so, whether Performance Insights manages the Performance Schema automatically\. The following table describes the behavior\.


| Performance Schema turned on | Performance Insights management mode | Performance Insights behavior | 
| --- | --- | --- | 
|  Yes  |  Automatic  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PerfInsights.EnableMySQL.html)  | 
|  Yes  |  Manual  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PerfInsights.EnableMySQL.html)  | 
|  No  |  N/A  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PerfInsights.EnableMySQL.html)  | 

## Automatic management of the Performance Schema by Performance Insights<a name="USER_PerfInsights.EnableMySQL.options"></a>

When you create an Amazon RDS for MariaDB or MySQL DB instance with Performance Insights turned on, the Performance Schema is also turned on\. In this case, Performance Insights automatically manages your Performance Schema parameters\. This is the recommended configuration\.

For automatic management of the Performance Schema, the following conditions must be true:
+ The `performance_schema` parameter is set to `0`\.
+ The **Source** is set to `system`, which is the default\.

If you change the `performance_schema` parameter value manually, and then later want to change to automatic management, see [Configuring the Performance Schema for automatic management](#USER_PerfInsights.EnableMySQL.RDS)\.

**Important**  
When Performance Insights turns on the Performance Schema, it doesn't change the parameter group values\. However, the values are changed on the DB instances that are running\. The only way to see the changed values is to run the `SHOW GLOBAL VARIABLES` command\.

## Effect of a reboot on the Performance Schema<a name="USER_PerfInsights.EnableMySQL.reboot"></a>

Performance Insights and the Performance Schema differ in their requirements for DB instance reboots:

Performance Schema  
To turn this feature on or off, you must reboot the DB instance\.

Performance Insights  
To turn this feature on or off, you don't need to reboot the DB instance\.

If the Performance Schema isn't currently turned on, and you turn on Performance Insights without rebooting the DB instance, the Performance Schema won't be turned on\.

## Determining whether Performance Insights is managing the Performance Schema<a name="USER_PerfInsights.EnableMySQL.determining-status"></a>

To find out whether Performance Insights is currently managing the Performance Schema for major engine versions 5\.6, 5\.7, and 8\.0, review the following table\.


| Setting of performance\_schema parameter | Setting of the Source column | Performance Insights is managing the Performance Schema? | 
| --- | --- | --- | 
| 0 | system | Yes | 
| 0 or 1 | user | No | 

**To determine whether Performance Insights is managing the Performance Schema automatically**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Parameter groups**\.

1. Select the name of the parameter group for your DB instance\.

1. Enter **performance\_schema** in the search bar\.

1. Check whether **Source** is the system default and **Values** is **0**\. If so, Performance Insights is managing the Performance Schema automatically\. If not, Performance Insights isn't managing the Performance Schema automatically\.  
![\[Shows the settings for the performance_schema parameter\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_schema_user.png)

## Configuring the Performance Schema for automatic management<a name="USER_PerfInsights.EnableMySQL.RDS"></a>

Assume that Performance Insights is turned on for your DB instance or Multi\-AZ DB cluster but isn't currently managing the Performance Schema\. If you want to allow Performance Insights to manage the Performance Schema automatically, complete the following steps\.

**To configure the Performance Schema for automatic management**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Parameter groups**\.

1. Select the name of the parameter group for your DB instance or Multi\-AZ DB cluster\.

1. Enter **performance\_schema** in the search bar\.

1. Select the `performance_schema` parameter\.

1. Choose **Edit parameters**\.

1. Select the `performance_schema` parameter\.

1. In **Values**, choose **0**\.

1. Choose **Reset** and then **Reset parameters**\.

1. Reboot the DB instance or Multi\-AZ DB cluster\.
**Important**  
Whenever you turn the Performance Schema on or off, make sure to reboot the DB instance or Multi\-AZ DB cluster\.

For more information about modifying instance parameters, see [Modifying parameters in a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Modifying)\. For more information about the dashboard, see [Analyzing metrics with the Performance Insights dashboard](USER_PerfInsights.UsingDashboard.md)\. For more information about the MySQL performance schema, see [MySQL 8\.0 Reference Manual](https://dev.mysql.com/doc/refman/8.0/en/performance-schema.html)\.