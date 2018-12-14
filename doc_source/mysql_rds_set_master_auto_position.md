# mysql\.rds\_set\_master\_auto\_position<a name="mysql_rds_set_master_auto_position"></a>

Sets the replication mode to be based on either binary log file positions or on global transaction identifiers \(GTIDs\)\.

## Syntax<a name="mysql_rds_set_master_auto_position-syntax"></a>

```
CALL mysql.rds_set_master_auto_position (
auto_position_mode
);
```

## Parameters<a name="mysql_rds_set_master_auto_position-parameters"></a>

 *auto\_position\_mode*   
A value that indicates whether to use log file position replication or GTID\-based replication:  
+ `0` – Use the replication method based on binary log file position\. The default is `0`\.
+ `1` – Use the GTID\-based replication method\.

## Usage Notes<a name="mysql_rds_set_master_auto_position-usage-notes"></a>

The master user must run the `mysql.rds_set_master_auto_position` procedure\.

For Amazon RDS MySQL 5\.7, this procedure is supported for MySQL 5\.7\.23 and later MySQL 5\.7 versions\. This procedure is not supported for Amazon RDS MySQL 5\.5, 5\.6, or 8\.0\.