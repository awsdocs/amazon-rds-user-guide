# mysql\.rds\_innodb\_buffer\_pool\_load\_abort<a name="mysql_rds_innodb_buffer_pool_load_abort"></a>

Cancels a load of the saved buffer pool state while in progress\. For more information, see [InnoDB Cache Warming](CHAP_MySQL.md#MySQL.Concepts.InnoDBCacheWarming)\.

## Syntax<a name="mysql_rds_innodb_buffer_pool_load_abort-syntax"></a>

```
CALL mysql.rds_innodb_buffer_pool_load_abort();
```

## Usage Notes<a name="mysql_rds_innodb_buffer_pool_load_abort-usage"></a>

The `mysql.rds_innodb_buffer_pool_load_abort` procedure must be run by the master user\. 

The `mysql.rds_innodb_buffer_pool_load_abort` procedure is available in these versions of Amazon RDS MySQL:

+ MySQL 5\.6

+ MySQL 5\.7

## Related Topics<a name="mysql_rds_innodb_buffer_pool_load_abort.related"></a>

+ [mysql\.rds\_innodb\_buffer\_pool\_dump\_now](mysql_rds_innodb_buffer_pool_dump_now.md)

+ [mysql\.rds\_innodb\_buffer\_pool\_load\_now](mysql_rds_innodb_buffer_pool_load_now.md)