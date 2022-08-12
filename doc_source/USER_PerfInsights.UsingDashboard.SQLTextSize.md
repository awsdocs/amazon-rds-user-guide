# Accessing more SQL text in the Performance Insights dashboard<a name="USER_PerfInsights.UsingDashboard.SQLTextSize"></a>

By default, each row in the **Top SQL** table shows 500 bytes of SQL text for each SQL statement\.

![\[500 bytes of SQL\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf-insights-top-sql-bytes.png)

When a SQL statement exceeds 500 bytes, you can view more text in the **SQL text** section below the **Top SQL** table\. In this case, the maximum length for the text displayed in **SQL text** is 4 KB\. This limit is introduced by the console and is subject to the limits set by the database engine\. To save the text shown in **SQL text**, choose **Download**\.

**Topics**
+ [Text size limits for Amazon RDS engines](#sql-text-engine-limits)
+ [Setting the SQL text limit for Amazon RDS for PostgreSQL DB instances](#USER_PerfInsights.UsingDashboard.SQLTextLimit)
+ [Viewing and downloading SQL text in the Performance Insights dashboard](#view-download-text)

## Text size limits for Amazon RDS engines<a name="sql-text-engine-limits"></a>

When you download SQL text, the database engine determines its maximum length\. You can download SQL text up to the following per\-engine limits\.


| DB engine | Maximum length of downloaded text | 
| --- | --- | 
| Amazon RDS for MySQL and MariaDB | 1,024 bytes | 
| Amazon RDS for Microsoft SQL Server | 4,096 characters | 
| Amazon RDS for Oracle | 1,000 bytes | 

The **SQL text** section of the Performance Insights console displays up to the maximum that the engine returns\. For example, if MySQL returns at most 1 KB to Performance Insights, it can only collect and show 1 KB, even if the original query is larger\. Thus, when you view the query in **SQL text** or download it, Performance Insights returns the same number of bytes\.

If you use the AWS CLI or API, Performance Insights doesn't have the 4 KB limit enforced by the console\. `DescribeDimensionKeys` and `GetResourceMetrics` return at most 500 bytes\. `GetDimensionKeyDetails` returns the full query, but the size is subject to the engine limit\. 

## Setting the SQL text limit for Amazon RDS for PostgreSQL DB instances<a name="USER_PerfInsights.UsingDashboard.SQLTextLimit"></a>

Amazon RDS for PostgreSQL handles text differently\. You can set the text size limit with the DB instance parameter `track_activity_query_size`\. This parameter has the following characteristics:

Default text size  
On Amazon RDS for PostgreSQL version 9\.6, the default setting for the `track_activity_query_size` parameter is 1,024 bytes\. On Amazon RDS for PostgreSQL version 10 or higher, the default is 4,096 bytes\.

Maximum text size  
The limit for `track_activity_query_size` is 102,400 bytes for Amazon RDS for PostgreSQL version 12 and lower\. The maximum is 1 MB for version 13 and higher\.   
If the engine returns 1 MB to Performance Insights, the console displays only the first 4 KB\. If you download the query, you get the full 1 MB\. In this case, viewing and downloading return different numbers of bytes\. For more information about the `track_activity_query_size` DB instance parameter, see [Run\-time Statistics](https://www.postgresql.org/docs/current/runtime-config-statistics.html) in the PostgreSQL documentation\.

To increase the SQL text size, increase the `track_activity_query_size` limit\. To modify the parameter, change the parameter setting in the parameter group that is associated with the Amazon RDS for PostgreSQL DB instance\.

**To change the setting when the instance uses the default parameter group**

1. Create a new DB instance parameter group for the appropriate DB engine and DB engine version\.

1. Set the parameter in the new parameter group\.

1. Associate the new parameter group with the DB instance\.

For information about setting a DB instance parameter, see [Modifying parameters in a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.

## Viewing and downloading SQL text in the Performance Insights dashboard<a name="view-download-text"></a>

In the Performance Insights dashboard, you can view or download SQL text\.

**To view more SQL text in the Performance Insights dashboard**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\.

   The Performance Insights dashboard is displayed for your DB instance\.

1. Scroll down to the **Top SQL** tab\.

1. Choose a SQL statement\.

   SQL statements with text larger than 500 bytes look similar to the following image\.  
![\[SQL statements with large text\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf-insights-large-text-1.png)

1. Scroll down to the **SQL text** tab\.  
![\[SQL information section shows more of the SQL text\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf-insights-large-text-2.png)

   The Performance Insights dashboard can display up to 4,096 bytes for each SQL statement\.

1. \(Optional\) Choose **Copy** to copy the displayed SQL statement, or choose **Download** to download the SQL statement to view the SQL text up to the DB engine limit\.
**Note**  
To copy or download the SQL statement, disable pop\-up blockers\. 