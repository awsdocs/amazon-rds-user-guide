# Working with Read Replicas for Microsoft SQL Server in Amazon RDS<a name="SQLServer.ReadReplicas"></a>

You usually use read replicas to configure replication between Amazon RDS DB instances\. For general information about read replicas, see [Working with Read Replicas](USER_ReadRepl.md)\. 

In this section, you can find specific information about working with read replicas on Amazon RDS for SQL Server\.

**Topics**
+ [Configuring Read Replicas for SQL Server](#SQLServer.ReadReplicas.Configuration)
+ [Read Replica Limitations with SQL Server](#SQLServer.ReadReplicas.Limitations)
+ [Troubleshooting a SQL Server Read Replica Problem](#SQLServer.ReadReplicas.Troubleshooting)

## Configuring Read Replicas for SQL Server<a name="SQLServer.ReadReplicas.Configuration"></a>

Before a DB instance can serve as a source instance for replication, you must enable automatic backups on the source DB instance\. To do so, you set the backup retention period to a value other than 0\. The source DB instance must be a Multi\-AZ deployment with Always On Availability Groups \(AGs\)\. Setting this type of deployment also enforces that automatic backups are enabled\.

Creating a SQL Server read replica doesn't require an outage for the primary DB instance\. Amazon RDS sets the necessary parameters and permissions for the source DB instance and the read replica without any service interruption\. A snapshot is taken of the source DB instance, and this snapshot becomes the read replica\. No outage occurs when you delete a read replica\. 

You can create up to five read replicas from one source DB instance\. For replication to operate effectively, each read replica should have the same amount of compute and storage resources as the source DB instance\. If you scale the source DB instance, also scale the read replicas\.

The SQL Server DB engine version of the source DB instance and all of its read replicas must be the same\. Amazon RDS upgrades the primary immediately after upgrading the read replicas, regardless of the maintenance window\. For more information about upgrading the DB engine version, see [Upgrading the Microsoft SQL Server DB Engine](USER_UpgradeDBInstance.SQLServer.md)\.

For a read replica to receive and apply changes from the source, it should have sufficient compute and storage resources\. If a read replica reaches compute, network, or storage resource capacity, the read replica stops receiving or applying changes from its source\. You can modify the storage and CPU resources of a read replica independently from its source and other read replicas\. 

## Read Replica Limitations with SQL Server<a name="SQLServer.ReadReplicas.Limitations"></a>

The following limitations apply to SQL Server read replicas on Amazon RDS:
+ Read replicas are only available on the SQL Server Enterprise Edition \(EE\) engine\.
+ Read replicas are available for SQL Server versions 2016 and 2017\.
+ The source DB instance to be replicated must be a Multi\-AZ deployment with Always On AGs\.
+ Read replicas are only available for DB instances on the EC2\-VPC platform\.
+ Read replicas are only available for DB instances running on DB instance classes with four or more vCPUs\.
+ The following aren't supported on Amazon RDS for SQL Server:
  + Creating a read replica in a different AWS Region \(a cross\-Region read replica\)
  + Backup retention of read replicas
  + Point\-in\-time recovery from read replicas
  + Manual snapshots of read replicas
  + Multi\-AZ read replicas
  + Creating read replicas of read replicas
  + Synchronization of user logins to read replicas
+ Amazon RDS for SQL Server doesn't intervene to mitigate high replica lag between a source DB instance and its read replicas\. Make sure that the source DB instance and its read replicas are sized properly, in terms of computing power and storage, to suit their operational load\.
+ A SQL Server read replica belongs to the same option group as the source DB instance\. Modifications to the source option group or source option group membership propagate to read replicas\. These changes are applied to the read replicas immediately after they are applied to the source DB instance, regardless of the read replica's maintenance window\.

  For more information about option groups, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\.

## Troubleshooting a SQL Server Read Replica Problem<a name="SQLServer.ReadReplicas.Troubleshooting"></a>

You can monitor replication lag in Amazon CloudWatch by viewing the Amazon RDS `ReplicaLag` metric\. For information about replication lag time, see [Monitoring Read Replication](USER_ReadRepl.md#USER_ReadRepl.Monitoring)\.

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