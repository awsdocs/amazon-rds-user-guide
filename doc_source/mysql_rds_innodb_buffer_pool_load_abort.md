# mysql\.rds\_innodb\_buffer\_pool\_load\_abort<a name="mysql_rds_innodb_buffer_pool_load_abort"></a>

Cancels a load of the saved buffer pool state while in progress\. For more information, see [InnoDB cache warming for MySQL on Amazon RDS](MySQL.Concepts.FeatureSupport.md#MySQL.Concepts.InnoDBCacheWarming)\.

## Syntax<a name="mysql_rds_innodb_buffer_pool_load_abort-syntax"></a>

 

```
CALL mysql.rds_innodb_buffer_pool_load_abort();
```

## Usage notes<a name="mysql_rds_innodb_buffer_pool_load_abort-usage"></a>

The master user must run the `mysql.rds_innodb_buffer_pool_load_abort` procedure\. 