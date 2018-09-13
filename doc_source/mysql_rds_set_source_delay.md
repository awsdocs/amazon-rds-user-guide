# mysql\.rds\_set\_source\_delay<a name="mysql_rds_set_source_delay"></a>

Sets the minimum number of seconds to delay replication from the master to the current Read Replica\. Use this procedure when you are connected to a Read Replica to delay replication from its master\.

**Note**  
On Amazon RDS MySQL 5\.7, this procedure is supported for MySQL 5\.7\.22 and later\. On Amazon RDS MySQL 5\.6, this procedure is supported for MySQL 5\.6\.40 and later\.

## Syntax<a name="mysql_rds_set_source_delay-syntax"></a>

```
CALL mysql.rds_set_source_delay(
delay
);
```

## Parameters<a name="mysql_rds_set_source_delay-parameters"></a>

 *delay*   
The minimum number of seconds to delay replication from the master\.  
The limit for this parameter is one day \(86400 seconds\)\.

## Usage Notes<a name="mysql_rds_set_source_delay-usage-notes"></a>

The `mysql.rds_set_source_delay` procedure must be run by the master user\.

You can use this procedure with the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure for disaster recovery\. You can run the `mysql.rds_set_source_delay` procedure to roll forward changes to a delayed Read Replica to the time just before a disaster\. After the `mysql.rds_start_replication_until` procedure stops replication, you can promote the Read Replica to be the new master DB instance by using the instructions in [Promoting a Read Replica to Be a Standalone DB Instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\.

The `mysql.rds_set_source_delay` procedure is available in these versions of Amazon RDS MySQL:
+ MySQL 5\.6
+ MySQL 5\.7

## Examples<a name="mysql_rds_set_source_delay-examples"></a>

To delay replication from the master to the current Read Replica for at least one hour \(3600 seconds\), you can call `mysql.rds_set_source_delay` with the following parameter:

```
CALL mysql.rds_set_source_delay(3600);
```