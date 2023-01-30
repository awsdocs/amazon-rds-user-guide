# mysql\.rds\_set\_configuration<a name="mysql_rds_set_configuration"></a>

Specifies the number of hours to retain binary logs or the number of seconds to delay replication\.

## Syntax<a name="mysql_rds_set_configuration-syntax"></a>

 

```
CALL mysql.rds_set_configuration(name,value);
```

## Parameters<a name="mysql_rds_set_configuration-parameters"></a>

 *name*   
The name of the configuration parameter to set\.

 *value*   
The value of the configuration parameter\. 

## Usage notes<a name="mysql_rds_set_configuration-usage-notes"></a>

The `mysql.rds_set_configuration` procedure supports the following configuration parameters:
+ [Binlog retention hours](#mysql_rds_set_configuration-usage-notes.binlog-retention-hours)
+ [Source delay ](#mysql_rds_set_configuration-usage-notes.source-delay)
+ [Target delay](#mysql_rds_set_configuration-usage-notes.target-delay)

The configuration parameters are stored permanently and survive any DB instance reboot or failover\.

### Binlog retention hours<a name="mysql_rds_set_configuration-usage-notes.binlog-retention-hours"></a>

The `binlog retention hours` parameter is used to specify the number of hours to retain binary log files\. Amazon RDS normally purges a binary log as soon as possible, but the binary log might still be required for replication with a MySQL database external to Amazon RDS\. The default value of `binlog retention hours` is `NULL`\. This default value is interpreted as follows: 
+  For RDS for MySQL, `NULL` means binary logs are not retained \(0 hours\)\. 
+  For Aurora MySQL, `NULL` means binary logs are cleaned up lazily\. Aurora MySQL binary logs might remain in the system for a certain period, usually not longer than a day\. 

To specify the number of hours for Amazon RDS to retain binary logs on a DB instance, use the `mysql.rds_set_configuration` stored procedure and specify a period with enough time for replication to occur, as shown in the following example\.

`call mysql.rds_set_configuration('binlog retention hours', 24);`

For MySQL DB instances, the maximum `binlog retention hours` value is 168 \(7 days\)\.

After you set the retention period, monitor storage usage for the DB instance to make sure that the retained binary logs don't take up too much storage\.

### Source delay<a name="mysql_rds_set_configuration-usage-notes.source-delay"></a>

Use the `source delay` parameter in a read replica to specify the number of seconds to delay replication from the read replica to its source DB instance\. Amazon RDS normally replicates changes as soon as possible, but you might want some environments to delay replication\. For example, when replication is delayed, you can roll forward a delayed read replica to the time just before a disaster\. If a table is dropped accidentally, you can use delayed replication to quickly recover it\. The default value of `target delay` is `0` \(don't delay replication\)\.

When you use this parameter, it runs [mysql\.rds\_set\_source\_delay](mysql_rds_set_source_delay.md) and applies CHANGE primary TO MASTER\_DELAY = input value\. If successful, the procedure saves the `source delay` parameter to the `mysql.rds_configuration` table\.

To specify the number of seconds for Amazon RDS to delay replication to a source DB instance, use the `mysql.rds_set_configuration` stored procedure and specify the number of seconds to delay replication\. In the following example, the replication is delayed by at least one hour \(3,600 seconds\)\.

`call mysql.rds_set_configuration('source delay', 3600);`

The procedure then runs `mysql.rds_set_source_delay(3600)`\. 

The limit for the `source delay` parameter is one day \(86400 seconds\)\.

**Note**  
The `source delay` parameter isn't supported for RDS for MySQL version 8\.0 or MariaDB versions below 10\.2\.

### Target delay<a name="mysql_rds_set_configuration-usage-notes.target-delay"></a>

Use the `target delay` parameter to specify the number of seconds to delay replication between a DB instance and any future RDS\-managed read replicas created from this instance\. This parameter is ignored for non\-RDS\-managed read replicas\. Amazon RDS normally replicates changes as soon as possible, but you might want some environments to delay replication\. For example, when replication is delayed, you can roll forward a delayed read replica to the time just before a disaster\. If a table is dropped accidentally, you can use delayed replication to recover it quickly\. The default value of `target delay` is `0` \(don't delay replication\)\.

For disaster recovery, you can use this configuration parameter with the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure or the [mysql\.rds\_start\_replication\_until\_gtid](mysql_rds_start_replication_until_gtid.md) stored procedure\. To roll forward changes to a delayed read replica to the time just before a disaster, you can run the `mysql.rds_set_configuration` procedure with this parameter set\. After the `mysql.rds_start_replication_until` or `mysql.rds_start_replication_until_gtid` procedure stops replication, you can promote the read replica to be the new primary DB instance by using the instructions in [Promoting a read replica to be a standalone DB instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\. 

To use the `mysql.rds_rds_start_replication_until_gtid` procedure, GTID\-based replication must be enabled\. To skip a specific GTID\-based transaction that is known to cause disaster, you can use the [mysql\.rds\_skip\_transaction\_with\_gtid](mysql_rds_skip_transaction_with_gtid.md) stored procedure\. For more information about working with GTID\-based replication, see [Using GTID\-based replication for Amazon RDS for MySQL](mysql-replication-gtid.md)\.

To specify the number of seconds for Amazon RDS to delay replication to a read replica, use the `mysql.rds_set_configuration` stored procedure and specify the number of seconds to delay replication\. The following example specifies that replication is delayed by at least one hour \(3,600 seconds\)\.

`call mysql.rds_set_configuration('target delay', 3600);`

The limit for the `target delay` parameter is one day \(86400 seconds\)\.

**Note**  
The `target delay` parameter isn't supported for RDS for MySQL version 8\.0 or MariaDB versions below 10\.2\.