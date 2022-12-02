# Using PostgreSQL extensions with Amazon RDS for PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks.Extensions"></a>

You can extend the functionality of PostgreSQL by installing a variety of extensions and modules\. For example, to work with spatial data you can install and use the PostGIS extension\. For more information, see [Managing spatial data with the PostGIS extension](Appendix.PostgreSQL.CommonDBATasks.PostGIS.md)\. As another example, if you want to improve data entry for very large tables, you can consider partitioning your data by using the `pg_partman` extension\. To learn more, see [Managing PostgreSQL partitions with the pg\_partman extension](PostgreSQL_Partitions.md)\.

**Note**  
As of RDS for PostgreSQL 14\.5, RDS for PostgreSQL supports Trusted Language Extensions for PostgreSQL\. This feature is implemented as the extension `pg_tle`, which you can add to your RDS for PostgreSQL DB instance\. By using this extension, developers can create their own PostgreSQL extensions in a safe environment that simplifies the setup and configuration requirements\. For more information, see [Working with Trusted Language Extensions for PostgreSQL](PostgreSQL_trusted_language_extension.md)\.

In some cases, rather than installing an extension, you might add a specific module to the list of `shared_preload_libraries` in your RDS for PostgreSQL DB instance's custom DB parameter group\. Typically, the default DB cluster parameter group loads only the `pg_stat_statements`, but several other modules are available to add to the list\. For example, you can add scheduling capability by adding the `pg_cron` module, as detailed in [Scheduling maintenance with the PostgreSQL pg\_cron extension](PostgreSQL_pg_cron.md)\. As another example, you can log query execution plans by loading the `auto_explain` module\. To learn more, see [Logging execution plans of queries](https://aws.amazon.com/premiumsupport/knowledge-center/rds-postgresql-tune-query-performance/#) in the AWS knowledge center\.

Depending on your version of RDS for PostgreSQL, installing an extension might require `rds_superuser` permissions, as follows: 
+ For RDS for PostgreSQL versions 12 and earlier versions, installing extensions requires `rds_superuser` privileges\.
+ For RDS for PostgreSQL version 13 and higher versions, users \(roles\) with create permissions on a given database instance can install and use any *trusted extensions*\. For a list of trusted extensions, see [PostgreSQL trusted extensions](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.Extensions.Trusted)\. 

You can also specify precisely which extensions can be installed on your RDS for PostgreSQL DB instance, by listing them in the `rds.allowed_extensions` parameter\. By default, this parameter isn't set, so any supported extension can be added if the user has permissions to do so\. By adding a list of extensions to this parameter, you explicitly identify the extensions that your RDS for PostgreSQL DB instance can use\. Any extensions not listed can't be installed\. This capability is available for the following versions:
+ RDS for PostgreSQL 14\.1 and all higher versions
+ RDS for PostgreSQL 13\.3 and higher minor versions 
+ RDS for PostgreSQL 12\.7 and higher minor versions 

For more information, see [Restricting installation of PostgreSQL extensions](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.FeatureSupport.Extensions.Restriction)\. 

To learn more about the `rds_superuser` role, see [Understanding PostgreSQL roles and permissions](Appendix.PostgreSQL.CommonDBATasks.Roles.md)\.

