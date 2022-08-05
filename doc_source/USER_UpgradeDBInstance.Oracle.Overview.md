# Overview of RDS for Oracle engine upgrades<a name="USER_UpgradeDBInstance.Oracle.Overview"></a>

Before upgrading your RDS for Oracle DB instance, familiarize yourself with the following concepts\. 

**Topics**
+ [Major and minor version upgrades](#USER_UpgradeDBInstance.Oracle.Overview.versions)
+ [Expected support dates for RDS for Oracle major releases](#Aurora.VersionPolicy.MajorVersionLifetime)
+ [Oracle engine version management](#Oracle.Concepts.Patching)
+ [Automatic snapshots during engine upgrades](#USER_UpgradeDBInstance.Oracle.Overview.snapshots)
+ [Oracle upgrades in a Multi\-AZ deployment](#USER_UpgradeDBInstance.Oracle.Overview.multi-az)
+ [Oracle upgrades of read replicas](#USER_UpgradeDBInstance.Oracle.Overview.read-replicas)
+ [Oracle upgrades of micro DB instances](#USER_UpgradeDBInstance.Oracle.Overview.micro-db)

## Major and minor version upgrades<a name="USER_UpgradeDBInstance.Oracle.Overview.versions"></a>

 Amazon RDS supports the following upgrades to an Oracle DB instance: 
+ Major version upgrades

  In general, a *major version upgrade* for a database engine can introduce changes that aren't compatible with existing applications\. To upgrade your DB instance to a major version, you must perform the action manually\.
+ Minor version upgrades

  A *minor version upgrade* includes only changes that are backward\-compatible with existing applications\. If you enable auto minor version upgrades on your DB instance, minor version upgrades occur automatically\. In all other cases, you upgrade the DB instance manually\.

When you upgrade the DB engine, an outage occurs\. The duration of the outage depends on your engine version and instance size\. 

## Expected support dates for RDS for Oracle major releases<a name="Aurora.VersionPolicy.MajorVersionLifetime"></a>

RDS for Oracle major versions remain available at least until the end of support date for the corresponding Oracle Database release version\. You can use the following dates to plan your testing and upgrade cycles\. These dates represent the earliest date that an upgrade to a newer version might be required\. If Amazon extends support for an RDS for Oracle version for longer than originally stated, we plan to update this table to reflect the later date\. 


| Oracle Database major release version  | Expected date for upgrading to a newer version | 
| --- | --- | 
|  Oracle Database 19c  |  April 30, 2024 without Extended Support or an Unlimited License Agreement April 30, 2027 with Extended Support or an Unlimited License Agreement  | 
|  Oracle Database 21c  | April 30, 2024 | 

Before we ask that you upgrade to a newer major version and to help you plan, we provide a reminder at least 12 months in advance\. We do so to communicate the detailed upgrade process\. Details include the timing of certain milestones, the impact on your DB instances, and the actions that we recommend that you take\. We recommend that you thoroughly test your applications with new RDS for Oracle versions before performing a major version upgrade\.

After this advance notification period, an automatic upgrade to the subsequent major version might be applied to any RDS for Oracle DB instance still running the older version\. If so, the upgrade is started during scheduled maintenance windows\. 

## Oracle engine version management<a name="Oracle.Concepts.Patching"></a>

With DB engine version management, you control when and how the database engine is patched and upgraded\. You get the flexibility to maintain compatibility with database engine patch versions\. You can also test new patch versions of RDS for Oracle to ensure they work with your application before deploying them in production\. In addition, you upgrade the versions on your own terms and timelines\.

**Note**  
Amazon RDS periodically aggregates official Oracle database patches using an Amazon RDS\-specific DB engine version\. To see a list of which Oracle patches are contained in an Amazon RDS Oracle\-specific engine version, go to [https://docs.aws.amazon.com/AmazonRDS/latest/OracleReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/OracleReleaseNotes/Welcome.html)\.

## Automatic snapshots during engine upgrades<a name="USER_UpgradeDBInstance.Oracle.Overview.snapshots"></a>

During upgrades of an Oracle DB instance, snapshots offer protection against upgrade issues\. If the backup retention period for your DB instance is greater than 0, Amazon RDS takes the following DB snapshots during the upgrade:

1. A snapshot of the DB instance before any upgrade changes have been made\. If the upgrade fails, you can restore this snapshot to create a DB instance running the old version\.

1. A snapshot of the DB instance after the upgrade completes\.

**Note**  
To change your backup retention period, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

After an upgrade, you can't revert to the previous engine version\. However, you can create a new Oracle DB instance by restoring the pre\-upgrade snapshot\.

## Oracle upgrades in a Multi\-AZ deployment<a name="USER_UpgradeDBInstance.Oracle.Overview.multi-az"></a>

If your DB instance is in a Multi\-AZ deployment, Amazon RDS upgrades both the primary and standby replicas\. If no operating system updates are required, the primary and standby upgrades occur simultaneously\. The instances are not available until the upgrade completes\.

If operating system updates are required in a Multi\-AZ deployment, Amazon RDS applies the updates when you request the DB upgrade\. Amazon RDS performs the following steps:

1. Updates the operating system on the standby DB instance\.

1. Upgrades the standby DB instance\.

1. Fails over the primary instance to the standby DB instance\.

1. Upgrades the operating system on the new standby DB instance, which was formerly the primary instance\.

1. Upgrades the new standby DB instance\.

## Oracle upgrades of read replicas<a name="USER_UpgradeDBInstance.Oracle.Overview.read-replicas"></a>

The Oracle DB engine version of the source DB instance and all of its read replicas must be the same\. Amazon RDS performs the upgrade in the following stages:

1. Upgrades the source DB instance\. The read replicas are available during this stage\.

1. Upgrades the read replicas in parallel, regardless of the replica maintenance windows\. The source DB is available during this stage\.

For major version upgrades of cross\-Region read replicas, Amazon RDS performs additional actions:
+ Generates an option group for the target version automatically
+ Copies all options and option settings from the original option group to the new option group
+ Associates the upgraded cross\-Region read replica with the new option group

## Oracle upgrades of micro DB instances<a name="USER_UpgradeDBInstance.Oracle.Overview.micro-db"></a>

We don't recommend upgrading databases running on micro DB instances\. Because these instances have limited CPU, the upgrade can take hours to complete\.

You can upgrade micro DB instances with small amounts of storage \(10–20 GiB\) by copying your data using Data Pump\. Before you migrate your production DB instances, we recommend that you test by copying data using Data Pump\.