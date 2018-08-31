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

## Usage Notes<a name="mysql_rds_set_configuration-usage-notes"></a>

The `mysql.rds_set_configuration` stored procedure is available in these versions of Amazon RDS MySQL:
+ MySQL 5\.6
+ MySQL 5\.7

The `mysql.rds_set_configuration` procedure supports the following configuration parameters:
+ [binlog retention hours](#mysql_rds_set_configuration-usage-notes.binlog-retention-hours)
+ [target delay](#mysql_rds_set_configuration-usage-notes.target-delay)

### binlog retention hours<a name="mysql_rds_set_configuration-usage-notes.binlog-retention-hours"></a>

The `binlog retention hours` parameter is used to specify the number of hours to retain binary log files\. Amazon RDS normally purges a binary log as soon as possible, but the binary log might still be required for replication with a MySQL database external to Amazon RDS\. The default value of `binlog retention hours` is NULL \(do not retain binary logs\)\.

To specify the number of hours for Amazon RDS to retain binary logs on a DB instance, use the `mysql.rds_set_configuration` stored procedure and specify a period with enough time for replication to occur, as shown in the following example\.

`call mysql.rds_set_configuration('binlog retention hours', 24);`

For MySQL DB instances, the maximum `binlog retention hours` value is 168 \(7 days\)\.

After you set the retention period, monitor storage usage for the DB instance to ensure that the retained binary logs don't take up too much storage\.

### target delay<a name="mysql_rds_set_configuration-usage-notes.target-delay"></a>

The `target delay` parameter is used to specify the number of seconds to delay replication from the master to the Read Replica\. The specified delay applies to new replicas created from the current DB instance\. Amazon RDS normally replicates changes as soon as possible, but some environments might want to delay replication\. For example, when replication is delayed, it is possible to roll forward a delayed Read Replica to the time just before a disaster\. If a table is dropped accidentally, delayed replication can enable you to recover it quickly\. The default value of `target delay` is 0 \(do not delay replication\)\.

You can use this configuration parameter with the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure for disaster recovery\. You can run the `mysql.rds_set_configuration` procedure with this parameter set to roll forward changes to a delayed Read Replica to the time just before a disaster\. After the `mysql.rds_start_replication_until` procedure stops replication, you can promote the Read Replica to be the new master DB instance by using the instructions in [Promoting a Read Replica to Be a Standalone DB Instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\.

To specify the number of seconds for Amazon RDS to delay replication to a Read Replica, use the `mysql.rds_set_configuration` stored procedure and specify the number of seconds to delay replication\. The following example specifies that replication is delayed by at least one hour \(3600 seconds\)\.

`call mysql.rds_set_configuration('target delay', 3600);`

The limit for the `target delay` parameter is one day \(86400 seconds\)\.

**Note**  
The `target delay` parameter is only supported on Amazon RDS MySQL\.

## Related Topics<a name="mysql_rds_set_configuration.related"></a>
+ [mysql\.rds\_show\_configuration](mysql_rds_show_configuration.md)