**Topics**
+ [Using functions from the orafce extension](#Appendix.PostgreSQL.CommonDBATasks.orafce)
+ [Managing PostgreSQL partitions with the pg\_partman extension](PostgreSQL_Partitions.md)
+ [Using pgAudit to log database activity](#Appendix.PostgreSQL.CommonDBATasks.pgaudit)
+ [Scheduling maintenance with the PostgreSQL pg\_cron extension](PostgreSQL_pg_cron.md)
+ [Using pglogical to synchronize data across instances](#Appendix.PostgreSQL.CommonDBATasks.pglogical)
+ [Reducing bloat in tables and indexes with the pg\_repack extension](#Appendix.PostgreSQL.CommonDBATasks.pg_repack)
+ [Upgrading and using the PLV8 extension](#PostgreSQL.Concepts.General.UpgradingPLv8)
+ [Managing spatial data with the PostGIS extension](Appendix.PostgreSQL.CommonDBATasks.PostGIS.md)

## Using functions from the orafce extension<a name="Appendix.PostgreSQL.CommonDBATasks.orafce"></a>

The orafce extension provides functions and operators that emulate a subset of functions and packages from an Oracle database\. The orafce extension makes it easier for you to port an Oracle application to PostgreSQL\. RDS for PostgreSQL versions 9\.6\.6 and higher support this extension\. For more information about orafce, see [orafce](https://github.com/orafce/orafce) on GitHub\.

**Note**  
RDS for PostgreSQL doesn't support the `utl_file` package that is part of the orafce extension\. This is because the `utl_file` schema functions provide read and write operations on operating\-system text files, which requires superuser access to the underlying host\. As a managed service, RDS for PostgreSQL doesn't provide host access\.

**To use the orafce extension**

1. Connect to the DB instance with the primary user name that you used to create the DB instance\. 

   If you want to turn on orafce for a different database in the same DB instance, use the `/c dbname` psql command\. Using this command, you change from the primary database after initiating the connection\.

1. Turn on the orafce extension with the `CREATE EXTENSION` statement\.

   ```
   CREATE EXTENSION orafce;
   ```

1. Transfer ownership of the oracle schema to the rds\_superuser role with the `ALTER SCHEMA` statement\.

   ```
   ALTER SCHEMA oracle OWNER TO rds_superuser;
   ```

   If you want to see the list of owners for the oracle schema, use the `\dn` psql command\.

## Using pgAudit to log database activity<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit"></a>

Financial institutions, government agencies, and many industries need to keep *audit logs* to meet regulatory requirements\. By using the PostgreSQL Audit extension \(pgAudit\) with your RDS for PostgreSQL DB instance, you can capture the detailed records that are typically needed by auditors or to meet regulatory requirements\. For example, you can set up the pgAudit extension to track changes made to specific databases and tables, to record the user who made the change, and many other details\.

The pgAudit extension builds on the functionality of the native PostgreSQL logging infrastructure by extending the log messages with more detail\. In other words, you use the same approach to view your audit log as you do to view any log messages\. For more information about PostgreSQL logging, see [RDS for PostgreSQL database log files](USER_LogAccess.Concepts.PostgreSQL.md)\. 

The pgAudit extension redacts sensitive data such as cleartext passwords from the logs\. If your RDS for PostgreSQL DB instance is configured to log data manipulation language \(DML\) statements as detailed in [Turning on query logging for your RDS for PostgreSQL DB instance](USER_LogAccess.Concepts.PostgreSQL.md#USER_LogAccess.Concepts.PostgreSQL.Query_Logging), you can avoid the cleartext password issue by using the PostgreSQL Audit extension\. 

You can configure auditing on your database instances with a great degree of specificity\. You can audit all databases and all users\. Or, you can choose to audit only certain databases, users, and other objects\. You can also explicitly exclude certain users and databases from being audited\. For more information, see [Excluding users or databases from audit logging](#Appendix.PostgreSQL.CommonDBATasks.pgaudit.exclude-user-db)\. 

Given the amount of detail that can be captured, we recommend that if you do use pgAudit, you monitor your storage consumption\. 

The pgAudit extension is supported on all available RDS for PostgreSQL versions\. For a list of pgAudit versions supported by available RDS for PostgreSQL versions, see [Extension versions for Amazon RDS for PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html) in the *Amazon RDS for PostgreSQL Release Notes\.* 

**Topics**
+ [Setting up the pgAudit extension](#Appendix.PostgreSQL.CommonDBATasks.pgaudit.basic-setup)
+ [Auditing database objects](#Appendix.PostgreSQL.CommonDBATasks.pgaudit.auditing)
+ [Excluding users or databases from audit logging](#Appendix.PostgreSQL.CommonDBATasks.pgaudit.exclude-user-db)
+ [Reference for the pgAudit extension](#Appendix.PostgreSQL.CommonDBATasks.pgaudit.reference)

### Setting up the pgAudit extension<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit.basic-setup"></a>

To set up the pgAudit extension on your RDS for PostgreSQL DB instance , you first add pgAudit to the shared libraries on the custom DB parameter group for your RDS for PostgreSQL DB instance\. For information about creating a custom DB parameter group, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. Next, you install the pgAudit extension\. Finally, you specify the databases and objects that you want to audit\. The procedures in this section show you how\. You can use the AWS Management Console or the AWS CLI\. 

You must have permissions as the `rds_superuser` role to perform all these tasks\.

The steps following assume that your RDS for PostgreSQL DB instance is associated with a custom DB parameter group\. 

#### Console<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit.basic-setup.CON"></a>

**To set up the pgAudit extension**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose your RDS for PostgreSQL DB instance\.

1. Open the **Configuration** tab for your RDS for PostgreSQL DB instance\. Among the Instance details, find the **Parameter group** link\. 

1. Choose the link to open the custom parameters associated with your RDS for PostgreSQL DB instance\. 

1. In the **Parameters** search field, type `shared_pre` to find the `shared_preload_libraries` parameter\.

1. Choose **Edit parameters** to access the property values\.

1. Add `pgaudit` to the list in the **Values** field\. Use a comma to separate items in the list of values\.   
![\[Image of the shared_preload_libaries parameter with pgAudit added.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/apg_rpg_shared_preload_pgaudit.png)

1. Reboot the RDS for PostgreSQL DB instance so that your change to the `shared_preload_libraries` parameter takes effect\. 

1. When the instance is available, verify that pgAudit has been initialized\. Use `psql` to connect to the RDS for PostgreSQL DB instance, and then run the following command\.

   ```
   SHOW shared_preload_libraries;
   shared_preload_libraries 
   --------------------------
   rdsutils,pgaudit
   (1 row)
   ```

1. With pgAudit initialized, you can now create the extension\. You need to create the extension after initializing the library because the `pgaudit` extension installs event triggers for auditing data definition language \(DDL\) statements\. 

   ```
   CREATE EXTENSION pgaudit;
   ```

1. Close the `psql` session\.

   ```
   labdb=> \q
   ```

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Find the `pgaudit.log` parameter in the list and set to the appropriate value for your use case\. For example, setting the `pgaudit.log` parameter to `write` as shown in the following image captures inserts, updates, deletes, and some other types changes to the log\.   
![\[Image of the pgaudit.log parameter with setting.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rpg_set_pgaudit-log-level.png)

   You can also choose one of the following values for the `pgaudit.log` parameter\.
   + none – This is the default\. No database changes are logged\. 
   + all – Logs everything \(read, write, function, role, ddl, misc\)\. 
   + ddl – Logs all data definition language \(DDL\) statements that aren't included in the `ROLE` class\.
   + function – Logs function calls and `DO` blocks\.
   + misc – Logs miscellaneous commands, such as `DISCARD`, `FETCH`, `CHECKPOINT`, `VACUUM`, and `SET`\.
   + read – Logs `SELECT` and `COPY` when the source is a relation \(such as a table\) or a query\.
   + role – Logs statements related to roles and privileges, such as `GRANT`, `REVOKE`, `CREATE ROLE`, `ALTER ROLE`, and `DROP ROLE`\.
   + write – Logs `INSERT`, `UPDATE`, `DELETE`, `TRUNCATE`, and `COPY` when the destination is a relation \(table\)\.

1. Choose **Save changes**\.

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose your RDS for PostgreSQL DB instance from the Databases list to select it, and then choose **Reboot** from the Actions menu\.

#### AWS CLI<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit.basic-setup.CLI"></a>

**To setup pgAudit**

To setup pgAudit using the AWS CLI, you call the [modify\-db\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html) operation to modify the audit log parameters in your custom parameter group, as shown in the following procedure\.

1. Use the following AWS CLI command to add `pgaudit` to the `shared_preload_libraries` parameter\.

   ```
   aws rds modify-db-parameter-group \
      --db-parameter-group-name custom-param-group-name \
      --parameters "ParameterName=shared_preload_libraries,ParameterValue=pgaudit,ApplyMethod=pending-reboot" \
      --region aws-region
   ```

1. Use the following AWS CLI command to reboot the RDS for PostgreSQL DB instance so that the pgaudit library is initialized\.

   ```
   aws rds reboot-db-instance \
       --db-instance-identifier your-instance \
       --region aws-region
   ```

1. When the instance is available, you can verify that `pgaudit` has been initialized\. Use `psql` to connect to the RDS for PostgreSQL DB instance, and then run the following command\.

   ```
   SHOW shared_preload_libraries;
   shared_preload_libraries 
   --------------------------
   rdsutils,pgaudit
   (1 row)
   ```

   With pgAudit initialized, you can now create the extension\.

   ```
   CREATE EXTENSION pgaudit;
   ```

1. Close the `psql` session so that you can use the AWS CLI\.

   ```
   labdb=> \q
   ```

1. Use the following AWS CLI command to specify the classes of statement that want logged by session audit logging\. The example sets the `pgaudit.log` parameter to `write`, which captures inserts, updates, and deletes to the log\.

   ```
   aws rds modify-db-parameter-group \
      --db-parameter-group-name custom-param-group-name \
      --parameters "ParameterName=pgaudit.log,ParameterValue=write,ApplyMethod=pending-reboot" \
      --region aws-region
   ```

   You can also choose one of the following values for the `pgaudit.log` parameter\.
   + none – This is the default\. No database changes are logged\. 
   + all – Logs everything \(read, write, function, role, ddl, misc\)\. 
   + ddl – Logs all data definition language \(DDL\) statements that aren't included in the `ROLE` class\.
   + function – Logs function calls and `DO` blocks\.
   + misc – Logs miscellaneous commands, such as `DISCARD`, `FETCH`, `CHECKPOINT`, `VACUUM`, and `SET`\.
   + read – Logs `SELECT` and `COPY` when the source is a relation \(such as a table\) or a query\.
   + role – Logs statements related to roles and privileges, such as `GRANT`, `REVOKE`, `CREATE ROLE`, `ALTER ROLE`, and `DROP ROLE`\.
   + write – Logs `INSERT`, `UPDATE`, `DELETE`, `TRUNCATE`, and `COPY` when the destination is a relation \(table\)\.

   Reboot the RDS for PostgreSQL DB instance using the following AWS CLI command\.

   ```
   aws rds reboot-db-instance \
       --db-instance-identifier your-instance \
       --region aws-region
   ```

### Auditing database objects<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit.auditing"></a>

With pgAudit set up on your RDS for PostgreSQL DB instance and configured for your requirements, more detailed information is captured in the PostgreSQL log\. For example, while the default PostgreSQL logging configuration identifies the date and time that a change was made in a database table, with the pgAudit extension the log entry can include the schema, user who made the change, and other details depending on how the extension parameters are configured\. You can set up auditing to track changes in the following ways\.
+ For each session, by user\. For the session level, you can capture the fully qualified command text\.
+ For each object, by user and by database\. 

The object auditing capability is activated when you create the `rds_pgaudit` role on your system and then add this role to the `pgaudit.role` parameter in your custom parameter parameter group\. By default, the `pgaudit.role` parameter is unset and the only allowable value is `rds_pgaudit`\. The following steps assume that `pgaudit` has been initialized and that you have created the `pgaudit` extension by following the procedure in [Setting up the pgAudit extension](#Appendix.PostgreSQL.CommonDBATasks.pgaudit.basic-setup)\. 

![\[Image of the PostgreSQL log file after setting up pgAudit.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/pgaudit-log-example.png)

As shown in this example, the "LOG: AUDIT: SESSION" line provides information about the table and its schema, among other details\. 

**To set up object auditing**

1. Use `psql` to connect to the RDS for PostgreSQL DB instance\.

   ```
   psql --host=your-instance-name.aws-region.rds.amazonaws.com --port=5432 --username=postgrespostgres --password --dbname=labdb
   ```

1. Create a database role named `rds_pgaudit` using the following command\.

   ```
   labdb=> CREATE ROLE rds_pgaudit;
   CREATE ROLE
   labdb=>
   ```

1. Close the `psql` session\.

   ```
   labdb=> \q
   ```

   In the next few steps, use the AWS CLI to modify the audit log parameters in your custom parameter group\. 

1. Use the following AWS CLI command to set the `pgaudit.role` parameter to `rds_pgaudit`\. By default, this parameter is empty, and `rds_pgaudit` is the only allowable value\.

   ```
   aws rds modify-db-parameter-group \
      --db-parameter-group-name custom-param-group-name \
      --parameters "ParameterName=pgaudit.role,ParameterValue=rds_pgaudit,ApplyMethod=pending-reboot" \
      --region aws-region
   ```

1. Use the following AWS CLI command to reboot the RDS for PostgreSQL DB instance so that your changes to the parameters take effect\.

   ```
   aws rds reboot-db-instance \
       --db-instance-identifier your-instance \
       --region aws-region
   ```

1. Run the following command to confirm that the `pgaudit.role` is set to `rds_pgaudit`\.

   ```
   SHOW pgaudit.role;
   pgaudit.role 
   ------------------
   rds_pgaudit
   ```

To test pgAudit logging, you can run several example commands that you want to audit\. For example, you might run the following commands\.

```
CREATE TABLE t1 (id int);
GRANT SELECT ON t1 TO rds_pgaudit;
SELECT * FROM t1;
id 
----
(0 rows)
```

The database logs should contain an entry similar to the following\.

```
...
2017-06-12 19:09:49 UTC:...:rds_test@postgres:[11701]:LOG: AUDIT:
OBJECT,1,1,READ,SELECT,TABLE,public.t1,select * from t1;
...
```

For information on viewing the logs, see [Monitoring Amazon RDS log files](USER_LogAccess.md)\.

To learn more about the pgAudit extension, see [pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) on GitHub\.

### Excluding users or databases from audit logging<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit.exclude-user-db"></a>

As discussed in [RDS for PostgreSQL database log files](USER_LogAccess.Concepts.PostgreSQL.md), PostgreSQL logs consume storage space\. Using the pgAudit extension adds to the volume of data gathered in your logs to varying degrees, depending on the changes that you track\. You might not need to audit every user or database in your RDS for PostgreSQL DB instance\.

To minimize impacts to your storage and to avoid needlessly capturing audit records, you can exclude users and databases from being audited\. You can also change logging within a given session\. The following examples show you how\. 

**Note**  
Parameter settings at the session level take precedence over the settings in the custom DB parameter group for the RDS for PostgreSQL DB instance\. If you don't want database users to bypass your audit logging configuration settings, be sure to change their permissions\. 

Suppose that your RDS for PostgreSQL DB instance is configured to audit the same level of activity for all users and databases\. You then decide that you don't want to audit the user `myuser`\. You can turn off auditing for `myuser` with the following SQL command\.

```
ALTER USER myuser SET pgaudit.log TO 'NONE';
```

Then, you can use the following query to check the `user_specific_settings` column for `pgaudit.log` to confirm that the parameter is set to `NONE`\.

```
SELECT
    usename AS user_name,
    useconfig AS user_specific_settings
FROM
    pg_user
WHERE
    usename = 'myuser';
```

You see output such as the following\.

```
 user_name | user_specific_settings
-----------+------------------------
 myuser    | {pgaudit.log=NONE}
(1 row)
```

You can turn off logging for a given user in the midst of their session with the database with the following command\.

```
ALTER USER myuser IN DATABASE mydatabase SET pgaudit.log TO 'none';
```

Use the following query to check the settings column for pgaudit\.log for a specific user and database combination\. 

```
SELECT
    usename AS "user_name",
    datname AS "database_name",
    pg_catalog.array_to_string(setconfig, E'\n') AS "settings"
FROM
    pg_catalog.pg_db_role_setting s
    LEFT JOIN pg_catalog.pg_database d ON d.oid = setdatabase
    LEFT JOIN pg_catalog.pg_user r ON r.usesysid = setrole
WHERE
    usename = 'myuser'
    AND datname = 'mydatabase'
ORDER BY
    1,
    2;
```

You see output similar to the following\.

```
  user_name | database_name |     settings
-----------+---------------+------------------
 myuser    | mydatabase    | pgaudit.log=none
(1 row)
```

After turning off auditing for `myuser`, you decide that you don't want to track changes to `mydatabase`\. You turn off auditing for that specific database by using the following command\.

```
ALTER DATABASE mydatabase SET pgaudit.log to 'NONE';
```

Then, use the following query to check the database\_specific\_settings column to confirm that pgaudit\.log is set to NONE\.

```
SELECT
a.datname AS database_name,
b.setconfig AS database_specific_settings
FROM
pg_database a
FULL JOIN pg_db_role_setting b ON a.oid = b.setdatabase
WHERE
a.datname = 'mydatabase';
```

You see output such as the following\.

```
 database_name | database_specific_settings
---------------+----------------------------
 mydatabase    | {pgaudit.log=NONE}
(1 row)
```

To return settings to the default setting for myuser, use the following command:

```
ALTER USER myuser RESET pgaudit.log;
```

To return settings to their default setting for a database, use the following command\.

```
ALTER DATABASE mydatabase RESET pgaudit.log;
```

To reset user and database to the default setting, use the following command\.

```
ALTER USER myuser IN DATABASE mydatabase RESET pgaudit.log;
```

You can also capture specific events to the log by setting the `pgaudit.log` to one of the other allowed values for the `pgaudit.log` parameter\. For more information, see [List of allowable settings for the `pgaudit.log` parameter](#Appendix.PostgreSQL.CommonDBATasks.pgaudit.reference.pgaudit-log-settings)\.

```
ALTER USER myuser SET pgaudit.log TO 'read';
ALTER DATABASE mydatabase SET pgaudit.log TO 'function';
ALTER USER myuser IN DATABASE mydatabase SET pgaudit.log TO 'read,function'
```

### Reference for the pgAudit extension<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit.reference"></a>

You can specify the level of detail that you want for your audit log by changing one or more of the parameters listed in this section\. 

#### Controlling pgAudit behavior<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit.reference.basic-setup.parameters"></a>

You can control the audit logging by changing one or more of the parameters listed in the following table\. 


| Parameter | Description | 
| --- | --- | 
| `pgaudit.log`  | Specifies the statement classes that will be logged by session audit logging\. Allowable values include ddl, function, misc, read, role, write, none, all\. For more information, see [List of allowable settings for the `pgaudit.log` parameter](#Appendix.PostgreSQL.CommonDBATasks.pgaudit.reference.pgaudit-log-settings)\.  | 
| `pgaudit.log_catalog` | When turned on \(set to 1\), adds statements to audit trail if all relations in a statement are in pg\_catalog\. | 
| `pgaudit.log_level` | Specifies the log level to use for log entries\. Allowed values: debug5, debug4, debug3, debug2, debug1, info, notice, warning, log | 
| `pgaudit.log_parameter` | When turned on \(set to 1\), parameters passed with the statement are captured in the audit log\. | 
| `pgaudit.log_relation` | When turned on \(set to 1\), the audit log for the session creates a separate log entry for each relation \(TABLE, VIEW, and so on\) referenced in a SELECT or DML statement\. | 
| `pgaudit.log_statement_once` | Specifies whether logging will include the statement text and parameters with the first log entry for a statement/substatement combination or with every entry\. | 
| `pgaudit.role` | Specifies the master role to use for object audit logging\. The only allowable entry is `rds_pgaudit`\. | 

#### List of allowable settings for the `pgaudit.log` parameter<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit.reference.pgaudit-log-settings"></a>

 


| Value | Description | 
| --- | --- | 
| none | This is the default\. No database changes are logged\.  | 
| all | Logs everything \(read, write, function, role, ddl, misc\)\.  | 
| ddl | Logs all data definition language \(DDL\) statements that aren't included in the `ROLE` class\. | 
| function | Logs function calls and `DO` blocks\. | 
| misc | Logs miscellaneous commands, such as `DISCARD`, `FETCH`, `CHECKPOINT`, `VACUUM`, and `SET`\. | 
| read | Logs `SELECT` and `COPY` when the source is a relation \(such as a table\) or a query\. | 
| role | Logs statements related to roles and privileges, such as `GRANT`, `REVOKE`, `CREATE ROLE`, `ALTER ROLE`, and `DROP ROLE`\. | 
| write | Logs `INSERT`, `UPDATE`, `DELETE`, `TRUNCATE`, and `COPY` when the destination is a relation \(table\)\. | 

To log multiple event types with session auditing, use a comma\-separated list\. To log all event types, set `pgaudit.log` to `ALL`\. Reboot your DB instance to apply the changes\.

With object auditing, you can refine audit logging to work with specific relations\. For example, you can specify that you want audit logging for `READ` operations on one or more tables\.

## Using pglogical to synchronize data across instances<a name="Appendix.PostgreSQL.CommonDBATasks.pglogical"></a>

All currently available RDS for PostgreSQL versions support the `pglogical` extension\. The pglogical extension predates the functionally similar logical replication feature that was introduced by PostgreSQL in version 10\. For more information, see [Performing logical replication for Amazon RDS for PostgreSQL](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.FeatureSupport.LogicalReplication)\.

The `pglogical` extension supports logical replication between two or more RDS for PostgreSQL DB instances\. It also supports replication between different PostgreSQL versions, and between databases running on RDS for PostgreSQL DB instances and Aurora PostgreSQL DB clusters\. The `pglogical` extension uses a publish\-subscribe model to replicate changes to tables and other objects, such as sequences, from a publisher to a subscriber\. It relies on a replication slot to ensure that changes are synchronized from a publisher node to a subscriber node, defined as follows\. 
+ The *publisher node* is the RDS for PostgreSQL DB instance that's the source of data to be replicated to other nodes\. The publisher node defines the tables to be replicated in a publication set\. 
+ The *subscriber node* is the RDS for PostgreSQL DB instance that receives WAL updates from the publisher\. The subscriber creates a subscription to connect to the publisher and get the decoded WAL data\. When the subscriber creates the subscription, the replication slot is created on the publisher node\. 

Following, you can find information about setting up the `pglogical` extension\. 

**Topics**
+ [Requirements and limitations for the pglogical extension](#Appendix.PostgreSQL.CommonDBATasks.pglogical.requirements-limitations)
+ [Setting up the pglogical extension](#Appendix.PostgreSQL.CommonDBATasks.pglogical.basic-setup)
+ [Setting up logical replication for RDS for PostgreSQL DB instance](#Appendix.PostgreSQL.CommonDBATasks.pglogical.setup-replication)
+ [Reestablishing logical replication after a major upgrade](#Appendix.PostgreSQL.CommonDBATasks.pglogical.recover-replication-after-upgrade)
+ [Managing logical replication slots for RDS for PostgreSQL](#Appendix.PostgreSQL.CommonDBATasks.pglogical.handle-slots)
+ [Parameter reference for the pglogical extension](#Appendix.PostgreSQL.CommonDBATasks.pglogical.reference)

### Requirements and limitations for the pglogical extension<a name="Appendix.PostgreSQL.CommonDBATasks.pglogical.requirements-limitations"></a>

All currently available releases of RDS for PostgreSQL support the `pglogical` extension\. 

Both the publisher node and the subscriber node must be set up for logical replication\.

The tables that you want to replicate from subscriber to publisher must have the same names and the same schema\. These tables must also contain the same columns, and the columns must use the same data types\. Both publisher and subscriber tables must have the same primary keys\. We recommend that you use only the PRIMARY KEY as the unique constraint\.

The tables on the subscriber node can have more permissive constraints than those on the publisher node for CHECK constraints and NOT NULL constraints\. 

The `pglogical` extension provides features such as two\-way replication that aren't supported by the logical replication feature built into PostgreSQL \(version 10 and higher\)\. For more information, see [PostgreSQL bi\-directional replication using pglogical](https://aws.amazon.com/blogs/database/postgresql-bi-directional-replication-using-pglogical/)\.

### Setting up the pglogical extension<a name="Appendix.PostgreSQL.CommonDBATasks.pglogical.basic-setup"></a>

To set up the `pglogical` extension on your RDS for PostgreSQL DB instance , you add `pglogical` to the shared libraries on the custom DB parameter group for your RDS for PostgreSQL DB instance\. You also need to set the value of the `rds.logical_replication` parameter to `1`, to turn on logical decoding\. Finally, you create the extension in the database\. You can use the AWS Management Console or the AWS CLI for these tasks\. 

You must have permissions as the `rds_superuser` role to perform these tasks\.

The steps following assume that your RDS for PostgreSQL DB instance is associated with a custom DB parameter group\. For information about creating a custom DB parameter group, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

#### Console<a name="Appendix.PostgreSQL.CommonDBATasks.pglogical.basic-setup.CON"></a>

**To set up the pglogical extension**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose your RDS for PostgreSQL DB instance\.

1. Open the **Configuration** tab for your RDS for PostgreSQL DB instance\. Among the Instance details, find the **Parameter group** link\. 

1. Choose the link to open the custom parameters associated with your RDS for PostgreSQL DB instance\. 

1. In the **Parameters** search field, type `shared_pre` to find the `shared_preload_libraries` parameter\.

1. Choose **Edit parameters** to access the property values\.

1. Add `pglogical` to the list in the **Values** field\. Use a comma to separate items in the list of values\.   
![\[Image of the shared_preload_libraries parameter with pglogical added.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/apg_rpg_shared_preload_pglogical.png)

1. Find the `rds.logical_replication` parameter and set it to `1`, to turn on logical replication\.

1. Reboot the RDS for PostgreSQL DB instance so that your changes take effect\. 

1. When the instance is available, you can use `psql` \(or pgAdmin\) to connect to the RDS for PostgreSQL DB instance\. 

   ```
   psql --host=111122223333.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password --dbname=labdb
   ```

1. To verify that pglogical is initialized, run the following command\.

   ```
   SHOW shared_preload_libraries;
   shared_preload_libraries 
   --------------------------
   rdsutils,pglogical
   (1 row)
   ```

1. Verify the setting that enables logical decoding, as follows\.

   ```
   SHOW wal_level;
   wal_level
   -----------
    logical
   (1 row)
   ```

1. Create the extension, as follows\.

   ```
   CREATE EXTENSION pglogical;
   EXTENSION CREATED
   ```

1. Choose **Save changes**\.

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose your RDS for PostgreSQL DB instance from the Databases list to select it, and then choose **Reboot** from the Actions menu\.

#### AWS CLI<a name="Appendix.PostgreSQL.CommonDBATasks.pglogical.basic-setup.CLI"></a>

**To setup the pglogical extension**

To setup pglogical using the AWS CLI, you call the [modify\-db\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html) operation to modify certain parameters in your custom parameter group as shown in the following procedure\.

1. Use the following AWS CLI command to add `pglogical` to the `shared_preload_libraries` parameter\.

   ```
   aws rds modify-db-parameter-group \
      --db-parameter-group-name custom-param-group-name \
      --parameters "ParameterName=shared_preload_libraries,ParameterValue=pglogical,ApplyMethod=pending-reboot" \
      --region aws-region
   ```

1. Use the following AWS CLI command to set `rds.logical_replication` to `1` to turn on the logical decoding capability for the RDS for PostgreSQL DB instance\.

   ```
   aws rds modify-db-parameter-group \
      --db-parameter-group-name custom-param-group-name \
      --parameters "ParameterName=rds.logical_replication,ParameterValue=1,ApplyMethod=pending-reboot" \
      --region aws-region
   ```

1. Use the following AWS CLI command to reboot the RDS for PostgreSQL DB instance so that the pglogical library is initialized\.

   ```
   aws rds reboot-db-instance \
       --db-instance-identifier your-instance \
       --region aws-region
   ```

1. When the instance is available, use `psql` to connect to the RDS for PostgreSQL DB instance\. 

   ```
   psql --host=111122223333.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password --dbname=labdb
   ```

1. Create the extension, as follows\.

   ```
   CREATE EXTENSION pglogical;
   EXTENSION CREATED
   ```

1. Reboot the RDS for PostgreSQL DB instance using the following AWS CLI command\.

   ```
   aws rds reboot-db-instance \
       --db-instance-identifier your-instance \
       --region aws-region
   ```

### Setting up logical replication for RDS for PostgreSQL DB instance<a name="Appendix.PostgreSQL.CommonDBATasks.pglogical.setup-replication"></a>

The following procedure shows you how to start logical replication between two RDS for PostgreSQL DB instances\. The steps assume that both the source \(publisher\) and the target \(subscriber\) have the `pglogical` extension set up as detailed in [Setting up the pglogical extension](#Appendix.PostgreSQL.CommonDBATasks.pglogical.basic-setup)\. 

**To create the publisher node and define the tables to replicate**

These steps assume that your RDS for PostgreSQL DB instance has a database that has one or more tables that you want to replicate to another node\. You need to recreate the table structure from the publisher on the subscriber, so first, get the table structure if necessary\. You can do that by using the `psq1` metacommand `\d tablename` and then creating the same table on the subscriber instance\. The following procedure creates an example table on the publisher \(source\) for demonstration purposes\.

1. Use `psql` to connect to the instance that has the table you want to use as a source for subscribers\. 

   ```
   psql --host=source-instance.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password --dbname=labdb
   ```

   If you don't have an existing table that you want to replicate, you can create a sample table as follows\.

   1. Create an example table using the following SQL statement\.

      ```
      CREATE TABLE docs_lab_table (a int PRIMARY KEY);
      ```

   1. Populate the table with generated data by using the following SQL statement\.

      ```
      INSERT INTO docs_lab_table VALUES (generate_series(1,5000));
      INSERT 0 5000
      ```

   1. Verify that data exists in the table by using the following SQL statement\.

      ```
      SELECT count(*) FROM docs_lab_table;
      ```

1. Identify this RDS for PostgreSQL DB instance as the publisher node, as follows\.

   ```
   SELECT pglogical.create_node(
       node_name := 'docs_lab_provider',
       dsn := 'host=source-instance.aws-region.rds.amazonaws.com port=5432 dbname=labdb');
    create_node
   -------------
      3410995529
   (1 row)
   ```

1. Add the table that you want to replicate to the default replication set\. For more information about replication sets, see [Replication sets](https://github.com/2ndQuadrant/pglogical/tree/REL2_x_STABLE/docs#replication-sets) in the pglogical documentation\. 

   ```
   SELECT pglogical.replication_set_add_table('default', 'docs_lab_table', 'true', NULL, NULL);
    replication_set_add_table
     ---------------------------
     t
     (1 row)
   ```

The publisher node setup is complete\. You can now set up the subscriber node to receive the updates from the publisher\.

**To set up the subscriber node and create a subscription to receive updates**

These steps assume that the RDS for PostgreSQL DB instance has been set up with the `pglogical` extension\. For more information, see [Setting up the pglogical extension](#Appendix.PostgreSQL.CommonDBATasks.pglogical.basic-setup)\. 

1. Use `psql` to connect to the instance that you want to receive updates from the publisher\.

   ```
   psql --host=target-instance.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password --dbname=labdb
   ```

1. On the subscriber RDS for PostgreSQL DB instance,create the same table that exists on the publisher\. For this example, the table is `docs_lab_table`\. You can create the table as follows\.

   ```
   CREATE TABLE docs_lab_table (a int PRIMARY KEY);
   ```

1. Verify that this table is empty\.

   ```
   SELECT count(*) FROM docs_lab_table;
    count
   -------
     0
   (1 row)
   ```

1. Identify this RDS for PostgreSQL DB instance as the subscriber node, as follows\.

   ```
   SELECT pglogical.create_node(
       node_name := 'docs_lab_target',
       dsn := 'host=target-instance.aws-region.rds.amazonaws.com port=5432 sslmode=require dbname=labdb user=postgres password=********');
    create_node
   -------------
      2182738256
   (1 row)
   ```

1. Create the subscription\. 

   ```
   SELECT pglogical.create_subscription(
      subscription_name := 'docs_lab_subscription',
      provider_dsn := 'host=source-instance.aws-region.rds.amazonaws.com port=5432 sslmode=require dbname=labdb user=postgres password=*******',
      replication_sets := ARRAY['default'],
      synchronize_data := true,
      forward_origins := '{}' );  
    create_subscription
   ---------------------
   1038357190
   (1 row)
   ```

   When you complete this step, the data from the table on the publisher is created in the table on the subscriber\. You can verify that this has occurred by using the following SQL query\.

   ```
   SELECT count(*) FROM docs_lab_table;
    count
   -------
     5000
   (1 row)
   ```

From this point forward, changes made to the table on the publisher are replicated to the table on the subscriber\.

### Reestablishing logical replication after a major upgrade<a name="Appendix.PostgreSQL.CommonDBATasks.pglogical.recover-replication-after-upgrade"></a>

Before you can perform a major version upgrade of an RDS for PostgreSQL DB instance that's set up as a publisher node for logical replication, you must drop all replication slots, even those that aren't active\. We recommend that you temporarily divert database transactions from the publisher node, drop the replication slots, upgrade the RDS for PostgreSQL DB instance, and then re\-establish and restart replication\.

The replication slots are hosted on the publisher node only\.  The RDS for PostgreSQL subscriber node in a logical replication scenario has no slots to drops, but it can't be upgraded to a major version while it's designated as a subscriber node with a subscription to the publisher\. Before upgrading the RDS for PostgreSQL subscriber node, drop the subscription and the node\. For more information, see [Managing logical replication slots for RDS for PostgreSQL](#Appendix.PostgreSQL.CommonDBATasks.pglogical.handle-slots)\.   

#### Determining that logical replication has been disrupted<a name="Appendix.PostgreSQL.CommonDBATasks.pglogical.recover-replication-after-upgrade.identifying-the-issue"></a>

You can determine that the replication process has been disrupted by querying either the publisher node or the subscriber node, as follows\.

**To check the publisher node**
+ Use `psql` to connect to the publisher node, and then query the `pg_replication_slots` function\. Note the value in the active column\. Normally, this will return `t` \(true\), showing that replication is active\. If the query returns `f` \(false\), it's an indication that replication to the subscriber has stopped\. 

  ```
  SELECT slot_name,plugin,slot_type,active FROM pg_replication_slots;
                      slot_name              |      plugin      | slot_type | active
  -------------------------------------------+------------------+-----------+--------
   pgl_labdb_docs_labcb4fa94_docs_lab3de412c | pglogical_output | logical   | f
  (1 row)
  ```

**To check the subscriber node**

On the subscriber node, you can check the status of replication in three different ways\.
+ Look through the PostgreSQL logs on the subscriber node to find failure messages\. The log identifies failure with messages that include exit code 1, as shown following\.

  ```
  2022-07-06 16:17:03 UTC::@:[7361]:LOG: background worker "pglogical apply 16404:2880255011" (PID 14610) exited with exit code 1
  2022-07-06 16:19:44 UTC::@:[7361]:LOG: background worker "pglogical apply 16404:2880255011" (PID 21783) exited with exit code 1
  ```
+ Query the `pg_replication_origin` function\. Connect to the database on the subscriber node using `psql` and query the `pg_replication_origin` function, as follows\.

  ```
  SELECT * FROM pg_replication_origin;
   roident | roname
  ---------+--------
  (0 rows)
  ```

  The empty result set means that replication has been disrupted\. Normally, you see output such as the following\.

  ```
     roident |                       roname
    ---------+----------------------------------------------------
           1 | pgl_labdb_docs_labcb4fa94_docs_lab3de412c
    (1 row)
  ```
+ Query the `pglogical.show_subscription_status` function as shown in the following example\.

  ```
  SELECT subscription_name,status,slot_name FROM pglogical.show_subscription_status();
       subscription_name | status |              slot_name
  ---====----------------+--------+-------------------------------------
   docs_lab_subscription | down   | pgl_labdb_docs_labcb4fa94_docs_lab3de412c
  (1 row)
  ```

  This output shows that replication has been disrupted\. Its status is `down`\. Normally, the output shows the status as `replicating`\.

If your logical replication process has been disrupted, you can re\-establish replication by following these steps\.

**To reestablish logical replication between publisher and subscriber nodes**

To re\-establish replication, you first disconnect the subscriber from the publisher node and then re\-establish the subscription, as outlined in these steps\. 

1. Connect to the subscriber node using `psql` as follows\.

   ```
   psql --host=222222222222.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password --dbname=labdb
   ```

1. Deactivate the subscription by using the `pglogical.alter_subscription_disable` function\.

   ```
   SELECT pglogical.alter_subscription_disable('docs_lab_subscription',true);
    alter_subscription_disable
   ----------------------------
    t
   (1 row)
   ```

1. Get the publisher node's identifier by querying the `pg_replication_origin`, as follows\.

   ```
   SELECT * FROM pg_replication_origin;
    roident |               roname
   ---------+-------------------------------------
          1 | pgl_labdb_docs_labcb4fa94_docs_lab3de412c
   (1 row)
   ```

1. Use the response from the previous step with the `pg_replication_origin_create` command to assign the identifier that can be used by the subscription when re\-established\. 

   ```
   SELECT pg_replication_origin_create('pgl_labdb_docs_labcb4fa94_docs_lab3de412c');
     pg_replication_origin_create
   ------------------------------
                               1
   (1 row)
   ```

1. Turn on the subscription by passing its name with a status of `true`, as shown in the following example\.

   ```
   SELECT pglogical.alter_subscription_enable('docs_lab_subscription',true);
     alter_subscription_enable
   ---------------------------
    t
   (1 row)
   ```

Check the status of the node\. Its status should be `replicating` as shown in this example\.

```
SELECT subscription_name,status,slot_name
  FROM pglogical.show_subscription_status();
             subscription_name |   status    |              slot_name
-------------------------------+-------------+-------------------------------------
 docs_lab_subscription         | replicating | pgl_labdb_docs_lab98f517b_docs_lab3de412c
(1 row)
```

Check the status of the subscriber's replication slot on the publisher node\. The slot's `active` column should return `t` \(true\), indicating that replication has been re\-established\.

```
SELECT slot_name,plugin,slot_type,active
  FROM pg_replication_slots;
                    slot_name              |      plugin      | slot_type | active
-------------------------------------------+------------------+-----------+--------
 pgl_labdb_docs_lab98f517b_docs_lab3de412c | pglogical_output | logical   | t
(1 row)
```

### Managing logical replication slots for RDS for PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks.pglogical.handle-slots"></a>

Before you can perform a major version upgrade on an RDS for PostgreSQL DB instance that's serving as a publisher node in a logical replication scenario, you must drop the replication slots on the instance\. The major version upgrade pre\-check process notifies you that the upgrade can't proceed until the slots are dropped\.

To drop slots from your RDS for PostgreSQL DB instance, first drop the subscription and then drop the slot\. 

To identify replication slots that were created using the `pglogical` extension, log in to each database and get the name of the nodes\. When you query the subscriber node, you get both the publisher and the subscriber nodes in the output, as shown in this example\. 

```
SELECT * FROM pglogical.node;
node_id   |     node_name
------------+-------------------
 2182738256 | docs_lab_target
 3410995529 | docs_lab_provider
(2 rows)
```

You can get the details about the subscription with the following query\.

```
SELECT sub_name,sub_slot_name,sub_target
  FROM pglogical.subscription;
 sub_name |         sub_slot_name          | sub_target
----------+--------------------------------+------------
  docs_lab_subscription     | pgl_labdb_docs_labcb4fa94_docs_lab3de412c | 2182738256
(1 row)
```

You can now drop the subscription, as follows\.

```
SELECT pglogical.drop_subscription(subscription_name := 'docs_lab_subscription');
 drop_subscription
-------------------
                 1
(1 row)
```

After dropping the subscription, you can delete the node\.

```
SELECT pglogical.drop_node(node_name := 'docs-lab-subscriber');
 drop_node
-----------
 t
(1 row)
```

You can verify that the node no longer exists, as follows\.

```
SELECT * FROM pglogical.node;
 node_id | node_name
---------+-----------
(0 rows)
```

### Parameter reference for the pglogical extension<a name="Appendix.PostgreSQL.CommonDBATasks.pglogical.reference"></a>

In the table you can find parameters associated with the `pglogical` extension\. Parameters such as `pglogical.conflict_log_level` and `pglogical.conflict_resolution` are used to handle update conflicts\. Conflicts can emerge when changes are made locally to the same tables that are subscribed to changes from the publisher\. Conflicts can also occur during various scenarios, such as two\-way replication or when multiple subscribers are replicating from the same publisher\. For more information, see [ PostgreSQL bi\-directional replication using pglogical](https://aws.amazon.com/blogs/database/postgresql-bi-directional-replication-using-pglogical/)\. 


| Parameter | Description | 
| --- | --- | 
| pglogical\.batch\_inserts | Batch inserts if possible\. Not set by default\. Change to '1' to turn on, '0' to turn off\. | 
| pglogical\.conflict\_log\_level | Sets the log level to use for logging resolved conflicts\. Supported string values are debug5, debug4, debug3, debug2, debug1, info, notice, warning, error, log, fatal, panic\. | 
| pglogical\.conflict\_resolution | Sets method to use to resolve conflicts when conflicts are resolvable\. Supported string values are error, apply\_remote, keep\_local, last\_update\_wins, first\_update\_wins\. | 
| pglogical\.extra\_connection\_options | Connection options to add to all peer node connections\. | 
| pglogical\.synchronous\_commit | pglogical specific synchronous commit value | 
| pglogical\.use\_spi | Use SPI \(server programming interface\) instead of low\-level API to apply changes\. Set to '1' to turn on, '0' to turn off\. For more information about SPI, see [Server Programming Interface](https://www.postgresql.org/docs/current/spi.html) in the PostgreSQL documentation\.  | 

## Reducing bloat in tables and indexes with the pg\_repack extension<a name="Appendix.PostgreSQL.CommonDBATasks.pg_repack"></a>

You can use the pg\_repack extension to remove bloat from tables and indexes\. This extension is supported on RDS for PostgreSQL versions 9\.6\.3 and higher\. For more information on the pg\_repack extension, see the [GitHub project documentation](https://reorg.github.io/pg_repack/)\.

**To use the pg\_repack extension**

1. Install the pg\_repack extension on your RDS for PostgreSQL DB instance by running the following command\.

   ```
   CREATE EXTENSION pg_repack;
   ```

1. Run the following commands to grant write access to repack temporary log tables created by pg\_repack\.

   ```
   ALTER DEFAULT PRIVILEGES IN SCHEMA repack GRANT INSERT ON TABLES TO PUBLIC;
   ALTER DEFAULT PRIVILEGES IN SCHEMA repack GRANT USAGE, SELECT ON SEQUENCES TO PUBLIC;
   ```

1. Connect to the database using the pg\_repack client utility\. Use an account that has `rds_superuser` privileges\. As an example, assume that `rds_test` role has `rds_superuser` privileges\. The command syntax is shown following\.

   ```
   pg_repack -h db-instance-name.111122223333.aws-region.rds.amazonaws.com -U rds_test -k postgres
   ```

   Connect using the \-k option\. The \-a option is not supported\.

1. The response from the pg\_repack client provides information on the tables on the DB instance that are repacked\.

   ```
   INFO: repacking table "pgbench_tellers"
   INFO: repacking table "pgbench_accounts"
   INFO: repacking table "pgbench_branches"
   ```

## Upgrading and using the PLV8 extension<a name="PostgreSQL.Concepts.General.UpgradingPLv8"></a>

PLV8 is a trusted Javascript language extension for PostgreSQL\. You can use it for stored procedures, triggers, and other procedural code that's callable from SQL\. This language extension is supported by all current releases of PostgreSQL\. 

If you use [PLV8](https://plv8.github.io/) and upgrade PostgreSQL to a new PLV8 version, you immediately take advantage of the new extension\. Take the following steps to synchronize your catalog metadata with the new version of PLV8\. These steps are optional, but we highly recommend that you complete them to avoid metadata mismatch warnings\.

The upgrade process drops all your existing PLV8 functions\. Thus, we recommend that you create a snapshot of your RDS for PostgreSQL DB instance before upgrading\. For more information, see [Creating a DB snapshot](USER_CreateSnapshot.md)\.

**To synchronize your catalog metadata with a new version of PLV8**

1. Verify that you need to update\. To do this, run the following command while connected to your instance\.

   ```
   SELECT * FROM pg_available_extensions WHERE name IN ('plv8','plls','plcoffee');
   ```

   If your results contain values for an installed version that is a lower number than the default version, continue with this procedure to update your extensions\. For example, the following result set indicates that you should update\.

   ```
   name    | default_version | installed_version |                     comment
   --------+-----------------+-------------------+--------------------------------------------------
   plls    | 2.1.0           | 1.5.3             | PL/LiveScript (v8) trusted procedural language
   plcoffee| 2.1.0           | 1.5.3             | PL/CoffeeScript (v8) trusted procedural language
   plv8    | 2.1.0           | 1.5.3             | PL/JavaScript (v8) trusted procedural language
   (3 rows)
   ```

1. Create a snapshot of your RDS for PostgreSQL DB instance if you haven't done so yet\. You can continue with the following steps while the snapshot is being created\. 

1. Get a count of the number of PLV8 functions in your DB instance so you can validate that they are all in place after the upgrade\. For example, the following SQL query returns the number of functions written in plv8, plcoffee, and plls\.

   ```
   SELECT proname, nspname, lanname 
   FROM pg_proc p, pg_language l, pg_namespace n
   WHERE p.prolang = l.oid
   AND n.oid = p.pronamespace
   AND lanname IN ('plv8','plcoffee','plls');
   ```

1. Use pg\_dump to create a schema\-only dump file\. For example, create a file on your client machine in the `/tmp` directory\.

   ```
   ./pg_dump -Fc --schema-only -U master postgres >/tmp/test.dmp
   ```

   This example uses the following options:  
   + `-Fc` – Custom format
   + \-\-schema\-only – Dump only the commands necessary to create schema \(functions in this case\)
   + `-U` – The RDS master user name
   + `database` – The database name for our DB instance

   For more information on pg\_dump, see [pg\_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html ) in the PostgreSQL documentation\.

1. Extract the "CREATE FUNCTION" DDL statement that is present in the dump file\. The following example uses the `grep` command to extract the DDL statement that creates the functions and save them to a file\. You use this in subsequent steps to recreate the functions\. 

   ```
   ./pg_restore -l /tmp/test.dmp | grep FUNCTION > /tmp/function_list/
   ```

   For more information on pg\_restore, see [pg\_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) in the PostgreSQL documentation\. 

1. Drop the functions and extensions\. The following example drops any PLV8 based objects\. The cascade option ensures that any dependent are dropped\.

   ```
   DROP EXTENSION pvl8 CASCADE;
   ```

   If your PostgreSQL instance contains objects based on plcoffee or plls, repeat this step for those extensions\.

1. Create the extensions\. The following example creates the plv8, plcoffee, and plls extensions\.

   ```
   CREATE EXTENSION plv8;
   CREATE EXTENSION plcoffee;
   CREATE EXTENSION plls;
   ```

1. Create the functions using the dump file and "driver" file\.

   The following example recreates the functions that you extracted previously\.

   ```
   ./pg_restore -U master -d postgres -Fc -L /tmp/function_list /tmp/test.dmp
   ```

1. Verify that all your functions have been recreated by using the following query\. 

   ```
   SELECT * FROM pg_available_extensions WHERE name IN ('plv8','plls','plcoffee'); 
   ```

   The PLV8 version 2 adds the following extra row to your result set:

   ```
       proname    |  nspname   | lanname
   ---------------+------------+----------
    plv8_version  | pg_catalog | plv8
   ```