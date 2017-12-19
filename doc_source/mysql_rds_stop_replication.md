# mysql\.rds\_stop\_replication<a name="mysql_rds_stop_replication"></a>

Terminates replication from a MySQL DB instance\.

## Syntax<a name="mysql_rds_stop_replication-syntax"></a>

```
CALL mysql.rds_stop_replication;
```

## Usage Notes<a name="mysql_rds_stop_replication-usage-notes"></a>

The `mysql.rds_stop_replication` procedure must be run by the master user\. 

If you are configuring replication to import data from an instance of MySQL running external to Amazon RDS, you call `mysql.rds_stop_replication` on the replica to stop the replication process after the import has completed\. For more information, see [Importing Data into an Amazon RDS MySQL DB Instance](MySQL.Procedural.Importing.md)\.

If you are configuring replication to export data to an instance of MySQL external to Amazon RDS, you call `mysql.rds_start_replication` and `mysql.rds_stop_replication` on the replica to control some replication actions, such as purging binary logs\. For more information, see [Exporting Data from a MySQL DB Instance by Using Replication](MySQL.Procedural.Exporting.NonRDSRepl.md)\.

You can also use `mysql.rds_stop_replication` to stop replication between two Amazon RDS DB instances\. You typically stop replication to perform a long running operation on the replica, such as creating a large index on the replica\. You can restart any replication process that you stopped by calling [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) on the replica\. For more information, see [Working with Read Replicas of MariaDB, MySQL, and PostgreSQL DB Instances](USER_ReadRepl.md)\.

The `mysql.rds_stop_replication` procedure is available in these versions of Amazon RDS MySQL:

+ MySQL 5\.5

+ MySQL 5\.6

+ MySQL 5\.7

## Related Topics<a name="mysql_rds_stop_replication.related"></a>

+ [mysql\.rds\_set\_external\_master](mysql_rds_set_external_master.md)

+ [mysql\.rds\_reset\_external\_master](mysql_rds_reset_external_master.md)

+ [mysql\.rds\_start\_replication](mysql_rds_start_replication.md)