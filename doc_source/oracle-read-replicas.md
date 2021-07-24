# Working with Oracle replicas for Amazon RDS<a name="oracle-read-replicas"></a>

To configure replication between Oracle DB instances, you can create replica databases\.

**Topics**
+ [Overview of Oracle replicas](#oracle-read-replicas.overview)
+ [Replica requirements for Oracle](#oracle-read-replicas.limitations)
+ [Preparing to create an Oracle replica](#oracle-read-replicas.Configuration)
+ [Creating an Oracle replica in mounted mode](#oracle-read-replicas.creating-in-mounted-mode)
+ [Modifying the Oracle replica mode](#oracle-read-replicas.changing-replica-mode)
+ [Troubleshooting Oracle replicas](#oracle-read-replicas.troubleshooting)

## Overview of Oracle replicas<a name="oracle-read-replicas.overview"></a>

An Oracle *replica* database is either mounted or read\-only\. An Oracle replica in read\-only mode is called a *read replica*\. An Oracle replica in mounted mode is called a *mounted replica*\.

The following video provides a helpful overview of Oracle disaster recovery\. For more information, see the blog post [Managed disaster recovery with Amazon RDS for Oracle cross\-Region automated backups](http://aws.amazon.com/blogs/database/part-2-managed-disaster-recovery-with-amazon-rds-for-oracle-xrab/) and [Managed disaster recovery with Amazon RDS for Oracle cross\-Region automated backups](http://aws.amazon.com/blogs/database/part-2-managed-disaster-recovery-with-amazon-rds-for-oracle-xrab/)\. 

[![AWS Videos](http://img.youtube.com/vi/-XpzhIevwVg/0.jpg)](http://www.youtube.com/watch?v=-XpzhIevwVg)

**Topics**
+ [Read\-only and mounted replicas](#oracle-read-replicas.overview.modes)
+ [Outages during replication](#oracle-read-replicas.overview.outages)

### Read\-only and mounted replicas<a name="oracle-read-replicas.overview.modes"></a>

When creating or modifying an Oracle replica, you can place it in either of the following modes:
+ Read\-only\. This is the default\. Active Data Guard transmits and applies changes from the source database to all read replica databases\.

  You can create up to five read replicas from one source DB instance\. For general information about read replicas that applies to all DB engines, see [Working with read replicas](USER_ReadRepl.md)\. For information about Oracle Data Guard, see [Oracle data guard concepts and administration](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/oracle-data-guard-concepts.html#GUID-F78703FB-BD74-4F20-9971-8B37ACC40A65) in the Oracle documentation\.
+ Mounted\. In this case, replication uses Oracle Data Guard, but the replica database doesn't accept user connections\. The primary use for mounted replicas is cross\-Region disaster recovery\.

  A mounted replica can't serve a read\-only workload\. The mounted replica deletes archived redo log files after it applies them, regardless of the archived log retention policy\.

You can create a combination of mounted and read\-only DB replicas for the same source DB instance\. You can change a read\-only replica to mounted mode, or change a mounted replica to read\-only mode\. In either case, the Oracle database preserves the archived log retention setting\.

### Outages during replication<a name="oracle-read-replicas.overview.outages"></a>

When you create an Oracle replica, no outage occurs for the source DB instance\. Amazon RDS takes a snapshot of the source DB instance\. This snapshot becomes the replica\. Amazon RDS sets the necessary parameters and permissions for the source DB and replica without service interruption\. Similarly, if you delete a replica, no outage occurs\.

## Replica requirements for Oracle<a name="oracle-read-replicas.limitations"></a>

Before creating an Oracle replica, check the following requirements\.

**Topics**
+ [Version and licensing requirements for Oracle replicas](#oracle-read-replicas.limitations.versions-and-licenses)
+ [Option requirements for Oracle replicas](#oracle-read-replicas.limitations.options)
+ [Miscellaneous requirements for Oracle replicas](#oracle-read-replicas.limitations.miscellaneous)

### Version and licensing requirements for Oracle replicas<a name="oracle-read-replicas.limitations.versions-and-licenses"></a>

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

  For more information about upgrading the DB engine version, see [Upgrading the Oracle DB engine](USER_UpgradeDBInstance.Oracle.md)\.

### Option requirements for Oracle replicas<a name="oracle-read-replicas.limitations.options"></a>

Before creating a replica for Oracle, check the requirements for option groups:
+ If your Oracle replica is in the same AWS Region as its source DB instance, make sure that it belongs to the same option group as the source DB instance\. Modifications to the source option group or source option group membership propagate to replicas\. These changes are applied to the replicas immediately after they are applied to the source DB instance, regardless of the replica's maintenance window\.

  For more information about option groups, see [Working with option groups](USER_WorkingWithOptionGroups.md)\.
+ When you create an Oracle cross\-Region replica, Amazon RDS creates a dedicated option group for it\.

  You can't remove an Oracle cross\-Region replica from its dedicated option group\. No other DB instances can use the dedicated option group for an Oracle cross\-Region replica\.

  You can only add or remove the following nonreplicated options from a dedicated option group:
  + `NATIVE_NETWORK_ENCRYPTION`
  + `OEM`
  + `OEM_AGENT`
  + `SSL`

  To add other options to an Oracle cross\-Region replica, add them to the source DB instance's option group\. The option is also installed on all of the source DB instance's replicas\. For licensed options, make sure that there are sufficient licenses for the replicas\.

  When you promote an Oracle cross\-Region replica, the promoted replica behaves the same as other Oracle DB instances, including the management of its options\. You can promote a replica explicitly or implicitly by deleting its source DB instance\.

  For more information about option groups, see [Working with option groups](USER_WorkingWithOptionGroups.md)\.

### Miscellaneous requirements for Oracle replicas<a name="oracle-read-replicas.limitations.miscellaneous"></a>

Before creating an Oracle replica, check the following miscellaneous requirements:
+ If a DB instance is a source for one or more cross\-Region replicas, the source DB retains its archived redo logs until they are applied on all cross\-Region replicas\. The archived redo logs might result in increased storage consumption\.
+ A logon trigger on a primary instance must permit access to the `RDS_DATAGUARD` user and to any user whose `AUTHENTICATED_IDENTITY` value is `RDS_DATAGUARD` or `rdsdb`\. Also, the trigger must not set the current schema for the `RDS_DATAGUARD` user\.
+ To avoid disrupting RDS automation, system triggers must permit specific users to log on to the primary and replica database\. [System triggers](https://docs.oracle.com/en/database/oracle/oracle-database/19/lnpls/plsql-triggers.html#GUID-FE23FCE8-DE36-41EF-80A9-6B4B49E80E5B) include DDL, logon, and database role triggers\. We recommend that you add code to your triggers to exclude the users listed in the following sample code:

  ```
  -- Determine who the user is
  SELECT SYS_CONTEXT('USERENV','AUTHENTICATED_IDENTITY') INTO CURRENT_USER FROM DUAL;
  -- The following users should always be able to login to either the Primary or Replica
  IF CURRENT_USER IN ('master_user', 'SYS', 'SYSTEM', 'RDS_DATAGUARD', 'rdsdb') THEN
  RETURN;
  END IF;
  ```
+ To avoid blocking connections from the Data Guard broker process, don't enable restricted sessions\. For more information about restricted sessions, see [Enabling and disabling restricted sessions](Appendix.Oracle.CommonDBATasks.System.md#Appendix.Oracle.CommonDBATasks.RestrictedSession)\.
+ Block change tracking is supported for read\-only replicas, but not for mounted replicas\. You can change a mounted replica to a read\-only replica, and then enable block change tracking\. For more information, see [Enabling and disabling block change tracking](Appendix.Oracle.CommonDBATasks.RMAN.md#Appendix.Oracle.CommonDBATasks.BlockChangeTracking)\.

## Preparing to create an Oracle replica<a name="oracle-read-replicas.Configuration"></a>

Before you can begin using your replica, perform the following tasks\.

**Topics**
+ [Enabling automatic backups](#oracle-read-replicas.configuration.autobackups)
+ [Enabling force logging mode](#oracle-read-replicas.configuration.force-logging)
+ [Changing your logging configuration](#oracle-read-replicas.configuration.logging-config)
+ [Setting the MAX\_STRING\_SIZE parameter](#oracle-read-replicas.configuration.string-size)
+ [Planning compute and storage resources](#oracle-read-replicas.configuration.planning-resources)

### Enabling automatic backups<a name="oracle-read-replicas.configuration.autobackups"></a>

Before a DB instance can serve as a source DB instance, make sure to enable automatic backups on the source DB instance\. To learn how to perform this procedure, see [Enabling automated backups](USER_WorkingWithAutomatedBackups.md#USER_WorkingWithAutomatedBackups.Enabling)\.

### Enabling force logging mode<a name="oracle-read-replicas.configuration.force-logging"></a>

We recommend that you enable force logging mode\. In force logging mode, the Oracle database writes redo records even when `NOLOGGING` is used with data definition language \(DDL\) statements\.

**To enable force logging mode**

1. Log in to your Oracle database using a client tool such as SQL Developer\.

1. Enable force logging mode by running the following procedure\. 

   ```
   exec rdsadmin.rdsadmin_util.force_logging(p_enable => true);
   ```

For more information about this procedure, see [Setting force logging](Appendix.Oracle.CommonDBATasks.Log.md#Appendix.Oracle.CommonDBATasks.SettingForceLogging)\.

### Changing your logging configuration<a name="oracle-read-replicas.configuration.logging-config"></a>

If you want to change your logging configuration, we recommend that you complete the changes before making a DB instance the source for replicas\. Also, we recommend that you not modify the logging configuration after you create the replicas\. Modifications can cause the online redo logging configuration to get out of sync with the standby logging configuration\.

Modify the logging configuration for a DB instance by using the Amazon RDS procedures `rdsadmin.rdsadmin_util.add_logfile` and `rdsadmin.rdsadmin_util.drop_logfile`\. For more information, see [Adding online redo logs](Appendix.Oracle.CommonDBATasks.Log.md#Appendix.Oracle.CommonDBATasks.RedoLogs) and [Dropping online redo logs](Appendix.Oracle.CommonDBATasks.Log.md#Appendix.Oracle.CommonDBATasks.DroppingRedoLogs)\.

### Setting the MAX\_STRING\_SIZE parameter<a name="oracle-read-replicas.configuration.string-size"></a>

Before you create an Oracle replica, ensure that the setting of the `MAX_STRING_SIZE` parameter is the same on the source DB instance and the replica\. You can do this by associating them with the same parameter group\. If you have different parameter groups for the source and the replica, you can set `MAX_STRING_SIZE` to the same value\. For more information about setting this parameter, see [Enabling extended data types for a new DB instance](Appendix.Oracle.CommonDBATasks.Misc.md#Oracle.Concepts.ExtendedDataTypes.CreateDBInstance)\.

### Planning compute and storage resources<a name="oracle-read-replicas.configuration.planning-resources"></a>

Ensure that the source DB instance and its replicas are sized properly, in terms of compute and storage, to suit their operational load\. If a replica reaches compute, network, or storage resource capacity, the replica stops receiving or applying changes from its source\. Amazon RDS for Oracle doesn't intervene to mitigate high replica lag between a source DB instance and its replicas\. You can modify the storage and CPU resources of a replica independently from its source and other replicas\.

## Creating an Oracle replica in mounted mode<a name="oracle-read-replicas.creating-in-mounted-mode"></a>

By default, Oracle replicas are read\-only\. To create a replica in mounted mode, use the console, the AWS CLI, or the RDS API\.

### Console<a name="oracle-read-replicas.creating-in-mounted-mode.console"></a>

**To create a mounted replica from a source Oracle DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Oracle DB instance that you want to use as the source for a mounted replica\.

1. For **Actions**, choose **Create replica**\. 

1. For **Replica mode**, choose **Mounted**\.

1. Choose the settings that you want to use\. For **DB instance identifier**, enter a name for the read replica\. Adjust other settings as needed\.

1. For **Regions**, choose the Region where the mounted replica will be launched\. 

1. Choose your instance size and storage type\. We recommend that you use the same DB instance class and storage type as the source DB instance for the read replica\.

1. For **Multi\-AZ deployment**, choose **Create a standby instance** to create a standby of your replica in another Availability Zone for failover support for the mounted replica\. Creating your mounted replica as a Multi\-AZ DB instance is independent of whether the source database is a Multi\-AZ DB instance\.

1. Choose the other settings that you want to use\.

1. Choose **Create replica**\.

In the **Databases** page, the mounted replica has the role Replica\.

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

To change a read\-only replica to a mounted state, set `--replica-mode` to `mounted` in the AWS CLI command [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)\. To place a mounted replica in read\-only mode, set `--replica-mode` to `open-read-only`\. 

### RDS API<a name="oracle-read-replicas.creating-in-mounted-mode.api"></a>

To create an Oracle replica in mounted mode, specify `ReplicaMode=mounted` in the RDS API operation [CreateDBInstanceReadReplica](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)\.

## Modifying the Oracle replica mode<a name="oracle-read-replicas.changing-replica-mode"></a>

To change the replica mode of an existing replica, use the console, AWS CLI, or RDS API\. When you change to mounted mode, the replica disconnects all active connections\. When you change to read\-only mode, Amazon RDS initializes Active Data Guard\.

The change operation can take a few minutes\. During the operation, the DB instance status changes to **modifying**\. For more information about status changes, see [Viewing DB instance status](accessing-monitoring.md#Overview.DBInstance.Status)\.

### Console<a name="oracle-read-replicas.changing-replica-mode.console"></a>

**To change the replica mode of an Oracle replica from mounted to read\-only**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the mounted replica database\.

1. Choose **Modify**\.

1. For **Replica mode**, choose **Read\-only**\.

1. Choose the other settings that you want to change\.

1. Choose **Continue**\.

1. For **Scheduling of modifications**, choose **Apply immediately**\.

1. Choose **Modify DB instance**\.

### AWS CLI<a name="oracle-read-replicas.changing-replica-mode.cli"></a>

To change a read replica to mounted mode, set `--replica-mode` to `mounted` in the AWS CLI command [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)\. To change a mounted replica to read\-only mode, set `--replica-mode` to `open-read-only`\.

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

## Troubleshooting Oracle replicas<a name="oracle-read-replicas.troubleshooting"></a>

This section describes possible replication problems and solutions\.

### Replication lag<a name="oracle-read-replicas.troubleshooting.lag"></a>

To monitor replication lag in Amazon CloudWatch, view the Amazon RDS `ReplicaLag` metric\. For information about replication lag time, see [Monitoring read replication](USER_ReadRepl.md#USER_ReadRepl.Monitoring)\.

If replication lag is too long, query the following views:
+ `V$ARCHIVED_LOG` – Shows which commits have been applied to the read replica\.
+ `V$DATAGUARD_STATS` – Shows a detailed breakdown of the components that make up the `replicaLag` metric\.
+ `V$DATAGUARD_STATUS` – Shows the log output from Oracle's internal replication processes\.

### Replication failure after adding or modifying triggers<a name="oracle-read-replicas.troubleshooting.triggers"></a>

If you add or modify any triggers, and if replication fails afterward, the problem may be the triggers\. Ensure that the trigger excludes the following user accounts, which are required by RDS for replication:
+ User accounts with administrator privileges
+ `SYS`
+ `SYSTEM`
+ `RDS_DATAGUARD`
+ `rdsdb`

For more information, see[Miscellaneous requirements for Oracle replicas](#oracle-read-replicas.limitations.miscellaneous)\.