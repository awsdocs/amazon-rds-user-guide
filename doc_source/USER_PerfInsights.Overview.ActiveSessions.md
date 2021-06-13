# DB load<a name="USER_PerfInsights.Overview.ActiveSessions"></a>

The central metric for Performance Insights is `DB Load`, which is collected every second\. The DB load represents the average active sessions \(AAS\) for the DB engine\. 

**Topics**
+ [Average active sessions](#USER_PerfInsights.Overview.ActiveSessions.AAS)
+ [Average active executions](#USER_PerfInsights.Overview.ActiveSessions.AAE)
+ [Dimensions](#USER_PerfInsights.Overview.ActiveSessions.dimensions)

## Average active sessions<a name="USER_PerfInsights.Overview.ActiveSessions.AAS"></a>

An active session is a connection that has submitted work to the DB engine and is waiting for a response\. For example, if you submit a SQL query to the DB engine, the database session is active while the engine is processing the query\. 

To obtain the average active sessions, Performance Insights samples the number of sessions concurrently running a query\. The AAS is the total number of sessions divided by the total number of samples\. The following table shows 5 consecutive samples of a running query\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PerfInsights.Overview.ActiveSessions.html)

## Average active executions<a name="USER_PerfInsights.Overview.ActiveSessions.AAE"></a>

The average active executions \(AAE\) per second is related to AAS\. To calculate the AAE, Performance Insights divides the total execution time of a query by the time interval\. The following table shows the AAE calculation for the same query in the preceding table\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PerfInsights.Overview.ActiveSessions.html)

In most cases, the AAS and AAE for a query are approximately the same\. However, because the inputs to the calculations are different data sources, the calculations often vary slightly\.

## Dimensions<a name="USER_PerfInsights.Overview.ActiveSessions.dimensions"></a>

The `db.load` metric is different from the other time\-series metrics because you can break it into subcomponents called dimensions\. You can think of dimensions as categories or "group by" clauses for the different characteristics of the `DB Load` metric\. When you are diagnosing performance issues, the most useful dimensions are wait events and top SQL\.

### Wait events<a name="USER_PerfInsights.Overview.ActiveSessions.waits"></a>

A *wait event* causes a SQL statement to wait for a specific event to happen before it can continue running\. For example, a SQL statement might wait until a locked resource is unlocked\. By combining `DB Load` with wait events, you can get a complete picture of the session state\. Wait events vary by DB engine: 
+ For information about all MariaDB and MySQL wait events, see [Wait Event Summary Tables](https://dev.mysql.com/doc/refman/5.7/en/wait-summary-tables.html) in the MySQL documentation\.
+ For information about all PostgreSQL wait events, see [PostgreSQL Wait Events](https://www.postgresql.org/docs/10/static/monitoring-stats.html#WAIT-EVENT-TABLE) in the PostgreSQL documentation\.
+ For information about all Oracle wait events, see [ Descriptions of Wait Events](https://docs.oracle.com/database/121/REFRN/GUID-2FDDFAA4-24D0-4B80-A157-A907AF5C68E2.htm#REFRN-GUID-2FDDFAA4-24D0-4B80-A157-A907AF5C68E2) in the Oracle documentation\.
+ For information about all SQL Server wait events, see [ Types of Waits](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql?view=sql-server-2017#WaitTypes) in the SQL Server documentation\.

**Note**  
For Oracle, background processes sometimes do work without an associated SQL statement\. In these cases, Performance Insights reports the type of background process concatenated with a colon and the wait class associated with that background process\. Types of background process include LGWR, ARC0, PMON, and so on\.   
For example, when the archiver is performing I/O, the Performance Insights report for it is similar to `ARC1:System I/O`\. Occasionally, the background process type is also missing, and Performance Insights only reports the wait class, for example `:System I/O`\. 

### Top SQL<a name="USER_PerfInsights.Overview.ActiveSessions.top-sql"></a>

Whereas wait events show bottlenecks, top SQL shows which queries are contributing the most to DB load\. For example, many queries might be currently running on the database, but a single query might consume 99% of the DB load\. In this case, the high load might indicate a problem with the query\. 

By default, the Performance Insights console displays top SQL queries that are contributing to the database load\. The console also shows relevant statistics for each statement\. To diagnose performance problems for a specific statement, you can examine its execution plan\.