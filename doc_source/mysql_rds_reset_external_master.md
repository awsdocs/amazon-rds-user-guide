# mysql\.rds\_reset\_external\_master<a name="mysql_rds_reset_external_master"></a>

Reconfigures a MySQL DB instance to no longer be a read replica of an instance of MySQL running external to Amazon RDS\.

**Important**  
To run this procedure, `autocommit` must be enabled\. To enable it, set the `autocommit` parameter to `1`\. For information about modifying parameters, see [Modifying Parameters in a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.

## Syntax<a name="mysql_rds_reset_external_master-syntax"></a>

```
CALL mysql.rds_reset_external_master;
```

## Usage Notes<a name="mysql_rds_reset_external_master-usage-notes"></a>

The master user must run the `mysql.rds_reset_external_master` procedure\. This procedure must be run on the MySQL DB instance to be removed as a read replica of a MySQL instance running external to Amazon RDS\.

**Note**  
We recommend that you use read replicas to manage replication between two Amazon RDS DB instances when possible\. When you do so, we recommend that you use only this and other replication\-related stored procedures\. These practices enable more complex replication topologies between Amazon RDS DB instances\. We offer these stored procedures primarily to enable replication with MySQL instances running external to Amazon RDS\. For information about managing replication between Amazon RDS DB instances, see [Working with Read Replicas](USER_ReadRepl.md)\.

For more information about using replication to import data from an instance of MySQL running external to Amazon RDS, see [Restoring a Backup into an Amazon RDS MySQL DB Instance](MySQL.Procedural.Importing.md)\.