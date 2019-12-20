# mysql\.rds\_innodb\_buffer\_pool\_dump\_now<a name="mysql_rds_innodb_buffer_pool_dump_now"></a>

Dumps the current state of the buffer pool to disk\. For more information, see [InnoDB Cache Warming](CHAP_MySQL.md#MySQL.Concepts.InnoDBCacheWarming)\.

## Syntax<a name="mysql_rds_innodb_buffer_pool_dump_now-syntax"></a>

```
CALL mysql.rds_innodb_buffer_pool_dump_now();
```

## Usage Notes<a name="mysql_rds_innodb_buffer_pool_dump_now-usage"></a>

The master user must run the `mysql.rds_innodb_buffer_pool_dump_now` procedure\. 

The `mysql.rds_innodb_buffer_pool_dump_now` procedure is available in these versions of Amazon RDS MySQL:
+ MySQL 5\.6
+ MySQL 5\.7
+ MySQL 8\.0