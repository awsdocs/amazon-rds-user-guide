# mysql\.rds\_skip\_transaction\_with\_gtid<a name="mysql_rds_skip_transaction_with_gtid"></a>

Skips replication of a transaction with the specified global transaction identifier \(GTID\) on a MySQL DB instance\.

You can use this procedure for disaster recovery when a specific GTID transaction is known to cause a problem\. Use this stored procedure to skip the problematic transaction\. Examples of problematic transactions include transactions that disable replication, delete important data, or cause the DB instance to become unavailable\.

## Syntax<a name="mysql_rds_skip_transaction_with_gtid-syntax"></a>

```
CALL mysql.rds_skip_transaction_with_gtid (
gtid_to_skip
);
```

## Parameters<a name="mysql_rds_start_replication_until_gtid-parameters"></a>

 *gtid\_to\_skip*   
The GTID of the replication transaction to skip\.

## Usage Notes<a name="mysql_rds_skip_transaction_with_gtid-usage-notes"></a>

The master user must run the `mysql.rds_skip_transaction_with_gtid` procedure\.

For Amazon RDS MySQL 5\.7, this procedure is supported for MySQL 5\.7\.23 and later MySQL 5\.7 versions\. This procedure is not supported for Amazon RDS MySQL 5\.5, 5\.6, or 8\.0\.