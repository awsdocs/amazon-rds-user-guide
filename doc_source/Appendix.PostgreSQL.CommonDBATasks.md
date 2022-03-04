# Common DBA tasks for Amazon RDS for PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks"></a>

Database administrators \(DBAs\) perform a variety of tasks when administering an Amazon RDS for PostgreSQL DB instance\. It helps to understand some of the basic capabilities of PostgreSQL and how to use them in an RDS for PostgreSQL DB instance\. For example, if you're a DBA already familiar with PostgreSQL, be aware that Amazon RDS doesn't allow shell access to DB instances\. That means that you can't access log files in the same way that you do when you run PostgreSQL on your server hardware\. To learn more about working with RDS for PostgreSQL log files, see [PostgreSQL database log files](USER_LogAccess.Concepts.PostgreSQL.md)\.

Amazon RDS limits access to certain system procedures and tables that require advanced privileges to the `rds_superuser` role\. This is the most privileged identity on an RDS for PostgreSQL DB instance\. For more information, see [Understanding the rds\_superuser role](#Appendix.PostgreSQL.CommonDBATasks.Roles)\.

Following are some common DBA tasks for RDS for PostgreSQL\.

**Topics**
+ [Working with the PostgreSQL autovacuum on Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Autovacuum.md)
+ [Controlling user access to the PostgreSQL database](#Appendix.PostgreSQL.CommonDBATasks.Access)
+ [Working with logging mechanisms supported by RDS for PostgreSQL](#Appendix.PostgreSQL.CommonDBATasks.Auditing)
+ [Using pgBadger for log analysis with PostgreSQL](#Appendix.PostgreSQL.CommonDBATasks.Badger)
+ [Limiting control of user passwords to a specific role](#Appendix.PostgreSQL.CommonDBATasks.RestrictPasswordMgmt)
+ [Working with parameters on your RDS for PostgreSQL DB instance](Appendix.PostgreSQL.CommonDBATasks.Parameters.md)
+ [Understanding the rds\_superuser role](#Appendix.PostgreSQL.CommonDBATasks.Roles)

## Controlling user access to the PostgreSQL database<a name="Appendix.PostgreSQL.CommonDBATasks.Access"></a>

In RDS for PostgreSQL, you can manage which users have privileges to connect to which databases\. In PostgreSQL environments, you sometimes perform this kind of management by modifying the `pg_hba.conf` file\. In Amazon RDS, you can use database grants instead\.

New databases in PostgreSQL are always created with a default set of privileges\. The default privileges allow `PUBLIC` \(all users\) to connect to the database and to create temporary tables while connected\. 

To control which users are allowed to connect to a given database in Amazon RDS, first revoke the default `PUBLIC` privileges\. Then grant back the privileges on a more granular basis\. The following example shows how\.

```
postgres=> REVOKE ALL on database db-name from public;
postgres=> GRANT CONNECT, TEMPORARY on database db-name to user/role;
```

For more information about privileges in PostgreSQL databases, see the [GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html) command in the PostgreSQL documentation\.

## Working with logging mechanisms supported by RDS for PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks.Auditing"></a>

There are several parameters, extensions, and other configurable items that you can set to log activities that occur on your PostgreSQL DB instance\. These include the following:
+ The `log_statement` parameter can be used to log user activity in your PostgreSQL database\. To learn more about RDS for PostgreSQL logging and how to monitor the logs, see [PostgreSQL database log files](USER_LogAccess.Concepts.PostgreSQL.md)\.
+ The `rds.force_admin_logging_level` parameter logs actions by the Amazon RDS internal user \(rdsadmin\) in the databases on the DB instance\. It writes the output to the PostgreSQL error log\. Allowed values are `disabled`, `debug5`, `debug4`, `debug3`, `debug2`, `debug1`, `info`, `notice`, `warning`, `error`, log, `fatal`, and `panic`\. The default value is `disabled`\.
+ The `rds.force_autovacuum_logging_level` parameter can be set to capture various autovacuum operations in the PostgreSQL error log\. For more information, see [Logging autovacuum and vacuum activities](Appendix.PostgreSQL.CommonDBATasks.Autovacuum.md#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.Logging)\. 
+ The `pgaudit` extension can be installed and configured to capture activities at the session level or at the object level\. For more information, see [Logging at the session and object level with the pgaudit extension](Appendix.PostgreSQL.CommonDBATasks.Extensions.md#Appendix.PostgreSQL.CommonDBATasks.pgaudit)\.
+ The `log_fdw` extension makes it possible for you to access the database engine log using SQL\. For more information, see [Using the log\_fdw extension to access the DB log using SQL](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md#CHAP_PostgreSQL.Extensions.log_fdw)\.
+ The `pg_stat_statements` library is specified as the default for the `shared_preload_libraries` parameter in RDS for PostgreSQL version 10 and higher\. It's this library that you can use to analyze running queries\. Be sure that `pg_stat_statements` is set in your DB parameter group\. For more information about monitoring your RDS for PostgreSQL DB instance using the information that this library provides, see [Analyzing running queries in PostgreSQL](USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.md)\.

In general terms, the point of logging is so that the DBA can monitor, tune performance, and troubleshoot\. Many of the logs are uploaded automatically to Amazon CloudWatch or Performance Insights\. Here, they're sorted and grouped to provide complete metrics for your DB instance\. To learn more about Amazon RDS monitoring and metrics, see [Monitoring metrics in an Amazon RDS instance](CHAP_Monitoring.md)\. 

## Using pgBadger for log analysis with PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks.Badger"></a>

You can use a log analyzer such as [pgBadger](http://dalibo.github.io/pgbadger/) to analyze PostgreSQL logs\. The pgBadger documentation states that the %l pattern \(the log line for the session or process\) should be a part of the prefix\. However, if you provide the current RDS `log_line_prefix` as a parameter to pgBadger it should still produce a report\.

For example, the following command correctly formats an Amazon RDS for PostgreSQL log file dated 2014\-02\-04 using pgBadger\.

```
./pgbadger -f stderr -p '%t:%r:%u@%d:[%p]:' postgresql.log.2014-02-04-00 
```

## Limiting control of user passwords to a specific role<a name="Appendix.PostgreSQL.CommonDBATasks.RestrictPasswordMgmt"></a>

You can restrict who can manage database user passwords to a special role\. By doing this, you can have more control over password management on the client side\.

You turn on restricted password management with the static parameter `rds.restrict_password_commands` and use a role called `rds_password`\. When the parameter `rds.restrict_password_commands` is set to 1, only users that are members of the `rds_password` role can run certain SQL commands\. The restricted SQL commands are commands that modify database user passwords and password expiration time\. 

To use restricted password management, make sure that your DB instance is running RDS for PostgreSQL 10\.6 or higher\. Because the `rds.restrict_password_commands` parameter is static, changing this parameter requires a database restart\.

When a database has restricted password management turned on, if you try to run restricted SQL commands you get the following error: ERROR: must be a member of rds\_password to alter passwords\.

Following are some examples of SQL commands that are restricted when restricted password management is turned on\.

```
postgres=> CREATE ROLE myrole WITH PASSWORD 'mypassword';
postgres=> CREATE ROLE myrole WITH PASSWORD 'mypassword' VALID UNTIL '2020-01-01';
postgres=> ALTER ROLE myrole WITH PASSWORD 'mypassword' VALID UNTIL '2020-01-01';
postgres=> ALTER ROLE myrole WITH PASSWORD 'mypassword';
postgres=> ALTER ROLE myrole VALID UNTIL '2020-01-01';
postgres=> ALTER ROLE myrole RENAME TO myrole2;
```

Some `ALTER ROLE` commands that include `RENAME TO` might also be restricted\. They might be restricted because renaming a PostgreSQL role that has an MD5 password clears the password\. 

The `rds_superuser` role has membership for the `rds_password` role by default, and you can't change this\. You can give other roles membership for the `rds_password` role by using the `GRANT` SQL command\. We recommend that you give membership to `rds_password` to only a few roles that you use solely for password management\. These roles require the `CREATEROLE` attribute to modify other roles\.

Make sure that you verify password requirements such as expiration and needed complexity on the client side\. We recommend that you restrict password\-related changes by using your own client\-side utility\. This utility should have a role that is a member of `rds_password` and has the `CREATEROLE` role attribute\.

## Understanding the rds\_superuser role<a name="Appendix.PostgreSQL.CommonDBATasks.Roles"></a>

When you create an RDS for PostgreSQL DB instance, the main user system account that you create is assigned to the `rds_superuser` role\. The `rds_superuser` role is a predefined Amazon RDS role similar to the PostgreSQL superuser role \(customarily named `postgres` in local instances\), but with some restrictions\. As with the PostgreSQL superuser role, the `rds_superuser` role has the most privileges for your DB instance\. Don't assign this role to users unless they need the most access to the DB instance\. 

The `rds_superuser` role can do the following:
+ Add extensions that are available for use with Amazon RDS\. For more information, see [Working with PostgreSQL features supported by Amazon RDS for PostgreSQL](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.FeatureSupport) and the [PostgreSQL documentation](http://www.postgresql.org/docs/current/sql-createextension.html)\.
+ Manage tablespaces, including creating and deleting them\. For more information, see [ Tablespaces for RDS for PostgreSQL](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.FeatureSupport.Tablespaces) and the [Tablespaces](http://www.postgresql.org/docs/current/manage-ag-tablespaces.html) section in the PostgreSQL documentation\.
+ View all users that aren't assigned the `rds_superuser` role using the `pg_stat_activity` command and stop their connections using the `pg_terminate_backend` and `pg_cancel_backend` commands\.
+ Grant and revoke the `rds_replication` role for all roles that aren't the `rds_superuser` role\. For more information, see the [GRANT](http://www.postgresql.org/docs/current/sql-grant.html) section in the PostgreSQL documentation\.

As mentioned, the `rds_superuser` role can't do everything that the `postgres` superuser can do\. For example, `rds_superuser` can't bypass the `CONNECT` privilege when connecting to a database\. This role must be specifically granted to any users that you create that should have `rds_superuser` privileges\. 

The following example shows how to create a user and then grant the user the `rds_superuser` role\. 

```
postgres=> CREATE ROLE bus_app_admin WITH PASSWORD 'change_me' LOGIN;
CREATE ROLE
postgres=> GRANT rds_superuser TO bus_app_admin;
GRANT ROLE
```