# mysql\.rds\_set\_source\_delay<a name="mysql_rds_set_source_delay"></a>

Sets the minimum number of seconds to delay replication from source database instance to the current read replica\. Use this procedure when you are connected to a read replica to delay replication from its source database instance\.

## Syntax<a name="mysql_rds_set_source_delay-syntax"></a>

```
CALL mysql.rds_set_source_delay(
delay
);
```

## Parameters<a name="mysql_rds_set_source_delay-parameters"></a>

 *delay*   
The minimum number of seconds to delay replication from the source database instance\.  
The limit for this parameter is one day \(86400 seconds\)\.

## Usage Notes<a name="mysql_rds_set_source_delay-usage-notes"></a>

The master user must run the `mysql.rds_set_source_delay` procedure\.

For disaster recovery, you can use this procedure with the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure or the [mysql\.rds\_start\_replication\_until\_gtid](mysql_rds_start_replication_until_gtid.md) stored procedure\. To roll forward changes to a delayed read replica to the time just before a disaster, you can run the `mysql.rds_set_source_delay` procedure\. After the `mysql.rds_start_replication_until` or `mysql.rds_start_replication_until_gtid` procedure stops replication, you can promote the read replica to be the new primary DB instance by using the instructions in [Promoting a Read Replica to Be a Standalone DB Instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\.

To use the `mysql.rds_rds_start_replication_until_gtid` procedure, GTID\-based replication must be enabled\. To skip a specific GTID\-based transaction that is known to cause disaster, you can use the [mysql\.rds\_skip\_transaction\_with\_gtid](mysql_rds_skip_transaction_with_gtid.md) stored procedure\. For more information on GTID\-based replication, see [Using GTID\-Based Replication for Amazon RDS MySQL](mysql-replication-gtid.md)\.

The `mysql.rds_set_source_delay` procedure is available in these versions of Amazon RDS MySQL:
+ MySQL 5\.6\.40 and later 5\.6 versions
+ MySQL 5\.7\.22 and later 5\.7 versions

## Examples<a name="mysql_rds_set_source_delay-examples"></a>

To delay replication from source database instance to the current read replica for at least one hour \(3,600 seconds\), you can call `mysql.rds_set_source_delay` with the following parameter:

```
CALL mysql.rds_set_source_delay(3600);
```