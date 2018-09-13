# mysql\.rds\_start\_replication<a name="mysql_rds_start_replication"></a>

Initiates replication from a MySQL DB instance\.

**Note**  
You can use the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure to initiate replication from an Amazon RDS MySQL DB instance and stop replication at the specified binary log file location\.

## Syntax<a name="mysql_rds_start_replication-syntax"></a>

```
CALL mysql.rds_start_replication;
```

## Usage Notes<a name="mysql_rds_start_replication-usage-notes"></a>

The `mysql.rds_start_replication` procedure must be run by the master user\.

If you are configuring replication to import data from an instance of MySQL running external to Amazon RDS, you call `mysql.rds_start_replication` on the Read Replica to start the replication process after you have called [mysql\.rds\_set\_external\_master](mysql_rds_set_external_master.md) to build the replication configuration\. For more information, see [Restoring a Backup into an Amazon RDS MySQL DB Instance](MySQL.Procedural.Importing.md)\.

If you are configuring replication to export data to an instance of MySQL external to Amazon RDS, you call `mysql.rds_start_replication` and `mysql.rds_stop_replication` on the Read Replica to control some replication actions, such as purging binary logs\. For more information, see [Exporting Data from a MySQL DB Instance by Using Replication](MySQL.Procedural.Exporting.NonRDSRepl.md)\.

You can also call `mysql.rds_start_replication` on the Read Replica to restart any replication process that you previously stopped by calling [mysql\.rds\_stop\_replication](mysql_rds_stop_replication.md)\. For more information, see [Working with Read Replicas of MariaDB, MySQL, and PostgreSQL DB Instances](USER_ReadRepl.md)\.

The `mysql.rds_start_replication` procedure is available in these versions of Amazon RDS MySQL:
+ MySQL 5\.5
+ MySQL 5\.6
+ MySQL 5\.7

## Related Topics<a name="mysql_rds_start_replication.related"></a>
+ [mysql\.rds\_set\_external\_master](mysql_rds_set_external_master.md)
+ [mysql\.rds\_reset\_external\_master](mysql_rds_reset_external_master.md)
+ [mysql\.rds\_stop\_replication](mysql_rds_stop_replication.md)