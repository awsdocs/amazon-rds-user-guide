# Troubleshooting Oracle replicas<a name="oracle-read-replicas.troubleshooting"></a>

This section describes possible replication problems and solutions\.

## Replication lag<a name="oracle-read-replicas.troubleshooting.lag"></a>

To monitor replication lag in Amazon CloudWatch, view the Amazon RDS `ReplicaLag` metric\. For information about replication lag time, see [Monitoring read replication](USER_ReadRepl.md#USER_ReadRepl.Monitoring)\.

If replication lag is too long, query the following views:
+ `V$ARCHIVED_LOG` – Shows which commits have been applied to the read replica\.
+ `V$DATAGUARD_STATS` – Shows a detailed breakdown of the components that make up the `replicaLag` metric\.
+ `V$DATAGUARD_STATUS` – Shows the log output from Oracle's internal replication processes\.

## Replication failure after adding or modifying triggers<a name="oracle-read-replicas.troubleshooting.triggers"></a>

If you add or modify any triggers, and if replication fails afterward, the problem may be the triggers\. Ensure that the trigger excludes the following user accounts, which are required by RDS for replication:
+ User accounts with administrator privileges
+ `SYS`
+ `SYSTEM`
+ `RDS_DATAGUARD`
+ `rdsdb`

For more information, see [Miscellaneous requirements and restrictions for Oracle replicas](oracle-read-replicas.limitations.md#oracle-read-replicas.limitations.miscellaneous)\.