# Working with Oracle Read Replicas for Amazon RDS<a name="oracle-read-replicas"></a>

You usually use Read Replicas to configure replication between Amazon RDS DB instances\. For general information about Read Replicas, see [Working with Read Replicas](USER_ReadRepl.md)\. 

In this section, you can find specific information about working with Read Replicas on Amazon RDS for Oracle\.

**Topics**
+ [Configuring Read Replicas for Oracle](#oracle-read-replicas.Configuration)
+ [Read Replica Limitations with Oracle](#oracle-read-replicas.limitations)
+ [Troubleshooting an Oracle Read Replica Problem](#oracle-read-replicas.troubleshooting)

## Configuring Read Replicas for Oracle<a name="oracle-read-replicas.Configuration"></a>

Oracle Read Replicas use Oracle Data Guard to replicate database changes from the source DB instance to its Read Replicas\. For information about Oracle Data Guard, see [Oracle Data Guard Concepts and Administration](https://docs.oracle.com/database/121/SBYDB/toc.htm) in the Oracle documentation\.

Before a DB instance can serve as a source DB instance, you must enable automatic backups on the source DB instance\. To do so, you set the backup retention period to a value other than 0\. We recommend that you enable force logging mode on the DB instance\. To enable force logging mode, connect to the DB instance and run the following procedure\. 

```
exec rdsadmin.rdsadmin_util.force_logging(p_enable => true);            
```

For more information about this procedure, see [Setting Force Logging](Appendix.Oracle.CommonDBATasks.Log.md#Appendix.Oracle.CommonDBATasks.SettingForceLogging)\.

If you plan changes to your logging configuration, we recommend that you complete the changes before making a DB instance the source for one or more Read Replicas\. You can modify the logging configuration for a DB instance by using the Amazon RDS procedures `rdsadmin.rdsadmin_util.add_logfile` and `rdsadmin.rdsadmin_util.drop_logfile`\. For more information, see [Adding Online Redo Logs](Appendix.Oracle.CommonDBATasks.Log.md#Appendix.Oracle.CommonDBATasks.RedoLogs) and [Dropping Online Redo Logs](Appendix.Oracle.CommonDBATasks.Log.md#Appendix.Oracle.CommonDBATasks.DroppingRedoLogs)\. As a best practice, we recommend that you not modify the logging configuration after Read Replicas are created\. Modifications can cause the online redo logging configuration to get out of sync with the standby logging configuration\. 

Creating an Oracle Read Replica doesn't require an outage for the master DB instance\. Amazon RDS sets the necessary parameters and permissions for the source DB instance and the Read Replica without any service interruption\. A snapshot is taken of the source DB instance, and this snapshot becomes the Read Replica\. No outage occurs when you delete a Read Replica\. 

You can create up to five Read Replicas from one source DB instance\. Before you create a Read Replica, make sure that the setting of the `max_string_size` parameter is same on the source DB instance and the Read Replica\. You can do this by associating them with the same parameter group\. If you have different parameters groups for the source and the Read Replica, you can do this by setting `max_string_size` to the same value\.

The Oracle DB engine version of the source DB instance and all of its Read Replicas must be the same\. Amazon RDS upgrades the Read Replicas immediately after upgrading the source DB instance, regardless of a Read Replica's maintenance window\. For more information about upgrading the DB engine version, see [Upgrading the Oracle DB Engine](USER_UpgradeDBInstance.Oracle.md)\.

For a Read Replica to receive and apply changes from the source, it should have sufficient compute and storage resources\. If a Read Replica reaches compute, network, or storage resource capacity, the Read Replica stops receiving or applying changes from its source\. You can modify the storage and CPU resources of a Read Replica independently from its source and other Read Replicas\. 

## Read Replica Limitations with Oracle<a name="oracle-read-replicas.limitations"></a>

The following are limitations for Oracle Read Replicas: 
+ You must have an Active Data Guard license\.
+ Oracle Read Replicas are only available on the Oracle Enterprise Edition \(EE\) engine\.
+ Oracle Read Replicas are available for Oracle version 12\.1\.0\.2\.v10 and higher 12\.1 versions, for all 12\.2 versions, and for all 18 versions\.
+ Oracle Read Replicas are only available for DB instances on the EC2\-VPC platform\.
+ Oracle Read Replicas are only available for DB instances running on DB instance classes with two or more vCPUs\.
+ Amazon RDS for Oracle doesn't intervene to mitigate high replica lag between a source DB instance and its Read Replicas\. Ensure that the source DB instance and its Read Replicas are sized properly, in terms of compute and storage, to suit their operational load\.
+ Amazon RDS for Oracle Read Replicas must belong to the same option group as the source database\. Modifications to the source option group or source option group membership propagate to Read Replicas\. These changes are applied to the Read Replicas immediately after they are applied to the source DB instance, regardless of the Read Replica's maintenance window\.

  For more information about option groups, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\.
+ You can't create cross\-region Read Replicas\.
+ Currently, Oracle Read Replicas are not supported in the EU \(Stockholm\) and China \(Ningxia\) regions\.

## Troubleshooting an Oracle Read Replica Problem<a name="oracle-read-replicas.troubleshooting"></a>

You can monitor replication lag in Amazon CloudWatch by viewing the Amazon RDS `ReplicaLag` metric\. For information about replication lag time, see [Monitoring Read Replication](USER_ReadRepl.md#USER_ReadRepl.Monitoring)\.

If replication lag is too long, you can query the following views for information about the lag:
+ `V$ARCHIVED_LOG` – Shows which commits have been applied to the Read Replica\.
+ `V$DATAGUARD_STATS` – Shows a detailed breakdown of the components that make up the `replicaLag` metric\.
+ `V$DATAGUARD_STATUS` – Shows the log output from Oracle's internal replication processes\.