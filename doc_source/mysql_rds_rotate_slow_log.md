# mysql\.rds\_rotate\_slow\_log<a name="mysql_rds_rotate_slow_log"></a>

Rotates the `mysql.slow_log` table to a backup table\. For more information, see [MySQL Database Log Files](USER_LogAccess.Concepts.MySQL.md)\.

## Syntax<a name="mysql_rds_rotate_slow_log-syntax"></a>

```
CALL mysql.rds_rotate_slow_log;
```

## Usage Notes<a name="mysql_rds_rotate_slow_log-usage-notes"></a>

You can rotate the `mysql.slow_log` table to a backup table by calling the `mysql.rds_rotate_slow_log` procedure\. When log tables are rotated, the current log table is copied to a backup log table and the entries in the current log table are removed\. If a backup log table already exists, then it is deleted before the current log table is copied to the backup\. 

You can query the backup log table if needed\. The backup log table for the `mysql.slow_log` table is named `mysql.slow_log_backup`\. 

The `mysql.rds_rotate_slow_log` procedure is available in these versions of Amazon RDS MySQL:

+ MySQL 5\.5

+ MySQL 5\.6

+ MySQL 5\.7

## Related Topics<a name="mysql_rds_rotate_slow_log.related"></a>

+ [mysql\.rds\_rotate\_general\_log](mysql_rds_rotate_general_log.md)