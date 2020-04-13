# Using Amazon RDS Performance Insights<a name="USER_PerfInsights"></a>

Amazon RDS Performance Insights monitors your Amazon RDS DB instance load so that you can analyze and troubleshoot your database performance\. Amazon RDS Performance Insights is currently available for use with the following DB engines:
+ Amazon Aurora with MySQL compatibility version 2\.04\.2 and higher 2\.x versions \(compatible with MySQL 5\.7\)
+ Amazon Aurora with MySQL compatibility version 1\.17\.3 and higher 1\.x versions \(compatible with MySQL 5\.6\)
+ Amazon Aurora with PostgreSQL compatibility
+ Amazon RDS for MariaDB version 10\.4\.8 and higher 10\.4 versions, version 10\.3\.13 and higher 10\.3 versions, and version 10\.2\.21 and higher 10\.2 versions
+ Amazon RDS for MySQL version 8\.0\.17 and higher 8\.0 versions, version 5\.7\.22 and higher 5\.7 versions, and version 5\.6\.41 and higher 5\.6 versions
+ Amazon RDS for Microsoft SQL Server \(all versions except SQL Server 2008\)
+ Amazon RDS for PostgreSQL version 10 and 11
+ Amazon RDS for Oracle \(all versions\)

**Note**  
Amazon RDS Performance Insights is not supported for MariaDB version 10\.0 or 10\.1, or for MySQL version 5\.5\.  
For Amazon RDS for MariaDB and MySQL, Performance Insights is not supported on the following DB instance classes: db\.t2\.micro, db\.t2\.small, db\.t3\.micro, and db\.t3\.small\.  
On Aurora MySQL, Performance Insights is not supported on db\.t2 or db\.t3 DB instance classes\.  
Performance Insights is not supported for Aurora MySQL DB clusters enabled for parallel query\.

**Important**  
This guide describes using Amazon RDS Performance Insights with non\-Aurora DB engines\. For information about using Amazon RDS Performance Insights with Amazon Aurora, see the [Using Amazon RDS Performance Insights](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_PerfInsights.html) in the *Amazon Aurora User Guide*\.

Performance Insights expands on existing Amazon RDS monitoring features to illustrate your database's performance and help you analyze any issues that affect it\. With the Performance Insights dashboard, you can visualize the database load and filter the load by waits, SQL statements, hosts, or users\.

Performance Insights is on by default in the console create wizard for all DB engines that work with Amazon RDS\. If you have more than one database on a DB instance, performance data for all of the databases is aggregated for the DB instance\. 

The central metric for Performance Insights is `DB Load`, which represents the average number of active sessions for the DB engine\. The `DB Load` metric is collected every second\. An *active session* is a connection that has submitted work to the DB engine and is waiting for a response from it\. For example, if you submit a SQL query to the DB engine, the database session is active while the DB engine is processing that query\. 

By combining `DB Load` with wait event data, you can get a complete picture of the state for an active session\. A *wait event* is a condition that causes the execution of a SQL statement to wait for a specific event to happen before it can continue\. For example, SQL statement execution might wait until a locked resource is unlocked\. Wait events vary by DB engine: 
+ For information about all MariaDB and MySQL wait events, see [Wait Event Summary Tables](https://dev.mysql.com/doc/refman/5.7/en/wait-summary-tables.html) in the MySQL documentation\.
+ For information about all PostgreSQL wait events, see [PostgreSQL Wait Events](https://www.postgresql.org/docs/10/static/monitoring-stats.html#WAIT-EVENT-TABLE) in the PostgreSQL documentation\.
+ For information about all Oracle wait events, see [ Descriptions of Wait Events](https://docs.oracle.com/database/121/REFRN/GUID-2FDDFAA4-24D0-4B80-A157-A907AF5C68E2.htm#REFRN-GUID-2FDDFAA4-24D0-4B80-A157-A907AF5C68E2) in the Oracle documentation\.
+ For information about all SQL Server wait events, see [ Types of Waits](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql?view=sql-server-2017#WaitTypes) in the SQL Server documentation\.

**Note**  
For Oracle, background processes sometimes do work without an associated SQL statement\. In these cases, Performance Insights reports the type of background process concatenated with a colon and the wait class associated with that background process\. Types of background process include LGWR, ARC0, PMON, and so on\.   
For example, when the archiver is performing I/O, the Performance Insights report for it is similar to `ARC1:System I/O`\. Occasionally, the background process type is also missing, and Performance Insights only reports the wait class, for example `:System I/O`\.

Session information is collected, aggregated, and displayed in the dashboard as the **Average Active Sessions** chart\. The **Average Active Sessions** chart displays the **Max CPU** value as a line, so you can see if active sessions are exceeding it or not\. The **Max CPU** value is determined by the number of **vCPU** \(virtual CPU\) cores for your DB instance\. 

If the load in the **Average Active Sessions** chart is often above the **Max CPU** line and the primary wait state is CPU, the system CPU is overloaded\. In these cases, you might want to throttle connections to the instance, tune any SQL queries with a high CPU load, or consider a larger instance class\. High and consistent instances of any wait state indicate that there might be bottlenecks or resource contention issues to resolve\. This can be true even if the load doesn't cross the **Max CPU** line\.

You can find an overview of Performance Insights in the following video\.

[![AWS Videos](http://img.youtube.com/vi/yOeWcPBT458/0.jpg)](http://www.youtube.com/watch?v=yOeWcPBT458)

**Topics**
+ [Enabling Performance Insights](USER_PerfInsights.Enabling.md)
+ [Access Control for Performance Insights](USER_PerfInsights.access-control.md)
+ [Using the Performance Insights Dashboard](USER_PerfInsights.UsingDashboard.md)
+ [Additional User Interface Features](USER_PerfInsights.UIcontrols.md)
+ [Performance Insights API](USER_PerfInsights.API.md)
+ [Performance Insights Metrics Published to Amazon CloudWatch](USER_PerfInsights.Cloudwatch.md)
+ [Performance Insights Counters](USER_PerfInsights_Counters.md)
+ [Logging Performance Insights Calls by Using AWS CloudTrail](USER_PerfInsights.CloudTrail.md)