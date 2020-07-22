# mysql\.rds\_start\_replication\_until<a name="mysql_rds_start_replication_until"></a>

Initiates replication from an Amazon RDS MySQL DB instance and stops replication at the specified binary log file location\.

## Syntax<a name="mysql_rds_start_replication_until-syntax"></a>

```
CALL mysql.rds_start_replication_until (
replication_log_file
  , replication_stop_point
);
```

## Parameters<a name="mysql_rds_start_replication_until-parameters"></a>

 *replication\_log\_file*   
The name of the binary log on the source database instance contains the replication information\.

 *replication\_stop\_point *   
The location in the `replication_log_file` binary log at which replication will stop\.

## Usage Notes<a name="mysql_rds_start_replication_until-usage-notes"></a>

The master user must run the `mysql.rds_start_replication_until` procedure\.

You can use this procedure with delayed replication for disaster recovery\. If you have delayed replication configured, you can use this procedure to roll forward changes to a delayed read replica to the time just before a disaster\. After this procedure stops replication, you can promote the read replica to be the new primary DB instance by using the instructions in [Promoting a Read Replica to Be a Standalone DB Instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\.

You can configure delayed replication using the following stored procedures:
+ [mysql\.rds\_set\_configuration](mysql_rds_set_configuration.md)
+ [mysql\.rds\_set\_external\_master\_with\_delay](mysql_rds_set_external_master_with_delay.md)
+ [mysql\.rds\_set\_source\_delay](mysql_rds_set_source_delay.md)

The file name specified for the `replication_log_file` parameter must match the source database instance binlog file name\.

When the `replication_stop_point ` parameter specifies a stop location that is in the past, replication is stopped immediately\.

The `mysql.rds_start_replication_until` procedure is available in these versions of Amazon RDS MySQL:
+ MySQL 5\.6\.40 and later 5\.6 versions
+ MySQL 5\.7\.22 and later 5\.7 versions

## Examples<a name="mysql_rds_start_replication_until-examples"></a>

The following example initiates replication and replicates changes until it reaches location `120` in the `mysql-bin-changelog.000777` binary log file\.

```
call mysql.rds_start_replication_until(
  'mysql-bin-changelog.000777',
  120);
```