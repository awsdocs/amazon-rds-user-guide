# mysql\.rds\_remove\_binlog\_ssl\_material<a name="mysql_rds_remove_binlog_ssl_material"></a>

Removes the certificate authority certificate, client certificate, and client key for SSL communication and encrypted replication\. This information is imported by using [mysql\.rds\_import\_binlog\_ssl\_material](mysql_rds_import_binlog_ssl_material.md)\.

**Note**  
Currently, this procedure is only supported for Aurora MySQL version 5\.6\.

## Syntax<a name="mysql_rds_remove_binlog_ssl_material-syntax"></a>

```
CALL mysql.rds_remove_binlog_ssl_material;
```