# Common DBA tasks for Amazon RDS for PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks"></a>

Database administrators \(DBAs\) perform a variety of tasks when administering an Amazon RDS for PostgreSQL DB instance\. If you're a DBA already familiar with PostgreSQL, you need to be aware of some of the important differences between running PostgreSQL on your hardware and RDS for PostgreSQL\. For example, because it's a managed service, Amazon RDS doesn't allow shell access to your DB instances\. That means that you don't have direct access to `pg_hba.conf` and other configuration files\. For RDS for PostgreSQL, changes that are typically made to the PostgreSQL configuration file of an on\-premises instance are made to a custom DB parameter group associated with the RDS for PostgreSQL DB instance\. For more information, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

You also can't access log files in the same way that you do with an on\-premises PostgreSQL instance\. To learn more about logging, see [RDS for PostgreSQL database log files](USER_LogAccess.Concepts.PostgreSQL.md)\.

As another example, you don't have access to the PostgreSQL `superuser` account\. On RDS for PostgreSQL, the `rds_superuser` role is the most highly privileged role, and it's granted to `postgres` at set up time\. Whether you're familiar with using PostgreSQL on\-premises or completely new to RDS for PostgreSQL, we recommend that you understand the `rds_superuser` role, and how to work with roles, users, groups, and permissions\. For more information, see [Understanding PostgreSQL roles and permissions](Appendix.PostgreSQL.CommonDBATasks.Roles.md)\.

Following are some common DBA tasks for RDS for PostgreSQL\.

**Topics**
+ [Understanding PostgreSQL roles and permissions](Appendix.PostgreSQL.CommonDBATasks.Roles.md)
+ [Working with the PostgreSQL autovacuum on Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Autovacuum.md)
+ [Working with logging mechanisms supported by RDS for PostgreSQL](#Appendix.PostgreSQL.CommonDBATasks.Auditing)
+ [Using pgBadger for log analysis with PostgreSQL](#Appendix.PostgreSQL.CommonDBATasks.Badger)
+ [Working with parameters on your RDS for PostgreSQL DB instance](Appendix.PostgreSQL.CommonDBATasks.Parameters.md)

## Working with logging mechanisms supported by RDS for PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks.Auditing"></a>

There are several parameters, extensions, and other configurable items that you can set to log activities that occur on your PostgreSQL DB instance\. These include the following:
+ The `log_statement` parameter can be used to log user activity in your PostgreSQL database\. To learn more about RDS for PostgreSQL logging and how to monitor the logs, see [RDS for PostgreSQL database log files](USER_LogAccess.Concepts.PostgreSQL.md)\.
+ The `rds.force_admin_logging_level` parameter logs actions by the Amazon RDS internal user \(rdsadmin\) in the databases on the DB instance\. It writes the output to the PostgreSQL error log\. Allowed values are `disabled`, `debug5`, `debug4`, `debug3`, `debug2`, `debug1`, `info`, `notice`, `warning`, `error`, log, `fatal`, and `panic`\. The default value is `disabled`\.
+ The `rds.force_autovacuum_logging_level` parameter can be set to capture various autovacuum operations in the PostgreSQL error log\. For more information, see [Logging autovacuum and vacuum activities](Appendix.PostgreSQL.CommonDBATasks.Autovacuum.md#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.Logging)\. 
+ The PostgreSQL Audit \(pgAudit\) extension can be installed and configured to capture activities at the session level or at the object level\. For more information, see [Using pgAudit to log database activity](Appendix.PostgreSQL.CommonDBATasks.Extensions.md#Appendix.PostgreSQL.CommonDBATasks.pgaudit)\.
+ The `log_fdw` extension makes it possible for you to access the database engine log using SQL\. For more information, see [Using the log\_fdw extension to access the DB log using SQL](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md#CHAP_PostgreSQL.Extensions.log_fdw)\.
+ The `pg_stat_statements` library is specified as the default for the `shared_preload_libraries` parameter in RDS for PostgreSQL version 10 and higher\. It's this library that you can use to analyze running queries\. Be sure that `pg_stat_statements` is set in your DB parameter group\. For more information about monitoring your RDS for PostgreSQL DB instance using the information that this library provides, see [SQL statistics for RDS PostgreSQL](USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.md)\.
+ The `log_hostname` parameter captures to the log the hostname of each client connection\. For RDS for PostgreSQL version 12 and higher versions, this parameter is set to `off` by default\. If you turn it on, be sure to monitor session connection times\. When turned on, the service uses the domain name system \(DNS\) reverse lookup request to get the hostname of the client that's making the connection and add it to the PostgreSQL log\. This has a noticeable impact during session connection\. We recommend that you turn on this parameter for troubleshooting purposes only\. 

In general terms, the point of logging is so that the DBA can monitor, tune performance, and troubleshoot\. Many of the logs are uploaded automatically to Amazon CloudWatch or Performance Insights\. Here, they're sorted and grouped to provide complete metrics for your DB instance\. To learn more about Amazon RDS monitoring and metrics, see [Monitoring metrics in an Amazon RDS instance](CHAP_Monitoring.md)\. 

## Using pgBadger for log analysis with PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks.Badger"></a>

You can use a log analyzer such as [pgBadger](http://dalibo.github.io/pgbadger/) to analyze PostgreSQL logs\. The pgBadger documentation states that the %l pattern \(the log line for the session or process\) should be a part of the prefix\. However, if you provide the current RDS `log_line_prefix` as a parameter to pgBadger it should still produce a report\.

For example, the following command correctly formats an Amazon RDS for PostgreSQL log file dated 2014\-02\-04 using pgBadger\.

```
./pgbadger -f stderr -p '%t:%r:%u@%d:[%p]:' postgresql.log.2014-02-04-00 
```