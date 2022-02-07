# Replica requirements for Oracle<a name="oracle-read-replicas.limitations"></a>

Before creating an Oracle replica, check the following requirements\.

**Topics**
+ [Version and licensing requirements for Oracle replicas](#oracle-read-replicas.limitations.versions-and-licenses)
+ [Option requirements and limitations for Oracle replicas](#oracle-read-replicas.limitations.options)
+ [Miscellaneous requirements and restrictions for Oracle replicas](#oracle-read-replicas.limitations.miscellaneous)

## Version and licensing requirements for Oracle replicas<a name="oracle-read-replicas.limitations.versions-and-licenses"></a>

Before creating an Oracle replica, check the following version and licensing requirements:
+ If the replica is in read\-only mode, make sure that you have an Active Data Guard license\. If you place the replica in mounted mode, you don't need an Active Data Guard license\. Only the Oracle DB engine supports mounted replicas\.
+ Oracle replicas are only available on the Oracle Enterprise Edition \(EE\) engine\.
+ Oracle replicas are available for Oracle version 12\.1\.0\.2\.v10 and higher versions of Oracle Database 12c Release 1 \(12\.1\), for all Oracle Database 12c Release 2 \(12\.2\) versions, and for all Oracle Database 19c versions\.
+ Oracle replicas are available for DB instances only on the EC2\-VPC platform\.
+ Oracle replicas are available for DB instances running only on DB instance classes with two or more vCPUs\. A source DB instance can't use the db\.t3\.micro or db\.t3\.small instance classes\.
+ The Oracle DB engine version of the source DB instance and all of its replicas must be the same\. Amazon RDS upgrades the replicas immediately after upgrading the source DB instance, regardless of a replica's maintenance window\. For major version upgrades of cross\-Region replicas, Amazon RDS automatically does the following:
  + Generates an option group for the target version\.
  + Copies all options and option settings from the original option group to the new option group\.
  + Associates the upgraded cross\-Region replica with the new option group\.

  For more information about upgrading the DB engine version, see [Upgrading the Oracle DB engine](USER_UpgradeDBInstance.Oracle.md)\.

## Option requirements and limitations for Oracle replicas<a name="oracle-read-replicas.limitations.options"></a>

Before creating a replica for Oracle, check the requirements and restrictions for option groups:
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

## Miscellaneous requirements and restrictions for Oracle replicas<a name="oracle-read-replicas.limitations.miscellaneous"></a>

Before creating an Oracle replica, check the following miscellaneous requirements and restrictions:
+ You can't create manual snapshots of Amazon RDS for Oracle read replicas or enable automatic backups for them\. 
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