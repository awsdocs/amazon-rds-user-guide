# MariaDB on Amazon RDS<a name="CHAP_MariaDB"></a>

Amazon RDS supports DB instances running several versions of MariaDB\. You can use the following major versions: 

+ MariaDB 10\.1

+ MariaDB 10\.0

For more information about minor version suport, see [MariaDB on Amazon RDS Versions](#MariaDB.Concepts.VersionMgmt)\. 

You first use the Amazon RDS management tools or interfaces to create an Amazon RDS MariaDB DB instance\. You can then use the Amazon RDS tools to perform management actions for the DB instance, such as reconfiguring or resizing the DB instance, authorizing connections to the DB instance, creating and restoring from backups or snapshots, creating Multi\-AZ secondaries, creating Read Replicas, and monitoring the performance of the DB instance\. You use standard MariaDB utilities and applications to store and access the data in the DB instance\. 

MariaDB is available in all of the AWS regions\. For more information about AWS Regions, see [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)\. 

You can use Amazon RDS for MariaDB databases to build HIPAA\-compliant applications\. You can store healthcare\-related information, including protected health information \(PHI\), under an executed Business Associate Agreement \(BAA\) with AWS\. For more information, see [HIPAA Compliance](https://aws.amazon.com/compliance/hipaa-compliance/)\. AWS Services in Scope have been fully assessed by a third\-party auditor and result in a certification, attestation of compliance, or Authority to Operate \(ATO\)\. For more information, see [AWS Services in Scope by Compliance Program](https://aws.amazon.com/compliance/services-in-scope/)\. 

## Common Management Tasks for MariaDB on Amazon RDS<a name="MariaDB.Concepts.General"></a>

These are the common management tasks you perform with an Amazon RDS MariaDB DB instance, with links to information about each task: 

+ Before creating a DB instance, you should complete the steps in the [Setting Up for Amazon RDS](CHAP_SettingUp.md) section of this guide\.

+ After you have met your prerequisites, such as creating security groups or DB parameter groups, you can create an Amazon RDS MariaDB DB instance\. For information on this process, see [Creating a DB Instance Running the MariaDB Database Engine](USER_CreateMariaDBInstance.md)\.

+ After creating your security group and DB instance, you can connect to the DB instance from MariaDB applications and utilities\. For information, see [Connecting to a DB Instance Running the MariaDB Database Engine](USER_ConnectToMariaDBInstance.md)\.

+ A newly created Amazon RDS DB instance has one empty database with the name you specified when you created the DB instance, and one master user account with the name and password you specified\. You must use a tool or utility compatible with MariaDB to log in as the master user, and then use MariaDB commands and SQL statements to add all of the users and elements required for your applications to store and retrieve data in the DB instance, such as the following:

  + Create all user IDs and grant them the appropriate permissions\. For information, see [MariaDB User Account Management](http://mariadb.com/kb/en/mariadb/user-account-management/) in the MariaDB documentation\.

  + Create any required databases and objects such as tables and views\. For information, see [Data Definition](http://mariadb.com/kb/en/mariadb/data-definition/) in the MariaDB documentation\.

  + Establish procedures for importing or exporting data\. For information on some recommended procedures, see [Importing Data into a MariaDB DB Instance](MariaDB.Procedural.Importing.md)\.

+ You might need to periodically change your DB instance, such as to resize or reconfigure the DB instance\. For information on doing so, see [Modifying a DB Instance Running the MariaDB Database Engine](USER_ModifyInstance.MariaDB.md)\. For additional information on specific tasks, see the following:

  + [Renaming a DB Instance](USER_RenameInstance.md)

  + [Deleting a DB Instance](USER_DeleteInstance.md)

  + [Rebooting a DB Instance](USER_RebootInstance.md)

  + [Tagging Amazon RDS Resources](USER_Tagging.md)

  + [Amazon RDS Maintenance](USER_UpgradeDBInstance.Maintenance.md)

  + [Adjusting the Preferred DB Instance Maintenance Window](USER_UpgradeDBInstance.Maintenance.md#AdjustingTheMaintenanceWindow)

+ You can configure your DB instance to take automated backups, or take manual snapshots, and then restore instances from the backups or snapshots\. For information, see [Backing Up and Restoring Amazon RDS DB Instances](CHAP_CommonTasks.BackupRestore.md)\.

+ You can monitor an instance through actions such as viewing the MariaDB logs, Amazon CloudWatch metrics for Amazon RDS, and events\. For information, see [Monitoring Amazon RDS](CHAP_Monitoring.md)\.

+ You can offload read traffic from your primary MariaDB DB instance by creating Read Replicas\. For information, see [Working with Read Replicas of MariaDB, MySQL, and PostgreSQL DB Instances](USER_ReadRepl.md)\.

+ Several Amazon RDS features that you can use with MariaDB DB instances are common across the Amazon RDS database engines\. For information on these, see the following:

  + [Working with Reserved DB Instances](USER_WorkingWithReservedDBInstances.md)

  + [Provisioned IOPS Storage](CHAP_Storage.md#USER_PIOPS)

Also, several appendices include useful information about working with Amazon RDS MariaDB DB instances:

+ [Appendix: Parameters for MariaDB](Appendix.MariaDB.Parameters.md)

+ [Appendix: MariaDB on Amazon RDS SQL Reference](Appendix.MariaDB.SQLRef.md)

## MariaDB on Amazon RDS Versions<a name="MariaDB.Concepts.VersionMgmt"></a>

For MariaDB, version numbers are organized as version X\.Y\.Z\. In Amazon RDS terminology, X\.Y denotes the major version, and Z is the minor version number\. For Amazon RDS implementations, a version change is considered major if the major version number changes, for example going from version 10\.0 to 10\.1\. A version change is considered minor if only the minor version number changes, for example going from version 10\.0\.17 to 10\.0\.24\. 

Amazon RDS currently supports the following versions of MariaDB: 


****  

| Major Version | Minor Version | 
| --- | --- | 
| MariaDB 10\.1 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_MariaDB.html)  | 
| MariaDB 10\.0 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_MariaDB.html)  | 

Over time, we plan to support additional MariaDB versions for Amazon RDS\. The number of new version releases supported in a given year will vary based on the frequency and content of the MariaDB version releases and the outcome of a thorough vetting of the release by our database engineering team\. However, as a general guidance, we aim to support new MariaDB versions within 3\-5 months of their General Availability release\.

You can specify any currently supported MariaDB version when creating a new DB Instance\. If no version is specified, Amazon RDS will default to a supported version, typically the most recent version\. If a major version \(e\.g\. MariaDB 10\.0\) is specified but a minor version is not, Amazon RDS will default to a recent release of the major version you have specified\. To see a list of supported versions, as well as defaults for newly created DB Instances, use the DescribeDBEngineVersions API\.

With Amazon RDS, you control when to upgrade your MariaDB instance to a new version supported by Amazon RDS\. You can maintain compatibility with specific MariaDB versions, test new versions with your application before deploying in production, and perform version upgrades at times that best fit your schedule\.

Unless you specify otherwise, your DB instance is automatically upgraded to new MariaDB minor versions as they are supported by Amazon RDS\. This patching occurs during your scheduled maintenance window, and it is announced on the [ Amazon RDS Community Forum](https://forums.aws.amazon.com/) in advance\. To turn off automatic version upgrades, change the **Auto Minor Version Upgrade** setting for the DB instance to **No**\. For more information on modifying the DB instance, see [Modifying a DB Instance Running the MariaDB Database Engine](USER_ModifyInstance.MariaDB.md)\.

If you opt out of automatic minor version upgrades, you can manually upgrade to a supported minor version release by following the same procedure as for a major version update\. For information, see [Upgrading the MariaDB DB Engine](USER_UpgradeDBInstance.MariaDB.md)\.

You cannot set major version upgrades to occur automatically, because they involve some compatibility risk\. Instead, you must make a request to upgrade the DB instance to a different major version\. You should thoroughly test your databases and applications against the new target version before upgrading your production instances\. For information about upgrading a DB instance, see [Upgrading the MariaDB DB Engine](USER_UpgradeDBInstance.MariaDB.md)\.

You can test a DB instance against a new version before upgrading by creating a DB snapshot of your existing DB instance, restoring from the DB snapshot to create a new DB instance, and then initiating a version upgrade for the new DB instance\. You can then experiment safely on the upgraded clone of your DB instance before deciding whether or not to upgrade your original DB instance\. 

The Amazon RDS deprecation policy for MariaDB includes the following:

+ We intend to support major MariaDB version releases, starting with MariaDB 10\.0\.17, for 3 years after they are initially supported by Amazon RDS\. 

+ We intend to support minor MariaDB version releases for at least 1 year after they are initially supported by Amazon RDS\. 

+ After a MariaDB major or minor version has been deprecated, we expect to provide a three\-month grace period for you to initiate an upgrade to a supported version prior to an automatic upgrade being applied during your scheduled maintenance window\. 

## MariaDB, MySQL, and Amazon Aurora Feature Comparison<a name="MariaDB.Concepts.Feature.Matrix"></a>

Use the following table to compare MariaDB, MySQL, and Aurora features to determine which DB engine is the best choice for your DB instance\. 


****  

| Feature | MariaDB | MySQL | Amazon Aurora | 
| --- | --- | --- | --- | 
|   **Storage engines**   |  Supports XtraDB fully, and Aria with some limitations\.  |  Supports both MyISAM and InnoDB\.  |  Supports only InnoDB\. Tables from other storage engines are automatically converted to InnoDB\. Because Amazon Aurora only supports the InnoDB engine, the NO\_ENGINE\_SUBSTITUTION option of the SQL\_MODE database parameter is enabled\. Enabling this option disables the ability to create an in\-memory table, unless that table is specified as TEMPORARY\.  | 
|   **Plugins**   |  Supports plugins\. For more information, see [Appendix: Options for MariaDB Database Engine](Appendix.MariaDB.Options.md)\.   |  Supports plugins\. For more information, see [Options for MySQL DB Instances](Appendix.MySQL.Options.md)\.   |  Doesn't support plugins\.  | 
|  **Join and subquery performance**   |  Includes query optimizer improvements for joins and subqueries faster than those in MySQL 5\.5 and 5\.6\. For more information, see [Optimizer Feature Comparison Matrix](http://mariadb.com/kb/en/mariadb/optimizer-feature-comparison-matrix/) in the MariaDB documentation\.  |  Query optimizer performance in keeping with MySQL 5\.5, 5\.6, or 5\.7, depending on the version you selected for your Amazon RDS MySQL DB instance\.  |  Query optimizer performance in keeping with MySQL 5\.6\.  | 
|   **Group commit**   |  Supports group commits\. For more information, see [Optimizer Feature Comparison Matrix](http://mariadb.com/kb/en/mariadb/group-commit-for-the-binary-log/) in the MariaDB documentation\. Supports additional tuning of group commits by setting the `binlog_commit_wait_count` parameter to determine the number of transactions that must complete before performing a group commit, and by setting the `binlog_commit_wait_usec` parameter to delay performing a group commit by a specified number of milliseconds\. For more information on these parameters, see [binlog\_commit\_wait\_count](http://mariadb.com/kb/en/mariadb/replication-and-binary-log-server-system-variables/#binlog_commit_wait_count) or [binlog\_commit\_wait\_usec](https://mariadb.com/kb/en/mariadb/replication-and-binary-log-server-system-variables/#binlog_commit_wait_usec) in the MariaDB documentation\. For more information on setting parameters for a DB instance, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.  |  Supports group commits\.  |  Supports group commits\.  | 
|   **Progress reporting**   | Supports progress reporting for some long\-running commands\. For more information, see [Progress Reporting](http://mariadb.com/kb/en/mariadb/progress-reporting/) in the MariaDB documentation\. |  Doesn't support progress reporting\.  |  Doesn't support progress reporting\.  | 
|   **Roles**   |  Support creation of custom roles for easily assigning sets of privileges to groups of users\. For more information, see [Roles](http://mariadb.com/kb/en/mariadb/roles-overview/) in the MariaDB documentation\.  |  Doesn't support roles\.  |  Doesn't support roles\.  | 
|   **SHOW EXPLAIN**   |  Supports the SHOW EXPLAIN command, using which you can get a description of the query plan for a query running in a specified thread\. For more information, see [SHOW EXPLAIN](http://mariadb.com/kb/en/mariadb/show-explain/) in the MariaDB documentation\.  |  Doesn't support SHOW EXPLAIN\.  |  Doesn't support SHOW EXPLAIN\.  | 
|   **Table elimination**   |  Supports table elimination, which sometimes allows the DB instance to improve performance by resolving a query without accessing some of the tables that the query refers to\. For more information, see [Table Elimination](http://mariadb.com/kb/en/mariadb/table-elimination/) in the MariaDB documentation\.  |  Doesn't support table elimination\.  |  Doesn't support table elimination\.  | 
|   **Thread pooling**   |  Supports thread pooling to enable the DB instance to handle more connections without performance degradation\. For more information, see [Thread Pool in MariaDB](http://mariadb.com/kb/en/mariadb/thread-pool-in-mariadb/) in the MariaDB documentation\.  |  Doesn't support thread pooling\.  |  Doesn't support thread pooling\.  | 
|   **Virtual columns**   |  Supports virtual columns\. These table columns have their values automatically calculated using a deterministic expression, typically based on the values of other columns in the table\. For more information, see [Virtual \(Computed\) Columns](http://mariadb.com/kb/en/mariadb/virtual-computed-columns/) in the MariaDB documentation\.  |  Doesn't support virtual columns\.  |  Doesn't support virtual columns\.  | 
|   **Global transaction IDs**   |  Supports the MariaDB implementation of global transaction IDs \(GTIDs\)\. For more information, see [Global Transaction ID](http://mariadb.com/kb/en/mariadb/global-transaction-id/) in the MariaDB documentation\.  Amazon RDS doesn't permit changes to the domain ID portion of a MariaDB GTID\.   |  Doesn't support the MySQL implementation of global transaction IDs\.  |  Doesn't support the MySQL implementation of global transaction IDs\.  | 
|   **Parallel replication**   |  Supports parallel replication, which increases replication performance by allowing queries to process in parallel on the replica\. For more information, see [Parallel Replication](http://mariadb.com/kb/en/mariadb/parallel-replication/) in the MariaDB documentation\. Although parallel replication is similar to multithreaded replication in MySQL 5\.6, it has some enhancements, such as not requiring partitioning across schemas and permitting group commits to replicate in parallel\.  |  MySQL 5\.6 and 5\.7 support multithreaded replication\. For more information, see [Replication Slave Options and Variables](http://dev.mysql.com/doc/refman/5.6/en/replication-options-slave.html) in the MySQL documentation\. MySQL 5\.5 doesn't support multithreaded replication\.  |  Supports the MySQL 5\.6 implementation of multithreaded replication\.  | 
|  **Database engine parameters**   |  Parameters apply to each individual DB instance or Read Replica and are managed by DB parameter groups\.  |  Parameters apply to each individual DB instance or Read Replica and are managed by DB parameter groups\.  |  Some parameters apply to the entire Aurora DB cluster and are managed by DB cluster parameter groups\. Other parameters apply to each individual DB instance in a DB cluster and are managed by DB parameter groups\.  | 
|  **Read Replicas with a different storage engine than the master instance**   |  Read Replicas can use XtraDB\.  |  Read Replicas can use both MyISAM and InnoDB\.  |  MySQL \(non\-RDS\) Read Replicas that replicate with an Aurora DB cluster can only use InnoDB\.  | 
|   **Read scaling**   |  Supports up to 5 Read Replicas with some impact on the performance of write operations\.  |  Supports up to 5 Read Replicas with some impact on the performance of write operations\.  |  Supports up to 15 Aurora Replicas with minimal impact on the performance of write operations\.  | 
|   **Failover target**   |  You can manually promote Read Replicas to the master DB instance with potential data loss\.  |  You can manually promote Read Replicas to the master DB instance with potential data loss\.  |  Aurora Replicas are automatic failover targets with no data loss\.  | 
|   **AWS region**   |  Available in all AWS regions\.  |  Available in all AWS regions\.  |  Aurora DB clusters are not available in all AWS Regions\. For more information, see [Availability](Aurora.Overview.md#Aurora.Overview.Availability)\.   | 

### MariaDB Features Supported in Version 10\.1<a name="MariaDB.Concepts.Features.Version.10-1"></a>

Amazon RDS supports the following features in MariaDB DB instances running MariaDB version 10\.1 or later:

+ [Optimistic in\-order parallel replication](https://mariadb.com/kb/en/parallel-replication/#optimistic-mode-of-in-order-parallel-replication)

+ [Page Compression](https://mariadb.com/kb/en/innodbxtradb-page-compression/)

+ [XtraDB data scrubbing and defragmentation](https://mariadb.com/kb/en/defragmenting-innodb-tablespaces/)

## MariaDB Features Not Supported by Amazon RDS<a name="MariaDB.Concepts.Features"></a>

Amazon RDS currently doesn't support the following MariaDB features:

+ Data at Rest Encryption

+ MariaDB Galera Cluster

+ HandlerSocket

+ JSON table type

+ Multi\-source Replication

+ Password validation plugin, `simple_password_check`, and `cracklib_password_check` 

+ Replication Filters

+ Storage engine\-specific object attributes, as described in [Engine\-defined New Table/Field/Index Attributes](http://mariadb.com/kb/en/mariadb/engine-defined-new-tablefieldindex-attributes/)\.

+ Table and Tablespace Encryption

To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances, and it restricts access to certain system procedures and tables that require advanced privileges\. Amazon RDS supports access to databases on a DB instance using any standard SQL client application\. Amazon RDS doesn't allow direct host access to a DB instance by using Telnet, Secure Shell \(SSH\), or Windows Remote Desktop Connection\.

## Supported Storage Engines for MariaDB on Amazon RDS<a name="MariaDB.Concepts.Storage"></a>

While MariaDB supports multiple storage engines with varying capabilities, not all of them are optimized for recovery and data durability\. Amazon RDS fully supports the XtraDB storage engine for MariaDB DB instances\. Amazon RDS features such as Point\-In\-Time Restore and snapshot restore require a recoverable storage engine and are supported for the XtraDB storage engine only\. Amazon RDS also supports Aria, although using Aria might have a negative impact on recovery in the event of an instance failure\. However, if you need to use spatial indexes to handle geographic data, you should use Aria because spatial indexes are not supported by XtraDB\.

Other storage engines are not currently supported by Amazon RDS for MariaDB\.

## MariaDB Security on Amazon RDS<a name="MariaDB.Concepts.UsersAndPrivileges"></a>

Security for Amazon RDS MariaDB DB instances is managed at three levels:

+ AWS Identity and Access Management controls who can perform Amazon RDS management actions on DB instances\. When you connect to AWS using IAM credentials, your IAM account must have IAM policies that grant the permissions required to perform Amazon RDS management operations\. For more information, see [Authentication and Access Control for Amazon RDS](UsingWithRDS.IAM.md)\.

+ When you create a DB instance, you use either a VPC security group or a DB security group to control which devices and Amazon EC2 instances can open connections to the endpoint and port of the DB instance\. These connections can be made using Secure Socket Layer \(SSL\)\. In addition, firewall rules at your company can control whether devices running at your company can open connections to the DB instance\.

+ Once a connection has been opened to a MariaDB DB instance, authentication of the login and permissions are applied the same way as in a stand\-alone instance of MariaDB\. Commands such as `CREATE USER`, `RENAME USER`, `GRANT`, `REVOKE`, and `SET PASSWORD` work just as they do in stand\-alone databases, as does directly modifying database schema tables\.

 When you create an Amazon RDS DB instance, the master user has the following default privileges: 

+  `alter` 

+  `alter routine` 

+  `create` 

+  `create routine` 

+  `create temporary tables` 

+  `create user` 

+  `create view` 

+  `delete` 

+  `drop` 

+  `event` 

+  `execute` 

+  `grant option` 

+  `index` 

+  `insert` 

+  `lock tables` 

+  `process` 

+  `references` 

+  `reload` 

  This privilege is limited on Amazon RDS MariaDB DB instances\. It doesn't grant access to the FLUSH LOGS or FLUSH TABLES WITH READ LOCK operations\.

+  `replication client` 

+  `replication slave` 

+  `select` 

+  `show databases` 

+  `show view` 

+  `trigger` 

+  `update` 

For more information about these privileges, see [User Account Management](http://mariadb.com/kb/en/mariadb/grant/) in the MariaDB documentation\.

**Note**  
Although you can delete the master user on a DB instance, we don't recommend doing so\. To recreate the master user, use the `ModifyDBInstance` API or the `modify-db-instance` AWS command line tool and specify a new master user password with the appropriate parameter\. If the master user does not exist in the instance, the master user is created with the specified password\. 

To provide management services for each DB instance, the `rdsadmin` user is created when the DB instance is created\. Attempting to drop, rename, change the password for, or change privileges for the `rdsadmin` account results in an error\.

To allow management of the DB instance, the standard `kill` and `kill_query` commands have been restricted\. The Amazon RDS commands `mysql.rds_kill`, `mysql.rds_kill_query`, and `mysql.rds_kill_query_id` are provided for use in MariaDB and also MySQL so that you can terminate user sessions or queries on DB instances\. 

## Using SSL with a MariaDB DB Instance<a name="MariaDB.Concepts.SSLSupport"></a>

Amazon RDS supports SSL connections with DB instances running the MariaDB database engine\. 

Amazon RDS creates an SSL certificate and installs the certificate on the DB instance when Amazon RDS provisions the instance\. These certificates are signed by a certificate authority\. The SSL certificate includes the DB instance endpoint as the Common Name \(CN\) for the SSL certificate to guard against spoofing attacks\. The public key is stored at [https://s3\.amazonaws\.com/rds\-downloads/rds\-combined\-ca\-bundle\.pem](https://s3.amazonaws.com/rds-downloads/rds-combined-ca-bundle.pem)\. 

To encrypt connections using the default mysql client, launch the mysql client using the `--ssl-ca parameter` to reference the public key, for example:

 `mysql -h mymariadbinstance.abcd1234.rds-us-east-1.amazonaws.com --ssl-ca=[full path]rds-combined-ca-bundle.pem --ssl-verify-server-cert` 

You can use the GRANT statement to require SSL connections for specific users accounts\. For example, you can use the following statement to require SSL connections on the user account **encrypted\_user**:

 ` GRANT USAGE ON *.* TO 'encrypted_user'@'%' REQUIRE SSL ` 

**Note**  
For more information on SSL connections with MariaDB, see the [SSL Overview](http://mariadb.com/kb/en/mariadb/ssl-connections/) in the MariaDB documentation\.

## XtraDB Cache Warming<a name="MariaDB.Concepts.XtraDBCacheWarming"></a>

XtraDB cache warming can provide performance gains for your MariaDB DB instance by saving the current state of the buffer pool when the DB instance is shut down, and then reloading the buffer pool from the saved information when the DB instance starts up\. This approach bypasses the need for the buffer pool to "warm up" from normal database use and instead preloads the buffer pool with the pages for known common queries\. For more information on XtraDB cache warming, see [Dumping and restoring the buffer pool](http://mariadb.com/kb/en/mariadb/xtradbinnodb-buffer-pool/#dumping-and-restoring-the-buffer-pool) in the MariaDB documentation\.

To enable XtraDB cache warming, set the `innodb_buffer_pool_dump_at_shutdown` and `innodb_buffer_pool_restore_at_startup` parameters to 1 in the parameter group for your DB instance\. Changing these parameter values in a parameter group affects all MariaDB DB instances that use that parameter group\. To enable XtraDB cache warming for specific MariaDB DB instances, you might need to create a new parameter group for those instances\. For information on parameter groups, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\. 

XtraDB cache warming primarily provides a performance benefit for DB instances that use standard storage\. If you use PIOPS storage, you don't commonly see a significant performance benefit\.

**Important**  
If your MariaDB DB instance doesn't shut down normally, such as during a failover, then the buffer pool state isn't saved to disk\. In this case, MariaDB loads whatever buffer pool file is available when the DB instance is restarted\. No harm is done, but the restored buffer pool might not reflect the most recent state of the buffer pool prior to the restart\. To ensure that you have a recent state of the buffer pool available to warm the XtraDB cache on startup, we recommend that you periodically dump the buffer pool "on demand\." You can dump or load the buffer pool on demand\.  
You can create an event to dump the buffer pool automatically and at a regular interval\. For example, the following statement creates an event named `periodic_buffer_pool_dump` that dumps the buffer pool every hour\.   

```
1. CREATE EVENT periodic_buffer_pool_dump 
2.    ON SCHEDULE EVERY 1 HOUR 
3.    DO CALL mysql.rds_innodb_buffer_pool_dump_now();
```
For more information, see [Events](http://mariadb.com/kb/en/mariadb/stored-programs-and-views-events/) in the MariaDB documentation\.

### Dumping and Loading the Buffer Pool on Demand<a name="MariaDB.Concepts.XtraDBCacheWarming.OnDemand"></a>

You can save and load the XtraDB cache on demand using the following stored procedures:

+ To dump the current state of the buffer pool to disk, call the [mysql\.rds\_innodb\_buffer\_pool\_dump\_now](mysql_rds_innodb_buffer_pool_dump_now.md) stored procedure\.

+ To load the saved state of the buffer pool from disk, call the [mysql\.rds\_innodb\_buffer\_pool\_load\_now](mysql_rds_innodb_buffer_pool_load_now.md) stored procedure\.

+ To cancel a load operation in progress, call the [mysql\.rds\_innodb\_buffer\_pool\_load\_abort](mysql_rds_innodb_buffer_pool_load_abort.md) stored procedure\.

## Database Parameters for MariaDB<a name="MariaDB.Concepts.Parameters"></a>

By default, a MariaDB DB instance uses a DB parameter group that is specific to a MariaDB database\. This parameter group contains some but not all of the parameters contained in the Amazon RDS DB parameter groups for the MySQL database engine\. It also contains a number of new, MariaDB\-specific parameters\. For more information on the parameters available for the Amazon RDS MariaDB DB engine, see [Appendix: Parameters for MariaDB](Appendix.MariaDB.Parameters.md)\.

## Common DBA Tasks for MariaDB<a name="MariaDB.Concepts.DBA.Tasks"></a>

Killing sessions or queries, skipping replication errors, working with XtraDB tablespaces to improve crash recovery times, and managing the global status history are common DBA tasks you might perform in a MariaDB DB instance\. You can handle these tasks just as in an Amazon RDS MySQL DB instance, as described in [Common DBA Tasks for MySQL DB Instances](Appendix.MySQL.CommonDBATasks.md)\. The crash recovery instructions there refer to the MySQL InnoDB engine, but they are applicable to a MariaDB instance running XtraDB as well\.

## Local Time Zone for MariaDB DB Instances<a name="MariaDB.Concepts.LocalTimeZone"></a>

By default, the time zone for an RDS MariaDB DB instance is Universal Time Coordinated \(UTC\)\. You can set the time zone for your DB instance to the local time zone for your application instead\.

To set the local time zone for a DB instance, set the `time_zone` parameter in the parameter group for your DB instance to one of the supported values listed later in this section\. When you set the `time_zone` parameter for a parameter group, all DB instances and Read Replicas that are using that parameter group change to use the new local time zone\. For information on setting parameters in a parameter group, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

After you set the local time zone, all new connections to the database reflect the change\. If you have any open connections to your database when you change the local time zone, you won't see the local time zone update until after you close the connection and open a new connection\.

You can set a different local time zone for a DB instance and one or more of its Read Replicas\. To do this, use a different parameter group for the DB instance and the replica or replicas and set the `time_zone` parameter in each parameter group to a different local time zone\.

If you are replicating across regions, then the replication master DB instance and the Read Replica use different parameter groups \(parameter groups are unique to a region\)\. To use the same local time zone for each instance, you must set the `time_zone` parameter in the instance's and Read Replica's parameter groups\.

When you restore a DB instance from a DB snapshot, the local time zone is set to UTC\. You can update the time zone to your local time zone after the restore is complete\. If you restore a DB instance to a point in time, then the local time zone for the restored DB instance is the time zone setting from the parameter group of the restored DB instance\.

You can set your local time zone to one of the following values\.


****  

|  |  |  | 
| --- |--- |--- |
| `Africa/Cairo` | `Asia/Bangkok` | `Australia/Darwin` | 
| `Africa/Casablanca` | `Asia/Beirut` | `Australia/Hobart` | 
| `Africa/Harare` | `Asia/Calcutta` | `Australia/Perth` | 
| `Africa/Monrovia` | `Asia/Damascus` | `Australia/Sydney` | 
| `Africa/Nairobi` | `Asia/Dhaka` | `Brazil/East` | 
| `Africa/Tripoli` | `Asia/Irkutsk` | `Canada/Newfoundland` | 
| `Africa/Windhoek` | `Asia/Jerusalem` | `Canada/Saskatchewan` | 
| `America/Araguaina` | `Asia/Kabul` | `Europe/Amsterdam` | 
| `America/Asuncion` | `Asia/Karachi` | `Europe/Athens` | 
| `America/Bogota` | `Asia/Kathmandu` | `Europe/Dublin` | 
| `America/Caracas` | `Asia/Krasnoyarsk` | `Europe/Helsinki` | 
| `America/Chihuahua` | `Asia/Magadan` | `Europe/Istanbul` | 
| `America/Cuiaba` | `Asia/Muscat` | `Europe/Kaliningrad` | 
| `America/Denver` | `Asia/Novosibirsk` | `Europe/Moscow` | 
| `America/Fortaleza` | `Asia/Riyadh` | `Europe/Paris` | 
| `America/Guatemala` | `Asia/Seoul` | `Europe/Prague` | 
| `America/Halifax` | `Asia/Shanghai` | `Europe/Sarajevo` | 
| `America/Manaus` | `Asia/Singapore` | `Pacific/Auckland` | 
| `America/Matamoros` | `Asia/Taipei` | `Pacific/Fiji` | 
| `America/Monterrey` | `Asia/Tehran` | `Pacific/Guam` | 
| `America/Montevideo` | `Asia/Tokyo` | `Pacific/Honolulu` | 
| `America/Phoenix` | `Asia/Ulaanbaatar` | `Pacific/Samoa` | 
| `America/Santiago` | `Asia/Vladivostok` | `US/Alaska` | 
| `America/Tijuana` | `Asia/Yakutsk` | `US/Central` | 
| `Asia/Amman` | `Asia/Yerevan` | `US/Eastern` | 
| `Asia/Ashgabat` | `Atlantic/Azores` | `US/East-Indiana` | 
| `Asia/Baghdad` | `Australia/Adelaide` | `US/Pacific` | 
| `Asia/Baku` | `Australia/Brisbane` | `UTC` | 