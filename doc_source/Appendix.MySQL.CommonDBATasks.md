# Common DBA Tasks for MySQL DB Instances<a name="Appendix.MySQL.CommonDBATasks"></a>

This section describes the Amazon RDS\-specific implementations of some common DBA tasks for DB instances running the MySQL database engine\. In order to deliver a managed service experience, Amazon RDS does not provide shell access to DB instances, and it restricts access to certain system procedures and tables that require advanced privileges\. 

For information about working with MySQL log files on Amazon RDS, see [MySQL Database Log Files](USER_LogAccess.Concepts.MySQL.md)

**Topics**
+ [Ending a Session or Query](#Appendix.MySQL.CommonDBATasks.End)
+ [Skipping the Current Replication Error](#Appendix.MySQL.CommonDBATasks.SkipError)
+ [Working with InnoDB Tablespaces to Improve Crash Recovery Times](#Appendix.MySQL.CommonDBATasks.Tables)
+ [Managing the Global Status History](#Appendix.MySQL.CommonDBATasks.GoSH)

## Ending a Session or Query<a name="Appendix.MySQL.CommonDBATasks.End"></a>

You can end user sessions or queries on DB instances by using the `rds_kill` and `rds_kill_query` commands\. First connect to your MySQL DB instance, then issue the appropriate command as shown following\. For more information, see [Connecting to a DB Instance Running the MySQL Database Engine](USER_ConnectToInstance.md)\. 

```
CALL mysql.rds_kill(thread-ID)
CALL mysql.rds_kill_query(thread-ID)
```

For example, to end the session that is running on thread 99, you would type the following: 

```
CALL mysql.rds_kill(99); 
```

To end the query that is running on thread 99, you would type the following: 

```
CALL mysql.rds_kill_query(99); 
```

## Skipping the Current Replication Error<a name="Appendix.MySQL.CommonDBATasks.SkipError"></a>

Amazon RDS provides a mechanism for you to skip an error on your read replicas if the error is causing your read replica to stop responding and the error doesn't affect the integrity of your data\. First connect to your MySQL DB instance, then issue the appropriate commands as shown following\. For more information, see [Connecting to a DB Instance Running the MySQL Database Engine](USER_ConnectToInstance.md)\. 

**Note**  
 You should first verify that the error can be safely skipped\. In a MySQL utility, connect to the read replica and run the following MySQL command:   

```
SHOW SLAVE STATUS\G 
```
For information about the values returned, see [the MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/show-slave-status.html)\.

 To skip the error, you can issue the following command: 

```
CALL mysql.rds_skip_repl_error; 
```

This command has no effect if you run it on the source DB instance, or on a read replica that has not encountered a replication error\. 

For more information, such as the versions of MySQL that support `mysql.rds_skip_repl_error`, see [mysql\.rds\_skip\_repl\_error](mysql_rds_skip_repl_error.md)\. 

**Important**  
If you attempt to call *mysql\.rds\_skip\_repl\_error* and encounter the following error: `ERROR 1305 (42000): PROCEDURE mysql.rds_skip_repl_error does not exist`, then upgrade your MySQL DB instance to the latest minor version or one of the minimum minor versions listed in [mysql\.rds\_skip\_repl\_error](mysql_rds_skip_repl_error.md)\.

## Working with InnoDB Tablespaces to Improve Crash Recovery Times<a name="Appendix.MySQL.CommonDBATasks.Tables"></a>

Every table in MySQL consists of a table definition, data, and indexes\. The MySQL storage engine InnoDB stores table data and indexes in a *tablespace*\. InnoDB creates a global shared tablespace that contains a data dictionary and other relevant metadata, and it can contain table data and indexes\. InnoDB can also create separate tablespaces for each table and partition\. These separate tablespaces are stored in files with a \.ibd extension and the header of each tablespace contains a number that uniquely identifies it\. 

Amazon RDS provides a parameter in a MySQL parameter group called `innodb_file_per_table`\. This parameters controls whether InnoDB adds new table data and indexes to the shared tablespace \(by setting the parameter value to 0\) or to individual tablespaces \(by setting the parameter value to 1\)\. Amazon RDS sets the default value for `innodb_file_per_table` parameter to 1, which allows you to drop individual InnoDB tables and reclaim storage used by those tables for the DB instance\. In most use cases, setting the `innodb_file_per_table` parameter to 1 is the recommended setting\.

You should set the `innodb_file_per_table` parameter to 0 when you have a large number of tables, such as over 1000 tables when you use standard \(magnetic\) or general purpose SSD storage or over 10,000 tables when you use Provisioned IOPS storage\. When you set this parameter to 0, individual tablespaces are not created and this can improve the time it takes for database crash recovery\. 

MySQL processes each metadata file, which includes tablespaces, during the crash recovery cycle\. The time it takes MySQL to process the metadata information in the shared tablespace is negligible compared to the time it takes to process thousands of tablespace files when there are multiple tablespaces\. Because the tablespace number is stored within the header of each file, the aggregate time to read all the tablespace files can take up to several hours\. For example, a million InnoDB tablespaces on standard storage can take from five to eight hours to process during a crash recovery cycle\. In some cases, InnoDB can determine that it needs additional cleanup after a crash recovery cycle so it will begin another crash recovery cycle, which will extend the recovery time\. Keep in mind that a crash recovery cycle also entails rolling\-back transactions, fixing broken pages, and other operations in addition to the processing of tablespace information\. 

Since the `innodb_file_per_table` parameter resides in a parameter group, you can change the parameter value by editing the parameter group used by your DB instance without having to reboot the DB instance\. After the setting is changed, for example, from 1 \(create individual tables\) to 0 \(use shared tablespace\), new InnoDB tables will be added to the shared tablespace while existing tables continue to have individual tablespaces\. To move an InnoDB table to the shared tablespace, you must use the `ALTER TABLE` command\.

### Migrating Multiple Tablespaces to the Shared Tablespace<a name="Appendix.MySQL.CommonDBATasks.MigrateMultiTbs"></a>

You can move an InnoDB table's metadata from its own tablespace to the shared tablespace, which will rebuild the table metadata according to the `innodb_file_per_table` parameter setting\. First connect to your MySQL DB instance, then issue the appropriate commands as shown following\. For more information, see [Connecting to a DB Instance Running the MySQL Database Engine](USER_ConnectToInstance.md)\. 

```
ALTER TABLE table_name ENGINE = InnoDB, ALGORITHM=COPY; 
```

For example, the following query returns an `ALTER TABLE` statement for every InnoDB table that is not in the shared tablespace\.

```
SELECT CONCAT('ALTER TABLE `', 
REPLACE(LEFT(NAME , INSTR((NAME), '/') - 1), '`', '``'), '`.`', 
REPLACE(SUBSTR(NAME FROM INSTR(NAME, '/') + 1), '`', '``'), '` ENGINE=InnoDB, ALGORITHM=COPY;') AS Query 
FROM INFORMATION_SCHEMA.INNODB_SYS_TABLES 
WHERE SPACE <> 0 AND LEFT(NAME, INSTR((NAME), '/') - 1) NOT IN ('mysql','');
```

**Note**  
This query is supported on MySQL 5\.6 and later\.

Rebuilding a MySQL table to move the table's metadata to the shared tablespace requires additional storage space temporarily to rebuild the table, so the DB instance must have storage space available\. During rebuilding, the table is locked and inaccessible to queries\. For small tables or tables not frequently accessed, this might not be an issue\. For large tables or tables frequently accessed in a heavily concurrent environment, you can rebuild tables on a read replica\. 

You can create a read replica and migrate table metadata to the shared tablespace on the read replica\. While the ALTER TABLE statement blocks access on the read replica, the source DB instance is not affected\. The source DB instance will continue to generate its binary logs while the read replica lags during the table rebuilding process\. Because the rebuilding requires additional storage space and the replay log file can become large, you should create a read replica with storage allocated that is larger than the source DB instance\.

To create a read replica and rebuild InnoDB tables to use the shared tablespace, take the following steps:

1. Make sure that backup retention is enabled on the source DB instance so that binary logging is enabled\. 

1. Use the AWS Management Console or AWS CLI to create a read replica for the source DB instance\. Because the creation of a read replica involves many of the same processes as crash recovery, the creation process can take some time if there is a large number of InnoDB tablespaces\. Allocate more storage space on the read replica than is currently used on the source DB instance\.

1. When the read replica has been created, create a parameter group with the parameter settings `read_only = 0` and `innodb_file_per_table = 0`\. Then associate the parameter group with the read replica\. 

1. Issue the following SQL statement for all tables that you want migrated on the replica:

   ```
   ALTER TABLE name ENGINE = InnoDB
   ```

1. When all of your `ALTER TABLE` statements have completed on the read replica, verify that the read replica is connected to the source DB instance and that the two instances are in sync\. 

1. Use the console or CLI to promote the read replica to be the instance\. Make sure that the parameter group used for the new standalone DB instance has the `innodb_file_per_table` parameter set to 0\. Change the name of the new standalone DB instance, and point any applications to the new standalone DB instance\. 

## Managing the Global Status History<a name="Appendix.MySQL.CommonDBATasks.GoSH"></a>

MySQL maintains many status variables that provide information about its operation\. Their value can help you detect locking or memory issues on a DB instance \. The values of these status variables are cumulative since last time the DB instance was started\. You can reset most status variables to 0 by using the `FLUSH STATUS` command\. 

To allow for monitoring of these values over time, Amazon RDS provides a set of procedures that will snapshot the values of these status variables over time and write them to a table, along with any changes since the last snapshot\. This infrastructure, called Global Status History \(GoSH\), is installed on all MySQL DB instances starting with versions 5\.5\.23\. GoSH is disabled by default\. 

To enable GoSH, you first enable the event scheduler from a DB parameter group by setting the parameter event\_scheduler to ON\. For information about creating and modifying a DB parameter group, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\. 

You can then use the procedures in the following table to enable and configure GoSH\. First connect to your MySQL DB instance, then issue the appropriate commands as shown following\. For more information, see [Connecting to a DB Instance Running the MySQL Database Engine](USER_ConnectToInstance.md)\. For each procedure, type the following: 

```
CALL procedure-name; 
```

Where *procedure\-name* is one of the procedures in the table\.


****  

| Procedure | Description | 
| --- | --- | 
| `mysql.rds_enable_gsh_collector` |   Enables GoSH to take default snapshots at intervals specified by `rds_set_gsh_collector`\.   | 
| `mysql.rds_set_gsh_collector` |   Specifies the interval, in minutes, between snapshots\. Default value is 5\.   | 
| `mysql.rds_disable_gsh_collector` |   Disables snapshots\.   | 
| `mysql.rds_collect_global_status_history` |   Takes a snapshot on demand\.   | 
| `mysql.rds_enable_gsh_rotation` |   Enables rotation of the contents of the `mysql.rds_global_status_history` table to `mysql.rds_global_status_history_old` at intervals specified by `rds_set_gsh_rotation`\.   | 
| `mysql.rds_set_gsh_rotation` |   Specifies the interval, in days, between table rotations\. Default value is 7\.   | 
| `mysql.rds_disable_gsh_rotation` |   Disables table rotation\.   | 
| `mysql.rds_rotate_global_status_history` |   Rotates the contents of the `mysql.rds_global_status_history` table to `mysql.rds_global_status_history_old` on demand\.   | 

When GoSH is running, you can query the tables that it writes to\. For example, to query the hit ratio of the Innodb buffer pool, you would issue the following query: 

```
select a.collection_end, a.collection_start, (( a.variable_Delta-b.variable_delta)/a.variable_delta)*100 as "HitRatio" 
    from mysql.rds_global_status_history as a join mysql.rds_global_status_history as b on a.collection_end = b.collection_end
    where a. variable_name = 'Innodb_buffer_pool_read_requests' and b.variable_name = 'Innodb_buffer_pool_reads'
```