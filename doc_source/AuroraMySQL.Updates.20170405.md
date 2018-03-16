# Amazon Aurora MySQL Database Engine Updates: 2017\-04\-05<a name="AuroraMySQL.Updates.20170405"></a>

**Version:** 1\.12

Amazon Aurora MySQL 1\.12 is now the preferred version for the creation of new DB clusters, including restores from snapshots\.

This is not a mandatory upgrade for existing clusters\. You will have the option to upgrade existing clusters to version 1\.12 after we complete the fleet\-wide patch to 1\.11 \(see Aurora 1\.11 [release notes](AuroraMySQL.Updates.20170223.md) and corresponding [forum announcement](https://forums.aws.amazon.com/ann.jspa?annID=4444)\)\. With version 1\.12 of Aurora, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. For more information, see [Amazon RDS Maintenance](USER_UpgradeDBInstance.Maintenance.md)\.

## New Features<a name="AuroraMySQL.Updates.20170405.New"></a>

+ **Fast DDL** – Amazon Aurora MySQL now allows you to execute an ALTER TABLE *tbl\_name* ADD COLUMN *col\_name* *column\_definition* operation nearly instantaneously\. The operation completes without requiring the table to be copied and without materially impacting other DML statements\. Since it does not consume temporary storage for a table copy, it makes DDL statements practical even for large tables on small instance types\. Fast DDL is currently only supported for adding a nullable column, without a default value, at the end of a table\. This feature is currently available in Aurora lab mode\. For more information, see [Altering Tables in Amazon Aurora Using Fast DDL](AuroraMySQL.Managing.md#AuroraMySQL.Managing.FastDDL)\.

+ **Show volume status** – We have added a new monitoring command, SHOW VOLUME STATUS, to display the number of nodes and disks in a volume\. For more information, see [Displaying Volume Status for an Aurora DB Cluster](AuroraMySQL.Managing.md#AuroraMySQL.Managing.VolumeStatus)\.

## Improvements<a name="AuroraMySQL.Updates.20170405.Improvements"></a>

+ Implemented changes to lock compression to further reduce memory allocated per lock object\. This improvement is available in lab mode\.

+ Fixed an issue where the `trx_active_transactions` metric decrements rapidly even when the database is idle\.

+ Fixed an invalid error message regarding fault injection query syntax when simulating failure in disks and nodes\.

+ Fixed multiple issues related to race conditions and dead latches in the lock manager\.

+ Fixed an issue causing a buffer overflow in the query optimizer\.

+ Fixed a stability issue in Aurora read replicas when the underlying storage nodes experience low available memory\.

+ Fixed an issue where idle connections persisted past the `wait_timeout` parameter setting\.

+ Fixed an issue where `query_cache_size` returns an unexpected value after reboot of the instance\.

+ Fixed a performance issue that is the result of a diagnostic thread probing the network too often in the event that writes are not progressing to storage\.

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.20170405.BugFixes"></a>

+ Reloading a table that was evicted while empty caused an AUTO\_INCREMENT value to be reset\. \(Bug \#21454472, Bug \#77743\)

+ An index record was not found on rollback due to inconsistencies in the purge\_node\_t structure\. The inconsistency resulted in warnings and error messages such as “error in sec index entry update”, “unable to purge a record”, and “tried to purge sec index entry not marked for deletion”\. \(Bug \#19138298, Bug \#70214, Bug \#21126772, Bug \#21065746\) 

+ Wrong stack size calculation for qsort operation leads to stack overflow\. \(Bug \#73979\)

+ Record not found in an index upon rollback\. \(Bug \#70214, Bug \#72419\)

+ ALTER TABLE add column TIMESTAMP on update CURRENT\_TIMESTAMP inserts ZERO\-datas \(Bug \#17392\)