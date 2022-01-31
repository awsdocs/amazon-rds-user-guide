# Viewing more SQL text in the Performance Insights dashboard<a name="USER_PerfInsights.UsingDashboard.SQLTextSize"></a>

By default, each row in the **Top SQL** table shows 500 bytes of SQL text for each SQL statement\. When a SQL statement is larger than 500 bytes, you can view more of the SQL statement by opening the statement in the Performance Insights dashboard\. The dashboard displays text up to the following per\-engine limits:
+ Amazon RDS for Microsoft SQL Server – 4,096 characters
+ Amazon RDS for MySQL and MariaDB – 1,024 bytes
+ Amazon RDS for Oracle – 1,000 bytes

You can copy the text that is displayed on the dashboard\. If you view a child SQL statement, you can also choose **Download**\.

Amazon RDS for PostgreSQL handles text differently\. Using the Performance Insights dashboard, you can view and download up to 500 bytes\. To access more than 500 bytes, set the size limit with the DB instance parameter `track_activity_query_size`\. The maximum value is 102,400 bytes\. To view or download text over 500 bytes, use the AWS Management Console, not the Performance Insights CLI or API\. For more information, see [Setting the SQL text limit for Amazon RDS for PostgreSQL DB instances](#USER_PerfInsights.UsingDashboard.SQLTextLimit)\.

**To view more SQL text in the Performance Insights dashboard**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\. The Performance Insights dashboard is displayed for that DB instance\.

   SQL statements with text larger than 500 bytes look similar to the following image\.  
![\[SQL statements with large text\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf-insights-large-text-1.png)

1. Examine the SQL information section to view more of the SQL text\.  
![\[SQL information section shows more of the SQL text\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/perf-insights-large-text-2.png)

   The Performance Insights dashboard can display up to 4,096 bytes for each SQL statement\.

1. \(Optional\) Choose **Copy** to copy the displayed SQL statement, or choose **Download** to download the SQL statement to view the SQL text up to the DB engine limit\.
**Note**  
To copy or download the SQL statement, disable pop\-up blockers\. 

## Setting the SQL text limit for Amazon RDS for PostgreSQL DB instances<a name="USER_PerfInsights.UsingDashboard.SQLTextLimit"></a>

For Amazon RDS for PostgreSQL DB instances, you can control the limit for the SQL text that can be shown on the Performance Insights dashboard\. 

To do so, modify the `track_activity_query_size` DB instance parameter\. The default setting for the `track_activity_query_size` parameter is 1,024 bytes\. 

You can increase the number of bytes to increase the SQL text size visible in the Performance Insights dashboard\. The limit for the parameter is 102,400 bytes\. For more information about the `track_activity_query_size` DB instance parameter, see [Run\-time Statistics](https://www.postgresql.org/docs/current/runtime-config-statistics.html) in the PostgreSQL documentation\.

To modify the parameter, change the parameter setting in the parameter group that is associated with the Amazon RDS for PostgreSQL DB instance\.

If the Amazon RDS for PostgreSQL DB instance is using the default parameter group, complete the following steps:

1. Create a new DB instance parameter group for the appropriate DB engine and DB engine version\.

1. Set the parameter in the new parameter group\.

1. Associate the new parameter group with the DB instance\.

For information about setting a DB instance parameter, see [Modifying parameters in a DB parameter group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.