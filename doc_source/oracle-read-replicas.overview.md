# Overview of RDS for Oracle replicas<a name="oracle-read-replicas.overview"></a>

An *Oracle replica* database is a physical copy of your primary database\. An Oracle replica in read\-only mode is called a *read replica*\. An Oracle replica in mounted mode is called a *mounted replica*\. Oracle Database doesn't permit writes in a replica, but you can promote a replica to make it writable\. The promoted read replica has the replicated data to the point when the request was made to promote it\.

The following video provides a helpful overview of RDS for Oracle disaster recovery\. 

[![AWS Videos](http://img.youtube.com/vi/-XpzhIevwVg/0.jpg)](http://www.youtube.com/watch?v=-XpzhIevwVg)

For more information, see the blog post [Managed disaster recovery with Amazon RDS for Oracle cross\-Region automated backups \- Part 1](http://aws.amazon.com/blogs/database/managed-disaster-recovery-with-amazon-rds-for-oracle-cross-region-automated-backups-part-1/) and [Managed disaster recovery with Amazon RDS for Oracle cross\-Region automated backups \- Part 2](http://aws.amazon.com/blogs/database/part-2-managed-disaster-recovery-with-amazon-rds-for-oracle-xrab/)\.

**Topics**
+ [Read\-only and mounted replicas](#oracle-read-replicas.overview.modes)
+ [Archived redo log retention](#oracle-read-replicas.overview.log-retention)
+ [Outages during replication](#oracle-read-replicas.overview.outages)

## Read\-only and mounted replicas<a name="oracle-read-replicas.overview.modes"></a>

When creating or modifying an Oracle replica, you can place it in either of the following modes:

Read\-only  
This is the default\. Active Data Guard transmits and applies changes from the source database to all read replica databases\.  
You can create up to five read replicas from one source DB instance\. For general information about read replicas that applies to all DB engines, see [Working with read replicas](USER_ReadRepl.md)\. For information about Oracle Data Guard, see [Oracle Data Guard concepts and administration](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/oracle-data-guard-concepts.html#GUID-F78703FB-BD74-4F20-9971-8B37ACC40A65) in the Oracle documentation\.

Mounted  
In this case, replication uses Oracle Data Guard, but the replica database doesn't accept user connections\. The primary use for mounted replicas is cross\-Region disaster recovery\.  
A mounted replica can't serve a read\-only workload\. The mounted replica deletes archived redo log files after it applies them, regardless of the archived log retention policy\.

You can create a combination of mounted and read\-only DB replicas for the same source DB instance\. You can change a read\-only replica to mounted mode, or change a mounted replica to read\-only mode\. In either case, the Oracle database preserves the archived log retention setting\.

## Archived redo log retention<a name="oracle-read-replicas.overview.log-retention"></a>

If a primary DB instance has no cross\-Region read replicas, Amazon RDS for Oracle keeps a minimum of two hours of archived redo logs on the source DB instance\. This is true regardless of the setting for `archivelog retention hours` in `rdsadmin.rdsadmin_util.set_configuration`\. 

RDS purges logs from the source DB instance after two hours or after the archive log retention hours setting has passed, whichever is longer\. RDS purges logs from the read replica after the archive log retention hours setting has passed only if they have been successfully applied to the database\.

In some cases, a primary DB instance might have one or more cross\-Region read replicas\. If so, Amazon RDS for Oracle keeps the transaction logs on the source DB instance until they have been transmitted and applied to all cross\-Region read replicas\. For information about `rdsadmin.rdsadmin_util.set_configuration`, see [Retaining archived redo logs](Appendix.Oracle.CommonDBATasks.Log.md#Appendix.Oracle.CommonDBATasks.RetainRedoLogs)\.

## Outages during replication<a name="oracle-read-replicas.overview.outages"></a>

When you create an Oracle replica, no outage occurs for the source DB instance\. Amazon RDS takes a snapshot of the source DB instance\. This snapshot becomes the replica\. Amazon RDS sets the necessary parameters and permissions for the source DB and replica without service interruption\. Similarly, if you delete a replica, no outage occurs\.