# Working with read replicas for Microsoft SQL Server in Amazon RDS<a name="SQLServer.ReadReplicas"></a>

You usually use read replicas to configure replication between Amazon RDS DB instances\. For general information about read replicas, see [Working with read replicas](USER_ReadRepl.md)\. 

In this section, you can find specific information about working with read replicas on Amazon RDS for SQL Server\.

**Topics**
+ [Configuring read replicas for SQL Server](#SQLServer.ReadReplicas.Configuration)
+ [Read replica limitations with SQL Server](#SQLServer.ReadReplicas.Limitations)
+ [Option considerations for RDS for SQL Server replicas](#SQLServer.ReadReplicas.limitations.options)
+ [Troubleshooting a SQL Server read replica problem](#SQLServer.ReadReplicas.Troubleshooting)

## Configuring read replicas for SQL Server<a name="SQLServer.ReadReplicas.Configuration"></a>

Before a DB instance can serve as a source instance for replication, you must enable automatic backups on the source DB instance\. To do so, you set the backup retention period to a value other than 0\. The source DB instance must be a Multi\-AZ deployment with Always On Availability Groups \(AGs\)\. Setting this type of deployment also enforces that automatic backups are enabled\.

Creating a SQL Server read replica doesn't require an outage for the primary DB instance\. Amazon RDS sets the necessary parameters and permissions for the source DB instance and the read replica without any service interruption\. A snapshot is taken of the source DB instance, and this snapshot becomes the read replica\. No outage occurs when you delete a read replica\. 

You can create up to five read replicas from one source DB instance\. For replication to operate effectively, we recommend that you configure each read replica with the same amount of compute and storage resources as the source DB instance\. If you scale the source DB instance, also scale the read replicas\.

The SQL Server DB engine version of the source DB instance and all of its read replicas must be the same\. Amazon RDS upgrades the primary immediately after upgrading the read replicas, regardless of the maintenance window\. For more information about upgrading the DB engine version, see [Upgrading the Microsoft SQL Server DB engine](USER_UpgradeDBInstance.SQLServer.md)\.

For a read replica to receive and apply changes from the source, it should have sufficient compute and storage resources\. If a read replica reaches compute, network, or storage resource capacity, the read replica stops receiving or applying changes from its source\. You can modify the storage and CPU resources of a read replica independently from its source and other read replicas\. 

## Read replica limitations with SQL Server<a name="SQLServer.ReadReplicas.Limitations"></a>

The following limitations apply to SQL Server read replicas on Amazon RDS:
+ Read replicas are only available on the SQL Server Enterprise Edition \(EE\) engine\.
+ Read replicas are available for SQL Server versions 2016–2019\.
+ The source DB instance to be replicated must be a Multi\-AZ deployment with Always On AGs\.
+ Read replicas are only available for DB instances running on DB instance classes with four or more vCPUs\.
+ The following aren't supported on Amazon RDS for SQL Server:
  + Backup retention of read replicas
  + Point\-in\-time recovery from read replicas
  + Manual snapshots of read replicas
  + Multi\-AZ read replicas
  + Creating read replicas of read replicas
  + Synchronization of user logins to read replicas
+ Amazon RDS for SQL Server doesn't intervene to mitigate high replica lag between a source DB instance and its read replicas\. Make sure that the source DB instance and its read replicas are sized properly, in terms of computing power and storage, to suit their operational load\.

## Option considerations for RDS for SQL Server replicas<a name="SQLServer.ReadReplicas.limitations.options"></a>

Before you create an RDS for SQL Server replica, consider the following requirements, restrictions, and recommendations:
+ If your SQL Server replica is in the same Region as its source DB instance, make sure that it belongs to the same option group as the source DB instance\. Modifications to the source option group or source option group membership propagate to replicas\. These changes are applied to the replicas immediately after they are applied to the source DB instance, regardless of the replica's maintenance window\.

  For more information about option groups, see [Working with option groups](USER_WorkingWithOptionGroups.md)\.
+ When you create a SQL Server cross\-Region replica, Amazon RDS creates a dedicated option group for it\.

  You can't remove an SQL Server cross\-Region replica from its dedicated option group\. No other DB instances can use the dedicated option group for a SQL Server cross\-Region replica\.

  The following options are replicated options\. To add replicated options to a SQL Server cross\-Region replica, add it to the source DB instance's option group\. The option is also installed on all of the source DB instance's replicas\.
  + `TDE`

  The following options are non\-replicated options\. You can add or remove non\-replicated options from a dedicated option group\.
  + `MSDTC`
  + `SQLSERVER_AUDIT`
  + To enable the `SQLSERVER_AUDIT` option on cross\-Region read replica, add the `SQLSERVER_AUDIT` option on the dedicated option group on the cross\-region read replica and the source instance’s option group\. By adding the `SQLSERVER_AUDIT` option on the source instance of SQL Server cross\-Region read replica, you can create Server Level Audit Object and Server Level Audit Specifications on each of the cross\-Region read replicas of the source instance\. To allow the cross\-Region read replicas access to upload the completed audit logs to an Amazon S3 bucket, add the `SQLSERVER_AUDIT` option to the dedicated option group and configure the option settings\. The Amazon S3 bucket that you use as a target for audit files must be in the same Region as the cross\-Region read replica\. You can modify the option setting of the `SQLSERVER_AUDIT` option for each cross region read replica independently so each can access an Amazon S3 bucket in their respective Region\.

  The following options are not supported for cross\-Region read replicas\.
  + `SSRS`
  + `SSAS`
  + `SSIS`

  The following options are partially supported for cross\-Region read replicas\.
  + `SQLSERVER_BACKUP_RESTORE`
  + The source DB instance of a SQL Server cross\-Region replica can have the `SQLSERVER_BACKUP_RESTORE` option, but you can not perform native restores on the source DB instance until you delete all its cross\-Region replicas\. Any existing native restore tasks will be cancelled during the creation of a cross\-Region replica\. You can't add the `SQLSERVER_BACKUP_RESTORE` option to a dedicated option group\.

    For more information on native backup and restore, see [Importing and exporting SQL Server databases using native backup and restore](SQLServer.Procedural.Importing.md)

  When you promote a SQL Server cross\-Region read replica, the promoted replica behaves the same as other SQL Server DB instances, including the management of its options\. For more information about option groups, see [Working with option groups](USER_WorkingWithOptionGroups.md)\.

## Troubleshooting a SQL Server read replica problem<a name="SQLServer.ReadReplicas.Troubleshooting"></a>

You can monitor replication lag in Amazon CloudWatch by viewing the Amazon RDS `ReplicaLag` metric\. For information about replication lag time, see [Monitoring read replication](USER_ReadRepl.md#USER_ReadRepl.Monitoring)\.

If replication lag is too long, you can use the following query to get information about the lag\.

```
SELECT AR.replica_server_name
     , DB_NAME (ARS.database_id) 'database_name'
     , AR.availability_mode_desc
     , ARS.synchronization_health_desc
     , ARS.last_hardened_lsn
     , ARS.last_redone_lsn
     , ARS.secondary_lag_seconds
FROM sys.dm_hadr_database_replica_states ARS
INNER JOIN sys.availability_replicas AR ON ARS.replica_id = AR.replica_id
--WHERE DB_NAME(ARS.database_id) = 'database_name'
ORDER BY AR.replica_server_name;
```