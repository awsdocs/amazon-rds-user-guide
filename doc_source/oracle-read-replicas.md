# Working with Oracle Read Replicas for Amazon RDS<a name="oracle-read-replicas"></a>

You usually use read replicas to configure replication between Amazon RDS DB instances\. For general information about read replicas, see [Working with Read Replicas](USER_ReadRepl.md)\. 

In this section, you can find specific information about working with read replicas on Amazon RDS for Oracle\.

**Topics**
+ [Configuring Read Replicas for Oracle](#oracle-read-replicas.Configuration)
+ [Read Replica Limitations with Oracle](#oracle-read-replicas.limitations)
+ [Troubleshooting an Oracle Read Replica Problem](#oracle-read-replicas.troubleshooting)

## Configuring Read Replicas for Oracle<a name="oracle-read-replicas.Configuration"></a>

Oracle read replicas use Oracle Data Guard to replicate database changes from the source DB instance to its read replicas\. For information about Oracle Data Guard, see [Oracle Data Guard Concepts and Administration](https://docs.oracle.com/database/121/SBYDB/toc.htm) in the Oracle documentation\.

Before a DB instance can serve as a source DB instance, you must enable automatic backups on the source DB instance\. To do so, you set the backup retention period to a value other than 0\. We recommend that you enable force logging mode on the DB instance\. To enable force logging mode, connect to the DB instance and run the following procedure\. 

```
exec rdsadmin.rdsadmin_util.force_logging(p_enable => true);            
```

For more information about this procedure, see [Setting Force Logging](Appendix.Oracle.CommonDBATasks.Log.md#Appendix.Oracle.CommonDBATasks.SettingForceLogging)\.

If you plan changes to your logging configuration, we recommend that you complete the changes before making a DB instance the source for one or more read replicas\. You can modify the logging configuration for a DB instance by using the Amazon RDS procedures `rdsadmin.rdsadmin_util.add_logfile` and `rdsadmin.rdsadmin_util.drop_logfile`\. For more information, see [Adding Online Redo Logs](Appendix.Oracle.CommonDBATasks.Log.md#Appendix.Oracle.CommonDBATasks.RedoLogs) and [Dropping Online Redo Logs](Appendix.Oracle.CommonDBATasks.Log.md#Appendix.Oracle.CommonDBATasks.DroppingRedoLogs)\. As a best practice, we recommend that you not modify the logging configuration after read replicas are created\. Modifications can cause the online redo logging configuration to get out of sync with the standby logging configuration\. 

Creating an Oracle read replica doesn't require an outage for the master DB instance\. Amazon RDS sets the necessary parameters and permissions for the source DB instance and the read replica without any service interruption\. A snapshot is taken of the source DB instance, and this snapshot becomes the read replica\. No outage occurs when you delete a read replica\. 

You can create up to five read replicas from one source DB instance\. Before you create a read replica, make sure that the setting of the `max_string_size` parameter is same on the source DB instance and the read replica\. You can do this by associating them with the same parameter group\. If you have different parameter groups for the source and the read replica, you can set `max_string_size` to the same value\.

The Oracle DB engine version of the source DB instance and all of its read replicas must be the same\. Amazon RDS upgrades the read replicas immediately after upgrading the source DB instance, regardless of a read replica's maintenance window\. For major version upgrades of cross\-Region read replicas, Amazon RDS automatically generates an option group for the target version, copies all options and option settings from the original option group to the new option group, and associates the upgraded cross\-Region read replica with the new option group\. For more information about upgrading the DB engine version, see [Upgrading the Oracle DB Engine](USER_UpgradeDBInstance.Oracle.md)\.

For a read replica to receive and apply changes from the source, it should have sufficient compute and storage resources\. If a read replica reaches compute, network, or storage resource capacity, the read replica stops receiving or applying changes from its source\. You can modify the storage and CPU resources of a read replica independently from its source and other read replicas\. 

## Read Replica Limitations with Oracle<a name="oracle-read-replicas.limitations"></a>

The following are limitations for Oracle read replicas: 
+ You must have an Active Data Guard license\.
+ Oracle read replicas are only available on the Oracle Enterprise Edition \(EE\) engine\.
+ Oracle read replicas are available for Oracle version 12\.1\.0\.2\.v10 and higher 12\.1 versions, for all 12\.2 versions, for all 18 versions, and for all 19 versions\.
+ Oracle read replicas are only available for DB instances on the EC2\-VPC platform\.
+ Oracle read replicas are only available for DB instances running on DB instance classes with two or more vCPUs\. A source DB instance can't use the db\.t3\.micro instance class\.
+ Amazon RDS for Oracle doesn't intervene to mitigate high replica lag between a source DB instance and its read replicas\. Ensure that the source DB instance and its read replicas are sized properly, in terms of compute and storage, to suit their operational load\.
+ An Oracle read replica that is in the same AWS Region as its source DB instance must belong to the same option group as the source DB instance\. Modifications to the source option group or source option group membership propagate to read replicas\. These changes are applied to the read replicas immediately after they are applied to the source DB instance, regardless of the read replica's maintenance window\.

  For more information about option groups, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\.
+ If a DB instance is a source for one or more cross\-Region read replicas, it retains its redo logs until they are applied on all cross\-Region read replicas\. The redo logs might result in increased storage consumption\. 
+ When you create an Oracle cross\-Region read replica, Amazon RDS creates a dedicated option group for it\.

  You can't remove an Oracle cross\-Region read replica from its dedicated option group\. No other DB instances can use the dedicated option group for an Oracle cross\-Region read replica\.

  Only the following nonreplicated options can be added to or removed from a dedicated option group:
  + NATIVE\_NETWORK\_ENCRYPTION
  + OEM
  + OEM\_AGENT
  + SSL

  To add other options to an Oracle cross\-Region read replica, add them to the source DB instance's option group\. The option is also installed on all of the source DB instance's read replicas\. For licensed options, you must ensure that there are sufficient licenses for the read replicas\.

  When you promote an Oracle cross\-Region read replica, the promoted read replica behaves the same as other Oracle DB instances, including the management of its options\. You can promote a read replica explicitly or implicitly by deleting its source DB instance\.

  For more information about option groups, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\.
+ A login trigger on a primary instance must permit access to the `RDS_DATAGUARD` user and to any user whose `AUTHENTICATED_ENTITY` is `RDS_DATAGUARD` or `rdsdb`\. Also, the trigger must not set the current schema for the `RDS_DATAGUARD` user\.
+ To avoid blocking connections from the Data Guard broker process, don't enable restricted sessions\. For more information about restricted sessions, see [Enabling and Disabling Restricted Sessions](Appendix.Oracle.CommonDBATasks.System.md#Appendix.Oracle.CommonDBATasks.RestrictedSession)\.

## Troubleshooting an Oracle Read Replica Problem<a name="oracle-read-replicas.troubleshooting"></a>

You can monitor replication lag in Amazon CloudWatch by viewing the Amazon RDS `ReplicaLag` metric\. For information about replication lag time, see [Monitoring Read Replication](USER_ReadRepl.md#USER_ReadRepl.Monitoring)\.

If replication lag is too long, you can query the following views for information about the lag:
+ `V$ARCHIVED_LOG` – Shows which commits have been applied to the read replica\.
+ `V$DATAGUARD_STATS` – Shows a detailed breakdown of the components that make up the `replicaLag` metric\.
+ `V$DATAGUARD_STATUS` – Shows the log output from Oracle's internal replication processes\.