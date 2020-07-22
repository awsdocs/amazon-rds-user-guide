# Overview of Performance Insights<a name="USER_PerfInsights.Overview"></a>

By default, Performance Insights is enabled in the console create wizard for Amazon RDS engines\. If you have more than one database on a DB instance, Performance Insights aggregates performance data\.

## Active Sessions<a name="USER_PerfInsights.Overview.ActiveSessions"></a>

The central metric for Performance Insights is `DB Load`, which represents the average number of active sessions for the DB engine\. The `DB Load` metric is collected every second\. An *active session* is a connection that has submitted work to the DB engine and is waiting for a response\. For example, if you submit a SQL query to the DB engine, the database session is active while the DB engine is processing the query\. 

A *wait event* causes a SQL statement to wait for a specific event to happen before it can continue running\. For example, a SQL statement might wait until a locked resource is unlocked\. By combining `DB Load` with wait events, you can get a complete picture of the session state\. Wait events vary by DB engine: 
+ For information about all MariaDB and MySQL wait events, see [Wait Event Summary Tables](https://dev.mysql.com/doc/refman/5.7/en/wait-summary-tables.html) in the MySQL documentation\.
+ For information about all PostgreSQL wait events, see [PostgreSQL Wait Events](https://www.postgresql.org/docs/10/static/monitoring-stats.html#WAIT-EVENT-TABLE) in the PostgreSQL documentation\.
+ For information about all Oracle wait events, see [ Descriptions of Wait Events](https://docs.oracle.com/database/121/REFRN/GUID-2FDDFAA4-24D0-4B80-A157-A907AF5C68E2.htm#REFRN-GUID-2FDDFAA4-24D0-4B80-A157-A907AF5C68E2) in the Oracle documentation\.
+ For information about all SQL Server wait events, see [ Types of Waits](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql?view=sql-server-2017#WaitTypes) in the SQL Server documentation\.

**Note**  
For Oracle, background processes sometimes do work without an associated SQL statement\. In these cases, Performance Insights reports the type of background process concatenated with a colon and the wait class associated with that background process\. Types of background process include LGWR, ARC0, PMON, and so on\.   
For example, when the archiver is performing I/O, the Performance Insights report for it is similar to `ARC1:System I/O`\. Occasionally, the background process type is also missing, and Performance Insights only reports the wait class, for example `:System I/O`\. 

## Maximum CPU<a name="USER_PerfInsights.Overview.MaxCPU"></a>

In the dashboard, the **Database load** chart collects, aggregates, and displays session information\. To see whether active sessions are exceeding the maximum CPU, look at their relationship to the **Max vCPU** line\. The **Max vCPU** value is determined by the number of vCPU \(virtual CPU\) cores for your DB instance\. 

If the load is often above the **Max vCPU** line, and the primary wait state is CPU, the system CPU is overloaded\. In these cases, you might want to throttle connections to the instance, tune any SQL queries with a high CPU load, or consider a larger instance class\. High and consistent instances of any wait state indicate that there might be bottlenecks or resource contention issues to resolve\. This can be true even if the load doesn't cross the **Max vCPU** line\.

You can find an overview of Performance Insights in the following video\.

[![AWS Videos](http://img.youtube.com/vi/yOeWcPBT458/0.jpg)](http://www.youtube.com/watch?v=yOeWcPBT458)

## Supported DB Engines for Performance Insights<a name="USER_PerfInsights.Overview.Engines"></a>

Following, you can find the DB engines that support Performance Insights\. 


|  DB Engine  | Supported DB Engine Versions | 
| --- | --- | 
|  Amazon Aurora with MySQL compatibility  |  2\.04\.2 and higher 2\.x versions \(compatible with MySQL 5\.7\), and 1\.17\.3 and higher 1\.x versions \(compatible with MySQL 5\.6\)\. Not supported on db\.t2 or db\.t3 DB instance classes\. Not supported for DB clusters enabled for parallel query\.   | 
|  Amazon Aurora with PostgreSQL compatibility  |  All versions\.  | 
|  Amazon RDS for MariaDB  |  10\.4\.8 and higher 10\.4 versions, 10\.3\.13 and higher 10\.3 versions, and 10\.2\.21 and higher 10\.2 versions\. Not supported for MariaDB version 10\.0 or 10\.1\. Not supported for MariaDB version 10\.3\.13 DB instances in the Europe \(Frankfurt\) and Europe \(Stockholm\) AWS Regions\. Not supported on the following DB instance classes: db\.t2\.micro, db\.t2\.small, db\.t3\.micro, and db\.t3\.small\.  | 
|  Amazon RDS for MySQL  |  8\.0\.17 and higher 8\.0 versions, version 5\.7\.22 and higher 5\.7 versions, and version 5\.6\.41 and higher 5\.6 versions\. Not supported for version 5\.5\. Not supported on the following DB instance classes: db\.t2\.micro, db\.t2\.small, db\.t3\.micro, and db\.t3\.small\.  | 
|  Amazon RDS for Microsoft SQL Server  |  All versions except SQL Server 2008\.   | 
|  Amazon RDS for PostgreSQL  |  Version 10, 11, and 12\.  | 
|  Amazon RDS for Oracle  |  All versions\.   | 

**Note**  
Amazon RDS Performance Insights is not supported in the Middle East \(Bahrain\) Region\.

**Important**  
This guide describes using Amazon RDS Performance Insights with non\-Aurora DB engines\. For information about using Amazon RDS Performance Insights with Amazon Aurora, see the [Using Amazon RDS Performance Insights](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_PerfInsights.html) in the *Amazon Aurora User Guide*\.