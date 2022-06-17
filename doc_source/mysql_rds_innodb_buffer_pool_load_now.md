# mysql\.rds\_innodb\_buffer\_pool\_load\_now<a name="mysql_rds_innodb_buffer_pool_load_now"></a>

Loads the saved state of the buffer pool from disk\. For more information, see [InnoDB cache warming for MySQL on Amazon RDS](MySQL.Concepts.FeatureSupport.md#MySQL.Concepts.InnoDBCacheWarming)\.

## Syntax<a name="mysql_rds_innodb_buffer_pool_load_now-syntax"></a>

 

```
CALL mysql.rds_innodb_buffer_pool_load_now();
```

## Usage notes<a name="mysql_rds_innodb_buffer_pool_load_now-usage"></a>

The master user must run the `mysql.rds_innodb_buffer_pool_load_now` procedure\. 