# mysql\.rds\_innodb\_buffer\_pool\_load\_abort<a name="mysql_rds_innodb_buffer_pool_load_abort"></a>

Cancels a load of the saved buffer pool state while in progress\. For more information, see [InnoDB Cache Warming](CHAP_MySQL.md#MySQL.Concepts.InnoDBCacheWarming)\.

## Syntax<a name="mysql_rds_innodb_buffer_pool_load_abort-syntax"></a>

```
CALL mysql.rds_innodb_buffer_pool_load_abort();
```

## Usage Notes<a name="mysql_rds_innodb_buffer_pool_load_abort-usage"></a>

The master user must run the `mysql.rds_innodb_buffer_pool_load_abort` procedure\. 

The `mysql.rds_innodb_buffer_pool_load_abort` procedure is available in these versions of Amazon RDS MySQL:
+ MySQL 5\.6
+ MySQL 5\.7
+ MySQL 8\.0