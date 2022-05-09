# Common DBA tasks for Amazon RDS for PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks"></a>

Database administrators \(DBAs\) perform a variety of tasks when administering an Amazon RDS for PostgreSQL DB instance\. If you're a DBA already familiar with PostgreSQL, you need to be aware of some of the important differences between running PostgreSQL on your hardware and RDS for PostgreSQL\. For example, because it's a managed service, Amazon RDS doesn't allow shell access to your DB instances\. That means that you don't have direct access to `pg_hba.conf` and other configuration files, nor can you work with log files in the same way that you do with an on\-premises PostgreSQL instance\. To learn more about logging, see [PostgreSQL database log files](USER_LogAccess.Concepts.PostgreSQL.md)\.

As another example, you don't have access to the PostgreSQL `superuser` account\. On RDS for PostgreSQL, the `rds_superuser` role is the most highly privileged role, and it's granted to `postgres` at set up time\. Whether you're familiar with using PostgreSQL on\-premises or completely new to RDS for PostgreSQL, we recommend that you understand the `rds_superuser` role, and how to work with roles, users, groups, and permissions\. For more information, see [Understanding PostgreSQL roles and permissions](#Appendix.PostgreSQL.CommonDBATasks.Roles)\.

Following are some common DBA tasks for RDS for PostgreSQL\.

