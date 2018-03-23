# mysql\.rds\_set\_configuration<a name="mysql_rds_set_configuration"></a>

Specifies the number of hours to retain binary logs\.

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

The `mysql.rds_set_configuration` procedure currently supports only the `binlog retention hours` configuration parameter\. The `binlog retention hours` parameter is used to specify the number of hours to retain binary log files\. Amazon RDS normally purges a binary log as soon as possible, but the binary log might still be required for replication with a MySQL database external to Amazon RDS\. The default value of `binlog retention hours` is NULL \(do not retain binary logs\)\.

To specify the number of hours for Amazon RDS to retain binary logs on a DB instance, use the `mysql.rds_set_configuration` stored procedure and specify a period with enough time for replication to occur, as shown in the following example\.

`call mysql.rds_set_configuration('binlog retention hours', 24);`

For MySQL DB instances, the maximum `binlog retention hours` value is 168 \(7 days\)\. For Amazon Aurora MySQL DB clusters, the maximum is 2160 \(90 days\)\.

After you set the retention period, monitor storage usage for the DB instance to ensure that the retained binary logs don't take up too much storage\.

The `mysql.rds_set_configuration` is available in these versions of Amazon RDS MySQL:
+ MySQL 5\.6
+ MySQL 5\.7

## Related Topics<a name="mysql_rds_set_configuration.related"></a>
+ [mysql\.rds\_show\_configuration](mysql_rds_show_configuration.md)