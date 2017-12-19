# mysql\.rds\_reset\_external\_master<a name="mysql_rds_reset_external_master"></a>

Reconfigures a MySQL DB instance to no longer be a Read Replica of an instance of MySQL running external to Amazon RDS\.

## Syntax<a name="mysql_rds_reset_external_master-syntax"></a>

```
CALL mysql.rds_reset_external_master;
```

## Usage Notes<a name="mysql_rds_reset_external_master-usage-notes"></a>

The `mysql.rds_reset_external_master` procedure must be run by the master user\. It must be run on the MySQL DB instance to be removed as a Read Replica of a MySQL instance running external to Amazon RDS\.

**Note**  
We recommend that you use Read Replicas to manage replication between two Amazon RDS DB instances when possible, and only use this and other replication\-related stored procedures to enable more complex replication topologies between Amazon RDS DB instances\. These stored procedures are primarily offered to enable replication with MySQL instances running external to Amazon RDS\. For information about managing replication between Amazon RDS DB instances, see [Working with Read Replicas of MariaDB, MySQL, and PostgreSQL DB Instances](USER_ReadRepl.md)\.

For more information about using replication to import data from an instance of MySQL running external to Amazon RDS, see [Importing Data into an Amazon RDS MySQL DB Instance](MySQL.Procedural.Importing.md)\.

The `mysql.rds_reset_external_master` procedure is available in these versions of Amazon RDS MySQL:

+ MySQL 5\.5

+ MySQL 5\.6

+ MySQL 5\.7

## Related Topics<a name="mysql_rds_reset_external_master.related"></a>

+ [mysql\.rds\_set\_external\_master](mysql_rds_set_external_master.md)

+ [mysql\.rds\_start\_replication](mysql_rds_start_replication.md)

+ [mysql\.rds\_stop\_replication](mysql_rds_stop_replication.md)