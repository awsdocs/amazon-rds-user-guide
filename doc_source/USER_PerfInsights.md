# Using Amazon RDS Performance Insights<a name="USER_PerfInsights"></a>

Amazon RDS Performance Insights monitors your Amazon RDS DB instance load so that you can analyze and troubleshoot your database performance\. Amazon RDS Performance Insights is currently available for use with the following DB engines:
+ Amazon Aurora MySQL \(MySQL 5\.6 version 1\.17\.3 and higher\)
+ Amazon RDS MySQL \(MySQL 5\.7 version 5\.7\.22 and higher\)
+ Amazon Aurora PostgreSQL
+ Amazon RDS PostgreSQL version 10

For information about using Amazon Aurora, see the [ *Amazon Aurora User Guide*](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html)\.

**Note**  
Performance Insights is not supported on db\.t2 DB instance classes\.

Performance Insights expands on existing Amazon RDS monitoring features to illustrate your database's performance and help you analyze any issues that affect it\. With the Performance Insights dashboard, you can visualize the database load and filter the load by waits, SQL statements, hosts, or users\. Performance Insights is on by default in the console create wizard for the Amazon Aurora MySQL, Amazon RDS MySQL, Amazon Aurora PostgreSQL, and Amazon RDS PostgreSQL DB engines\. If you have more than one database on the DB instance, performance data for all of the databases is aggregated for the DB instance\. 

The central metric for Performance Insights is `DB Load`, which represents the average number of active sessions for the DB engine\. An *active session* is a connection that has submitted work to the DB engine and is waiting for a response from it\. For example, if you submit a SQL query to the DB engine, the database session is active while the DB engine is processing that query\. 

By combining `DB Load` with wait event data, you can get a complete picture of the state for an active session\. Wait events vary by DB engine: 
+ For information about all MySQL wait events, see [Wait Event Summary Tables](https://dev.mysql.com/doc/refman/5.6/en/wait-summary-tables.html) in the MySQL documentation\.
+ For information about all PostgreSQL wait events, see [PostgreSQL Wait Events](https://www.postgresql.org/docs/10/static/monitoring-stats.html#WAIT-EVENT-TABLE) in the PostgreSQL documentation\.

Session information is collected, aggregated, and displayed in the dashboard as the **Average Active Sessions** chart\. The **Average Active Sessions** chart displays the **Max CPU** value as a line, so you can see if active sessions are exceeding it or not\. The **Max CPU** value is determined by the number of **vCPU** \(virtual CPU\) cores for your DB instance\. 

If you find that the load in the **Average Active Sessions** chart is often above the **Max CPU** line and the primary wait state is CPU, the system CPU is overloaded\. In these cases, you might want to throttle connections to the instance, tune any SQL queries with a high CPU load, or consider a larger instance class\. High and consistent instances of any wait state indicate that there might be bottlenecks or resource contention issues that you should resolve, even if the load doesn't cross the **Max CPU** line\.

You can find an overview of Performance Insights in the following video\.

[![AWS Videos](http://img.youtube.com/vi/yOeWcPBT458/0.jpg)](http://www.youtube.com/watch?v=yOeWcPBT458)

**Topics**
+ [Enabling Performance Insights](USER_PerfInsights.Enabling.md)
+ [Access Control for Performance Insights](USER_PerfInsights.access-control.md)
+ [Using the Performance Insights Dashboard](USER_PerfInsights.UsingDashboard.md)
+ [Additional User Interface Features](USER_PerfInsights.UIcontrols.md)
+ [Performance Insights API](USER_PerfInsights.API.md)
+ [Performance Insights Metrics Published to Amazon CloudWatch](USER_PerfInsights.Cloudwatch.md)
+ [Logging Performance Insights Operations by Using AWS CloudTrail](USER_PerfInsights.CloudTrail.md)