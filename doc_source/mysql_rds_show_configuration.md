# mysql\.rds\_show\_configuration<a name="mysql_rds_show_configuration"></a>

The number of hours that binary logs are retained\.

## Syntax<a name="mysql_rds_show_configuration-syntax"></a>

```
CALL mysql.rds_show_configuration;
```

## Usage Notes<a name="mysql_rds_show_configuration-usage-notes"></a>

To verify the number of hours that Amazon RDS retains binary logs, use the `mysql.rds_show_configuration` stored procedure\.

The `mysql.rds_show_configuration` procedure is available in these versions of Amazon RDS MySQL:
+ MySQL 5\.6
+ MySQL 5\.7
+ MySQL 8\.0

## Examples<a name="mysql_rds_show_configuration-examples"></a>

The following example displays the retention period:

```
call mysql.rds_show_configuration;
                name                         value     description
                binlog retention hours       24        binlog retention hours specifies the duration in hours before binary logs are automatically deleted.
```