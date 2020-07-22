# mysql\.rds\_start\_replication\_until\_gtid<a name="mysql_rds_start_replication_until_gtid"></a>

Initiates replication from an Amazon RDS MySQL DB instance and stops replication immediately after the specified global transaction identifier \(GTID\)\.

## Syntax<a name="mysql_rds_start_replication_until_gtid-syntax"></a>

```
CALL mysql.rds_start_replication_until_gtid (
gtid
);
```

## Parameters<a name="mysql_rds_start_replication_until_gtid-parameters"></a>

 *gtid*   
The GTID after which replication is to stop\.

## Usage Notes<a name="mysql_rds_start_replication_until_gtid-usage-notes"></a>

The master user must run the `mysql.rds_start_replication_until_gtid` procedure\.

For Amazon RDS MySQL 5\.7, this procedure is supported for MySQL 5\.7\.23 and later MySQL 5\.7 versions\. This procedure is not supported for Amazon RDS MySQL 5\.5, 5\.6, or 8\.0\.

You can use this procedure with delayed replication for disaster recovery\. If you have delayed replication configured, you can use this procedure to roll forward changes to a delayed read replica to the time just before a disaster\. After this procedure stops replication, you can promote the read replica to be the new primary DB instance by using the instructions in [Promoting a Read Replica to Be a Standalone DB Instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\.

You can configure delayed replication using the following stored procedures:
+ [mysql\.rds\_set\_configuration](mysql_rds_set_configuration.md)
+ [mysql\.rds\_set\_external\_master\_with\_auto\_position](mysql_rds_set_external_master_with_auto_position.md)
+ [mysql\.rds\_set\_source\_delay](mysql_rds_set_source_delay.md)

When the `gtid` parameter specifies a transaction that has already been run by the replica, replication is stopped immediately\.

## Examples<a name="mysql_rds_start_replication_until_gtid-examples"></a>

The following example initiates replication and replicates changes until it reaches GTID `3E11FA47-71CA-11E1-9E33-C80AA9429562:23`\.

```
call mysql.rds_start_replication_until_gtid('3E11FA47-71CA-11E1-9E33-C80AA9429562:23');
```