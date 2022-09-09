# Troubleshooting RDS for Oracle replicas<a name="oracle-read-replicas.troubleshooting"></a>

This section describes possible replication problems and solutions\.

**Topics**
+ [Monitoring Oracle replication lag](#oracle-read-replicas.troubleshooting.lag)
+ [Troubleshooting Oracle replication failure after adding or modifying triggers](#oracle-read-replicas.troubleshooting.triggers)

## Monitoring Oracle replication lag<a name="oracle-read-replicas.troubleshooting.lag"></a>

To monitor replication lag in Amazon CloudWatch, view the Amazon RDS `ReplicaLag` metric\. For more information about replication lag time, see [Monitoring read replication](USER_ReadRepl.md#USER_ReadRepl.Monitoring) and [Amazon CloudWatch metrics for Amazon RDS](rds-metrics.md)\.

For a read replica, if the lag time is too long, query the following views:
+ `V$ARCHIVED_LOG` – Shows which commits have been applied to the read replica\.
+ `V$DATAGUARD_STATS` – Shows a detailed breakdown of the components that make up the `ReplicaLag` metric\.
+ `V$DATAGUARD_STATUS` – Shows the log output from Oracle's internal replication processes\.

For a mounted replica, if the lag time is too long, you can't query the `V$` views\. Instead, do the following:
+ Check the `ReplicaLag` metric in CloudWatch\.
+ Check the alert log file for the replica in the console\. Look for errors in the recovery messages\. The messages include the log sequence number, which you can compare to the primary sequence number\. For more information, see [Oracle database log files](USER_LogAccess.Concepts.Oracle.md)\.

## Troubleshooting Oracle replication failure after adding or modifying triggers<a name="oracle-read-replicas.troubleshooting.triggers"></a>

If you add or modify any triggers, and if replication fails afterward, the problem may be the triggers\. Ensure that the trigger excludes the following user accounts, which are required by RDS for replication:
+ User accounts with administrator privileges
+ `SYS`
+ `SYSTEM`
+ `RDS_DATAGUARD`
+ `rdsdb`

For more information, see [Miscellaneous considerations for RDS for Oracle replicas](oracle-read-replicas.limitations.md#oracle-read-replicas.limitations.miscellaneous)\.