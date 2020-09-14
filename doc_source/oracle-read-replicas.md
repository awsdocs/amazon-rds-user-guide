# Working with Oracle Replicas for Amazon RDS<a name="oracle-read-replicas"></a>

To configure replication between Oracle DB instances, you can create replica databases\. 

**Topics**
+ [Overview of Oracle Replicas](#oracle-read-replicas.overview)
+ [Replica Requirements for Oracle](#oracle-read-replicas.limitations)
+ [Preparing to Create an Oracle Replica](#oracle-read-replicas.Configuration)
+ [Creating an Oracle Replica in Mounted Mode](#oracle-read-replicas.creating-in-mounted-mode)
+ [Modifying the Oracle Replica Mode](#oracle-read-replicas.changing-replica-mode)
+ [Troubleshooting Oracle Replicas](#oracle-read-replicas.troubleshooting)

## Overview of Oracle Replicas<a name="oracle-read-replicas.overview"></a>

An Oracle *replica* database is either mounted or read\-only\. An Oracle replica in read\-only mode is called a *read replica*\. An Oracle replica in mounted mode is called a *mounted replica*\.

### Read\-Only and Mounted Replicas<a name="oracle-read-replicas.overview.modes"></a>

When creating or modifying an Oracle replica, you can place it in either of the following modes:
+ Read\-only\. This is the default\. Active Data Guard transmits and applies changes from the source database to all read replica databases\.

  You can create up to five read replicas from one source DB instance\. For general information about read replicas that applies to all DB engines, see [Working with Read Replicas](USER_ReadRepl.md)\. For information about Oracle Data Guard, see [Oracle Data Guard Concepts and Administration](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/oracle-data-guard-concepts.html#GUID-F78703FB-BD74-4F20-9971-8B37ACC40A65) in the Oracle documentation\.
+ Mounted\. In this case, replication uses Oracle Data Guard, but the replica database doesn't accept user connections\. The primary use for mounted replicas is cross\-Region disaster recovery\.

  A mounted replica can't serve a read\-only workload\. The mounted replica deletes archived redo log files after it applies them, regardless of the archived log retention policy\.

You can create a combination of mounted and read\-only DB replicas for the same source DB instance\. You can change a read\-only replica to mounted mode, or change a mounted replica to read\-only mode\. In either case, the Oracle database preserves the archived log retention setting\.

### Outages During Replication<a name="oracle-read-replicas.overview.outages"></a>

When you create an Oracle replica, no outage occurs for the source DB instance\. Amazon RDS takes a snapshot of the source DB instance\. This snapshot becomes the replica\. Amazon RDS sets the necessary parameters and permissions for the source DB and replica without service interruption\. Similarly, if you delete a replica, no outage occurs\.

## Replica Requirements for Oracle<a name="oracle-read-replicas.limitations"></a>

Before creating an Oracle replica, check the following requirements\.

### Version and Licensing Requirements for Oracle Replicas<a name="oracle-read-replicas.limitations.versions-and-licenses"></a>

Before creating an Oracle replica, check the version and licensing requirements:
+ If the replica is in read\-only mode, make sure that you have an Active Data Guard license\. If you place the replica in mounted mode, you don't need an Active Data Guard license\. Only the Oracle DB engine supports mounted replicas\.
+ Oracle replicas are only available on the Oracle Enterprise Edition \(EE\) engine\.
+ Oracle replicas are available for Oracle version 12\.1\.0\.2\.v10 and higher 12\.1 versions, for all 12\.2 versions, for all 18c versions, and for all 19c versions\.
+ Oracle replicas are available for DB instances only on the EC2\-VPC platform\.
+ Oracle replicas are available for DB instances running only on DB instance classes with two or more vCPUs\. A source DB instance can't use the db\.t3\.micro instance class\.
+ The Oracle DB engine version of the source DB instance and all of its replicas must be the same\. Amazon RDS upgrades the replicas immediately after upgrading the source DB instance, regardless of a replica's maintenance window\. For major version upgrades of cross\-Region replicas, Amazon RDS automatically does the following:
  + Generates an option group for the target version\.
  + Copies all options and option settings from the original option group to the new option group\.
  + Associates the upgraded cross\-Region replica with the new option group\.

  For more information about upgrading the DB engine version, see [Upgrading the Oracle DB Engine](USER_UpgradeDBInstance.Oracle.md)\.

### Option Requirements for Oracle Replicas<a name="oracle-read-replicas.limitations.options"></a>

Before creating a replica for Oracle, check the requirements for option groups: 
+ If your Oracle replica is in the same AWS Region as its source DB instance, make sure that it belongs to the same option group as the source DB instance\. Modifications to the source option group or source option group membership propagate to replicas\. These changes are applied to the replicas immediately after they are applied to the source DB instance, regardless of the replica's maintenance window\.

  For more information about option groups, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\.
+ When you create an Oracle cross\-Region replica, Amazon RDS creates a dedicated option group for it\.

  You can't remove an Oracle cross\-Region replica from its dedicated option group\. No other DB instances can use the dedicated option group for an Oracle cross\-Region replica\.

  You can only add or remove the following nonreplicated options from a dedicated option group:
  + `NATIVE_NETWORK_ENCRYPTION`
  + `OEM`
  + `OEM_AGENT`
  + `SSL`

  To add other options to an Oracle cross\-Region replica, add them to the source DB instance's option group\. The option is also installed on all of the source DB instance's replicas\. For licensed options, make sure that there are sufficient licenses for the replicas\.

  When you promote an Oracle cross\-Region replica, the promoted replica behaves the same as other Oracle DB instances, including the management of its options\. You can promote a replica explicitly or implicitly by deleting its source DB instance\.

  For more information about option groups, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\.

### Miscellaneous Requirements for Oracle Replicas<a name="oracle-read-replicas.limitations.miscellaneous"></a>

Before creating an Oracle replica, check the following miscellaneous requirements:
+ If a DB instance is a source for one or more cross\-Region replicas, the source DB retains its redo logs until they are applied on all cross\-Region replicas\. The redo logs might result in increased storage consumption\.
+ A login trigger on a primary instance must permit access to the `RDS_DATAGUARD` user and to any user whose `AUTHENTICATED_ENTITY` value is `RDS_DATAGUARD` or `rdsdb`\. Also, the trigger must not set the current schema for the `RDS_DATAGUARD` user\.
+ To avoid blocking connections from the Data Guard broker process, don't enable restricted sessions\. For more information about restricted sessions, see [Enabling and Disabling Restricted Sessions](Appendix.Oracle.CommonDBATasks.System.md#Appendix.Oracle.CommonDBATasks.RestrictedSession)\.

## Preparing to Create an Oracle Replica<a name="oracle-read-replicas.Configuration"></a>

Before you can begin using your replica, perform the following tasks\.

**Topics**
+ [Enabling Automatic Backups](#oracle-read-replicas.configuration.autobackups)
+ [Enabling Force Logging Mode](#oracle-read-replicas.configuration.force-logging)
+ [Changing Your Logging Configuration](#oracle-read-replicas.configuration.logging-config)
+ [Setting the MAX\_STRING\_SIZE Parameter](#oracle-read-replicas.configuration.string-size)
+ [Planning Compute and Storage Resources](#oracle-read-replicas.configuration.planning-resources)

### Enabling Automatic Backups<a name="oracle-read-replicas.configuration.autobackups"></a>

Before a DB instance can serve as a source DB instance, make sure to enable automatic backups on the source DB instance\. To learn how to perform this procedure, see [Enabling Automated Backups ](USER_WorkingWithAutomatedBackups.md#USER_WorkingWithAutomatedBackups.Enabling)\.

### Enabling Force Logging Mode<a name="oracle-read-replicas.configuration.force-logging"></a>

We recommend that you enable force logging mode\. In force logging mode, the Oracle database writes redo records even when `NOLOGGING` is used with data definition language \(DDL\) statements\.

**To enable force logging mode**

1. Log in to your Oracle database using a client tool such as SQL Developer\.

1. Enable force logging mode by running the following procedure\. 

   ```
   exec rdsadmin.rdsadmin_util.force_logging(p_enable => true);
   ```

For more information about this procedure, see [Setting Force Logging](Appendix.Oracle.CommonDBATasks.Log.md#Appendix.Oracle.CommonDBATasks.SettingForceLogging)\.

### Changing Your Logging Configuration<a name="oracle-read-replicas.configuration.logging-config"></a>

If you want to change your logging configuration, we recommend that you complete the changes before making a DB instance the source for replicas\. Also, we recommend that you not modify the logging configuration after you create the replicas\. Modifications can cause the online redo logging configuration to get out of sync with the standby logging configuration\.

Modify the logging configuration for a DB instance by using the Amazon RDS procedures `rdsadmin.rdsadmin_util.add_logfile` and `rdsadmin.rdsadmin_util.drop_logfile`\. For more information, see [Adding Online Redo Logs](Appendix.Oracle.CommonDBATasks.Log.md#Appendix.Oracle.CommonDBATasks.RedoLogs) and [Dropping Online Redo Logs](Appendix.Oracle.CommonDBATasks.Log.md#Appendix.Oracle.CommonDBATasks.DroppingRedoLogs)\.

### Setting the MAX\_STRING\_SIZE Parameter<a name="oracle-read-replicas.configuration.string-size"></a>

Before you create an Oracle replica, ensure that the setting of the `MAX_STRING_SIZE` parameter is the same on the source DB instance and the replica\. You can do this by associating them with the same parameter group\. If you have different parameter groups for the source and the replica, you can set `MAX_STRING_SIZE` to the same value\. For more information about setting this parameter, see [Enabling Extended Data Types for a New DB Instance](CHAP_Oracle.md#Oracle.Concepts.ExtendedDataTypes.CreateDBInstance)\.

### Planning Compute and Storage Resources<a name="oracle-read-replicas.configuration.planning-resources"></a>

Ensure that the source DB instance and its replicas are sized properly, in terms of compute and storage, to suit their operational load\. If a replica reaches compute, network, or storage resource capacity, the replica stops receiving or applying changes from its source\. Amazon RDS for Oracle doesn't intervene to mitigate high replica lag between a source DB instance and its replicas\. You can modify the storage and CPU resources of a replica independently from its source and other replicas\.

## Creating an Oracle Replica in Mounted Mode<a name="oracle-read-replicas.creating-in-mounted-mode"></a>

By default, Oracle replicas are read\-only\. To create a replica in mounted mode, use either the AWS CLI or RDS API\. 

### AWS CLI<a name="oracle-read-replicas.creating-in-mounted-mode.cli"></a>

To create an Oracle replica in mounted mode, set `--replica-mode` to `mounted` in the AWS CLI command [create\-db\-instance\-read\-replica](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html)\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds create-db-instance-read-replica \
    --db-instance-identifier myreadreplica \
    --source-db-instance-identifier mydbinstance \
    --replica-mode mounted
```
For Windows:  

```
aws rds create-db-instance-read-replica ^
    --db-instance-identifier myreadreplica ^
    --source-db-instance-identifier mydbinstance ^
    --replica-mode mounted
```

To change a read\-only replica to a mounted state, set `--replica-mode` to `mounted` in the AWS CLI command [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)\. To place a mounted replica in read\-only mode, set `--replica-mode` to `read-only`\. 

### RDS API<a name="oracle-read-replicas.creating-in-mounted-mode.api"></a>

To create an Oracle replica in mounted mode, specify `ReplicaMode=mounted` in the RDS API operation [CreateDBInstanceReadReplica](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)\. 

```
https://rds.amazonaws.com/
	?Action=CreateDBInstanceReadReplica
	&DBInstanceIdentifier=myreadreplica
	&SourceDBInstanceIdentifier=mydbinstance
	&Version=2012-01-15
	&SignatureVersion=2
	&SignatureMethod=HmacSHA256
	&Timestamp=2012-01-20T22%3A06%3A23.624Z
	&AWSAccessKeyId=<AWS Access Key ID>
	&Signature=<Signature>
	&ReplicaMode=mounted
```

## Modifying the Oracle Replica Mode<a name="oracle-read-replicas.changing-replica-mode"></a>

To change the replica mode of an existing replica database, use either the AWS CLI or RDS API\. When you change the replica mode to mounted, the replica disconnects all active connections\. When you change the mode to read\-only, Amazon RDS initializes Active Data Guard\.

The operation to change the replica mode can take a few minutes\. During the operation, the DB instance status changes to **modifying**\. For more information about status changes, see [DB Instance Status](Overview.DBInstance.Status.md)\.

### AWS CLI<a name="oracle-read-replicas.changing-replica-mode.cli"></a>

To change a read replica to mounted mode, set `--replica-mode` to `mounted` in the AWS CLI command [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)\. To change a mounted replica to read\-only mode, set `--replica-mode` to `read-only`\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier myreadreplica \
    --replica-mode mode
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier myreadreplica ^
    --replica-mode mode
```

### RDS API<a name="oracle-read-replicas.changing-replica-mode.api"></a>

To change a read\-only replica to mounted mode, set `ReplicaMode=mounted` in [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)\. To change a mounted replica to read\-only mode, set `ReplicaMode=read-only`\. 

```
https://rds.amazonaws.com/
	?Action=ModifyDBInstance
	&DBInstanceIdentifier=myreadreplica
	&SourceDBInstanceIdentifier=mysourcedb
	&Version=2012-01-15
	&SignatureVersion=2
	&SignatureMethod=HmacSHA256
	&Timestamp=2012-01-20T22%3A06%3A23.624Z
	&AWSAccessKeyId=<AWS Access Key ID>
	&Signature=<Signature>
	&ReplicaMode=mode
```

## Troubleshooting Oracle Replicas<a name="oracle-read-replicas.troubleshooting"></a>

To monitor replication lag in Amazon CloudWatch, view the Amazon RDS `ReplicaLag` metric\. For information about replication lag time, see [Monitoring Read Replication](USER_ReadRepl.md#USER_ReadRepl.Monitoring)\.

If replication lag is too long, you can query the following views for information about the lag:
+ `V$ARCHIVED_LOG` – Shows which commits have been applied to the read replica\.
+ `V$DATAGUARD_STATS` – Shows a detailed breakdown of the components that make up the `replicaLag` metric\.
+ `V$DATAGUARD_STATUS` – Shows the log output from Oracle's internal replication processes\.