**Topics**
+ [Understanding PostgreSQL roles and permissions](#Appendix.PostgreSQL.CommonDBATasks.Roles)
+ [Working with the PostgreSQL autovacuum on Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Autovacuum.md)
+ [Working with logging mechanisms supported by RDS for PostgreSQL](#Appendix.PostgreSQL.CommonDBATasks.Auditing)
+ [Using pgBadger for log analysis with PostgreSQL](#Appendix.PostgreSQL.CommonDBATasks.Badger)
+ [Working with parameters on your RDS for PostgreSQL DB instance](Appendix.PostgreSQL.CommonDBATasks.Parameters.md)

## Understanding PostgreSQL roles and permissions<a name="Appendix.PostgreSQL.CommonDBATasks.Roles"></a>

When you create an RDS for PostgreSQL DB instance using the AWS Management Console, an administrator account is created at the same time\. By default, its name is `postgres`, as shown in the following screenshot:

![\[The default login identity for Credentials in the Create database page is postgres.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/default-login-identity-apg-rpg.png)

You can choose another name rather than accept the default \(`postgres`\)\. If you do, the name you choose must start with a letter and be between 1 and 16 alphanumeric characters\. For simplicity's sake, we refer to this main user account by its default value \(`postgres`\) throughout this guide\.

If you use the `create-db-instance` AWS CLI rather than the AWS Management Console, you create the name by passing it with the `master-username` parameter in the command\. For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\. 

Whether you use the AWS Management Console, the AWS CLI, or the Amazon RDS API, and whether you use the default `postgres` name or choose a different name, this first database user account is a member of the `rds_superuser` group and has `rds_superuser` privileges\.

**Topics**
+ [Understanding the rds\_superuser role](#Appendix.PostgreSQL.CommonDBATasks.Roles.rds_superuser)
+ [Controlling user access to the PostgreSQL database](#Appendix.PostgreSQL.CommonDBATasks.Access)
+ [Delegating and controlling user password management](#Appendix.PostgreSQL.CommonDBATasks.RestrictPasswordMgmt)

### Understanding the rds\_superuser role<a name="Appendix.PostgreSQL.CommonDBATasks.Roles.rds_superuser"></a>

In PostgreSQL, a *role* can define a user, a group, or a set of specific permissions granted to a group or user for various objects in the database\. PostgreSQL commands to `CREATE USER` and `CREATE GROUP` have been replaced by the more general, `CREATE ROLE` with specific properties to distinguish database users\. A database user can be thought of as a role with the LOGIN privilege\. 

**Note**  
The `CREATE USER` and `CREATE GROUP` commands can still be used\. For more information, see [Database Roles](https://www.postgresql.org/docs/current/user-manag.html) in the PostgreSQL documentation\.

The `postgres` user is the most highly privileged database user on your RDS for PostgreSQL DB instance\. It has the characteristics defined by the following `CREATE ROLE` statement\. 

```
CREATE ROLE postgres WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION VALID UNTIL 'infinity'
```

The properties `NOSUPERUSER`, `NOREPLICATION`, `INHERIT`, and `VALID UNTIL 'infinity'` are the default options for CREATE ROLE, unless otherwise specified\. 

By default, `postgres` has privileges granted to the the `rds_superuser` role\. The `rds_superuser` role allows the `postgres` user to do the following: 
+ Add extensions that are available for use with Amazon RDS\. For more information, see [Working with PostgreSQL features supported by Amazon RDS for PostgreSQL](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.FeatureSupport) 
+ Create roles for users and grant privileges to users\. For more information, see [CREATE ROLE](https://www.postgresql.org/docs/current/sql-createrole.html) and [GRANT](https://www.postgresql.org/docs/14/sql-grant.html) in the PostgreSQL documentation\. 
+ Create databases\. For more information, see [CREATE DATABASE](https://www.postgresql.org/docs/14/sql-createdatabase.html) in the PostgreSQL documentation\.
+ Grant `rds_superuser` privileges to other user roles that don't have these privileges, and revoke such privileges as needed\. We recommend that can grant this role to others only as needed\. In other words, don't grant this role to users unless they are DBAs or system administrators\. 
+ Grant \(and revoke\) the `rds_replication` role to database users that don't have the `rds_superuser` role\. 
+ Grant \(and revoke\) the `rds_password` role to database users that don't have the `rds_superuser` role\. 
+ Obtain status information about all database connections by using the `pg_stat_activity` view\. When needed, `rds_superuser` can stop any connections by using `pg_terminate_backend` or `pg_cancel_backend`\. 

In the `CREATE ROLE postgres...` statement, you can see that the `postgres` user role specifically disallows PostgreSQL `superuser` permissions\. RDS for PostgreSQL is a managed service, so you can't access the host OS, and you can't connect using the PostgreSQL `superuser` account\. Many of the tasks that require `superuser` access on a stand\-alone PostgreSQL are managed automatically by Amazon RDS\. 

For more information about granting privileges, see [GRANT](http://www.postgresql.org/docs/current/sql-grant.html) in the PostgreSQL documentation\.

The `rds_superuser` role is one of several *predefined* roles in an RDS for PostgreSQL DB instance\. 

**Note**  
In PostgreSQL 13 and earlier releases, *predefined* roles are known as *default* roles\.

In the following list, you find some of the other predefined roles that are created automatically for a new RDS for PostgreSQL DB instance\. Predefined roles and their privileges can't be changed\. You can't drop, rename, or modify privileges for these predefined roles\. Attempting to do so results in an error\. 
+ **rds\_password** – A role that can change passwords and set up password constraints for databaes users\. The `rds_superuser` role is granted this role by default, and can grant the role to database users\. `For more information, see [Controlling user access to the PostgreSQL databaseControlling user access to PostgreSQL](#Appendix.PostgreSQL.CommonDBATasks.Access)\. 
+ **rdsadmin** – A role that's created to handle many of the management tasks that the adminstrator with `superuser` privileges would perform on a standalone PostgreSQL database\. This role is used internally by RDS for PostgreSQL for many management tasks\. 
+ **rdstopmgr** – A role that's used internally by Amazon RDS to support Multi\-AZ deployments\. 

To see all predefined roles, you can connect to your RDS for PostgreSQL DB instance and use the `psq1 \du` metacommand\. The output looks as follows: 

```
List of roles
  Role name   |      Attributes                   |      Member of      
--------------+-----------------------------------+------------------------------------
postgres      | Create role, Create DB           +| {rds_superuser}
              | Password valid until infinity     |
rds_superuser | Cannot login                      | {pg_monitor,pg_signal_backend,
              |                                  +|   rds_replication,rds_password}
...
```

In the output, you can see that `rds_superuser` isn't a database user role \(it can't login\), but it has the privileges of many other roles\. You can also see that database user `postgres` is a member of the `rds_superuser` role\. As mentioned previously, `postgres` is the default value in the Amazon RDS console's **Create database** page\. If you chose another name, that name is shown in the list of roles instead\. 

### Controlling user access to the PostgreSQL database<a name="Appendix.PostgreSQL.CommonDBATasks.Access"></a>

New databases in PostgreSQL are always created with a default set of privileges in the database's `public` schema that allow all database users and roles to create objects\. These privileges allow database users to connect to the database, for example, and create temporary tables while connected\.

To better control user access to the databases instances that you create on your RDS for PostgreSQL DB instance, we recommend that you revoke these default `public` privileges\. After doing so, you then grant specific privileges for database users on a more granular basis, as shown in the following procedure\. 

**To set up roles and privileges for a new database instance**

Suppose you're setting up a database on a newly created RDS for PostgreSQL DB instance for use by several researchers, all of whom need read\-write access to the database\. 

1. Use `psql` \(or pgAdmin\) to connect to your RDS for PostgreSQL DB instance:

   ```
   psql --host=your-db-instance.666666666666.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password
   ```

   When prompted, enter your password\. The `psql` client connects and displays the default administrative connection database, `postgres=>`, as the prompt\.

1. To prevent database users from creating objects in the `public` schema, do the following:

   ```
   postgres=> REVOKE CREATE ON SCHEMA public FROM PUBLIC;
   REVOKE
   ```

1. Next, you create a new database instance:

   ```
   postgres=> CREATE DATABASE lab_db;
   CREATE DATABASE
   ```

1. Revoke all privileges from the `PUBLIC` schema on this new database\.

   ```
   postgres=> REVOKE ALL ON DATABASE lab_db FROM public;
   REVOKE
   ```

1. Create a role for database users\.

   ```
   postgres=> CREATE ROLE lab_tech;
   CREATE ROLE
   ```

1. Give database users that have this role the ability to connect to the database\.

   ```
   postgres=> GRANT CONNECT ON DATABASE lab_db TO lab_tech;
   GRANT
   ```

1. Grant all users with the `lab_tech` role all privileges on this database\.

   ```
   postgres=> GRANT ALL PRIVILEGES ON DATABASE lab_db TO lab_tech;
   GRANT
   ```

1. Create database users, as follows:

   ```
   postgres=> CREATE ROLE lab_user1 LOGIN PASSWORD 'change_me';
   CREATE ROLE
   postgres=> CREATE ROLE lab_user2 LOGIN PASSWORD 'change_me';
   CREATE ROLE
   ```

1. Grant these two users the privileges associated with the lab\_tech role:

   ```
   postgres=> GRANT lab_tech TO lab_user1;
   GRANT ROLE
   postgres=> GRANT lab_tech TO lab_user2;
   GRANT ROLE
   ```

At this point, `lab_user1` and `lab_user2` can connect to the `lab_db` database\. This example doesn't follow best practices for enterprise usage, which might include creating multiple database instances, different schemas, and granting limited permissions\. For more complete information and additional scenarios, see [Managing PostgreSQL Users and Roles](http://aws.amazon.com/blogs/database/managing-postgresql-users-and-roles/)\. 

For more information about privileges in PostgreSQL databases, see the [GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html) command in the PostgreSQL documentation\.

### Delegating and controlling user password management<a name="Appendix.PostgreSQL.CommonDBATasks.RestrictPasswordMgmt"></a>

As a DBA, you might want to delegate the management of user passwords\. Or, you might want to prevent database users from changing their passwords or reconfiguring password constraints, such as password lifetime\. To ensure that only the database users that you choose can change password settings, you can turn on the restricted password management feature\. When you activate this feature, only those database users that have been granted the `rds_password` role can manage passwords\. 

**Note**  
To use restricted password management, your RDS for PostgreSQL DB instance must be running PostgreSQL 10\.6 or higher\.

By default, this feature is `off`, as shown in the following:

```
postgres=> SHOW rds.restrict_password_commands;
  rds.restrict_password_commands
--------------------------------
 off
(1 row)
```

To turn on this feature, you use a custom parameter group and change the setting for `rds.restrict_password_commands` to 1\. Be sure to reboot your RDS for PostgreSQL DB instance so that the setting takes effect\. 

With this feature active, `rds_password` privileges are needed for the following SQL commands:

```
CREATE ROLE myrole WITH PASSWORD 'mypassword';
CREATE ROLE myrole WITH PASSWORD 'mypassword' VALID UNTIL '2023-01-01';
ALTER ROLE myrole WITH PASSWORD 'mypassword' VALID UNTIL '2023-01-01';
ALTER ROLE myrole WITH PASSWORD 'mypassword';
ALTER ROLE myrole VALID UNTIL '2023-01-01';
ALTER ROLE myrole RENAME TO myrole2;
```

Renaming a role \(`ALTER ROLE myrole RENAME TO newname`\) is also restricted if the password uses the MD5 hashing algorithm\. 

With this feature active, attempting any of these SQL commands without the `rds_password` role permissions generates the following error: 

```
ERROR: must be a member of rds_password to alter passwords
```

We recommend that you grant the `rds_password` to only a few roles that you use solely for password management\. If you grant `rds_password` privileges to database users that don't have `rds_superuser` privileges, you need to also grant them the `CREATEROLE` attribute\.

Make sure that you verify password requirements such as expiration and needed complexity on the client side\. If you use your own client\-side utility for password related changes, the utility needs to be a member of `rds_password` and have `CREATE ROLE` privileges\. 

## Working with logging mechanisms supported by RDS for PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks.Auditing"></a>

There are several parameters, extensions, and other configurable items that you can set to log activities that occur on your PostgreSQL DB instance\. These include the following:
+ The `log_statement` parameter can be used to log user activity in your PostgreSQL database\. To learn more about RDS for PostgreSQL logging and how to monitor the logs, see [PostgreSQL database log files](USER_LogAccess.Concepts.PostgreSQL.md)\.
+ The `rds.force_admin_logging_level` parameter logs actions by the Amazon RDS internal user \(rdsadmin\) in the databases on the DB instance\. It writes the output to the PostgreSQL error log\. Allowed values are `disabled`, `debug5`, `debug4`, `debug3`, `debug2`, `debug1`, `info`, `notice`, `warning`, `error`, log, `fatal`, and `panic`\. The default value is `disabled`\.
+ The `rds.force_autovacuum_logging_level` parameter can be set to capture various autovacuum operations in the PostgreSQL error log\. For more information, see [Logging autovacuum and vacuum activities](Appendix.PostgreSQL.CommonDBATasks.Autovacuum.md#Appendix.PostgreSQL.CommonDBATasks.Autovacuum.Logging)\. 
+ The PostgreSQL Audit \(pgAudit\) extension can be installed and configured to capture activities at the session level or at the object level\. For more information, see [Logging at the session and object level with the pgAudit extension](Appendix.PostgreSQL.CommonDBATasks.Extensions.md#Appendix.PostgreSQL.CommonDBATasks.pgaudit)\.
+ The `log_fdw` extension makes it possible for you to access the database engine log using SQL\. For more information, see [Using the log\_fdw extension to access the DB log using SQL](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md#CHAP_PostgreSQL.Extensions.log_fdw)\.
+ The `pg_stat_statements` library is specified as the default for the `shared_preload_libraries` parameter in RDS for PostgreSQL version 10 and higher\. It's this library that you can use to analyze running queries\. Be sure that `pg_stat_statements` is set in your DB parameter group\. For more information about monitoring your RDS for PostgreSQL DB instance using the information that this library provides, see [SQL statistics for RDS PostgreSQL](USER_PerfInsights.UsingDashboard.AnalyzeDBLoad.AdditionalMetrics.PostgreSQL.md)\.

In general terms, the point of logging is so that the DBA can monitor, tune performance, and troubleshoot\. Many of the logs are uploaded automatically to Amazon CloudWatch or Performance Insights\. Here, they're sorted and grouped to provide complete metrics for your DB instance\. To learn more about Amazon RDS monitoring and metrics, see [Monitoring metrics in an Amazon RDS instance](CHAP_Monitoring.md)\. 

## Using pgBadger for log analysis with PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks.Badger"></a>

You can use a log analyzer such as [pgBadger](http://dalibo.github.io/pgbadger/) to analyze PostgreSQL logs\. The pgBadger documentation states that the %l pattern \(the log line for the session or process\) should be a part of the prefix\. However, if you provide the current RDS `log_line_prefix` as a parameter to pgBadger it should still produce a report\.

For example, the following command correctly formats an Amazon RDS for PostgreSQL log file dated 2014\-02\-04 using pgBadger\.

```
./pgbadger -f stderr -p '%t:%r:%u@%d:[%p]:' postgresql.log.2014-02-04-00 
```