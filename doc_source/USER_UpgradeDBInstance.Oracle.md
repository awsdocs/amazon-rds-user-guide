# Upgrading the Oracle DB Engine<a name="USER_UpgradeDBInstance.Oracle"></a>

When Amazon RDS supports a new version of Oracle, you can upgrade your DB instances to the new version\. Amazon RDS supports the following upgrades to an Oracle DB instance: 
+ Major Version Upgrades 

  In general, a major engine version upgrade can introduce changes that are not compatible with existing applications\. You must modify the DB instance manually to perform a major version upgrade\.
+ Minor Version Upgrades 

  A minor version upgrade includes only changes that are backward\-compatible with existing applications\. If you enable auto minor version upgrades on your DB instance, minor version upgrades occur automatically\. In all other cases, you must modify the DB instance manually\.

When you update the DB engine, an outage occurs\. The time for the outage depends on your engine version and instance size\. 

For information about what Oracle versions are available on Amazon RDS, see [Oracle Database Engine Release Notes](Appendix.Oracle.PatchComposition.md)\. 

## Overview of Oracle DB Engine Upgrades<a name="USER_UpgradeDBInstance.Oracle.Overview"></a>

If the backup retention period for your DB instance is greater than 0, Amazon RDS takes the following DB snapshots during the upgrade:

1. A snapshot of the DB instance before any upgrade changes have been made\. If the upgrade doesn't work for your databases, you can restore this snapshot to create a DB instance running the old version\.

1. A snapshot of the DB instance after the upgrade completes\.

**Note**  
To change your backup retention period, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

After an upgrade completes, you can't revert to the previous version of the DB engine\. However, you create a new DB instance by restoring the DB snapshot taken before the upgrade\.

### Oracle Upgrades in a Multi\-AZ Deployment<a name="USER_UpgradeDBInstance.Oracle.Overview.multi-az"></a>

If your DB instance is in a Multi\-AZ deployment, Amazon RDS upgrades both the primary and standby replicas\. If no operating system updates are required, the primary and standby upgrades occur simultaneously\. The instances are not available until the upgrade completes\.

If operating system updates are required in a Multi\-AZ deployment, Amazon RDS applies the updates when you request the DB upgrade\. Amazon RDS performs the following steps:

1. Updates the operating system on the standby DB instance

1. Upgrades the standby DB instance

1. Fails over the primary instance to the standby DB instance

1. Upgrades the operating system on the new standby DB instance, which was formerly the primary instance

1. Upgrades the new standby DB instance

### Oracle Upgrades of Read Replicas<a name="USER_UpgradeDBInstance.Oracle.Overview.read-replicas"></a>

The Oracle DB engine version of the source DB instance and all of its read replicas must be the same\. Amazon RDS performs the upgrade in the following stages:

1. Upgrades the source DB instance\. The read replicas are available during this stage\.

1. Upgrades the read replicas in parallel, regardless of the replica maintenance windows\. The source DB is available during this stage\.

For major version upgrades of cross\-Region read replicas, Amazon RDS performs additional actions:
+ Generates an option group for the target version automatically
+ Copies all options and option settings from the original option group to the new option group
+ Associates the upgraded cross\-Region read replica with the new option group

### Oracle Upgrades of Micro DB Instances<a name="USER_UpgradeDBInstance.Oracle.Overview.micro-db"></a>

We don't recommend upgrading databases running on micro DB instances\. Because these instances have limited CPU, the upgrade can take hours to complete\.

You can upgrade micro DB instances with small amounts of storage \(10–20 GiB\) by copying your data using Data Pump\. Before you migrate your production DB instances, we recommend that you test by copying data using Data Pump\.

## Major Version Upgrades<a name="USER_UpgradeDBInstance.Oracle.Major"></a>

Amazon RDS supports the following major version upgrades\.


****  

| Current Version | Upgrade Supported | 
| --- | --- | 
|  18\.0\.0\.0  |  19\.0\.0\.0  | 
|  12\.2\.0\.1  |  19\.0\.0\.0 18\.0\.0\.0  | 
|  12\.1\.0\.2  |  19\.0\.0\.0 18\.0\.0\.0 12\.2\.0\.1  | 
|  11\.2\.0\.4  |  19\.0\.0\.0 18\.0\.0\.0 12\.2\.0\.1 12\.1\.0\.2\.v5 and higher 12\.1 versions  | 

To perform a major version upgrade, modify the DB instance manually\. Major version upgrades don't occur automatically\.

In some cases, your current Oracle DB instance might be running on a DB instance class that isn't supported for the version to which you are upgrading\. In such a case, you must migrate the DB instance to a supported DB instance class before you upgrade\. For more information about the supported DB instance classes for each version and edition of Amazon RDS Oracle, see [DB Instance Classes](Concepts.DBInstanceClass.md)\.

Before you perform a major version upgrade, Oracle recommends that you gather optimizer statistics on the DB instance that you are upgrading\. Gathering optimizer statistics can reduce DB instance downtime during the upgrade\. To gather optimizer statistics, connect to the DB instance as the master user, and run the `DBMS_STATS.GATHER_DICTIONARY_STATS` procedure, as in the following example\.

```
EXEC DBMS_STATS.GATHER_DICTIONARY_STATS;
```

For more information, see [ Gathering Optimizer Statistics to Decrease Oracle Database Downtime](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/upgrd/database-preparation-tasks-to-complete-before-upgrades.html#GUID-6719608D-F145-403C-8CCE-CF23120BCC2A) in the Oracle documentation\.

**Note**  
Major version upgrades aren't supported for deprecated Oracle versions, such as Oracle version 11\.2\.0\.3 and 11\.2\.0\.2\.   
Major version downgrades aren't supported\.   
A major version upgrade from 11g to 12c must upgrade to an Oracle Patch Set Update \(PSU\) that was released in the same month or later\.   
For example, a major version upgrade from Oracle version 11\.2\.0\.4\.v14 to Oracle version 12\.1\.0\.2\.v11 is supported\. However, a major version upgrade from Oracle version 11\.2\.0\.4\.v14 to Oracle version 12\.1\.0\.2\.v9 isn't supported\. This is because Oracle version 11\.2\.0\.4\.v14 was released in October 2017, and Oracle version 12\.1\.0\.2\.v9 was released in July 2017\. For information about the release date for each Oracle PSU, see [Oracle Database Engine Release Notes](Appendix.Oracle.PatchComposition.md)\.

## Oracle Minor Version Upgrades<a name="USER_UpgradeDBInstance.Oracle.Minor"></a>

A minor version upgrade applies an Oracle Database Patch Set Update \(PSU\) or Release Update \(RU\) in a major version\. 

An Amazon RDS for Oracle DB instance is scheduled to be upgraded automatically during its next maintenance window when it meets the following conditions:
+ The DB instance has the **Auto minor version upgrade** option enabled\.
+ The DB instance is not running the latest minor DB engine version\.

The DB instance is upgraded to the latest quarterly PSU or RU four to six weeks after it is made available by Amazon RDS for Oracle\. For more information about PSUs and RUs, see [Oracle Database Engine Release Notes](Appendix.Oracle.PatchComposition.md)\. 

The following minor version upgrades aren't supported\. 


****  

| Current Version | Upgrade Not Supported | 
| --- | --- | 
| 12\.1\.0\.2\.v6 | 12\.1\.0\.2\.v7 | 
| 12\.1\.0\.2\.v5 | 12\.1\.0\.2\.v7 | 
| 12\.1\.0\.2\.v5 | 12\.1\.0\.2\.v6 | 

**Note**  
Minor version downgrades aren't supported\.

## Oracle SE2 Upgrade Paths<a name="USER_UpgradeDBInstance.Oracle.SE2"></a>

The following table shows supported upgrade paths to Standard Edition Two \(SE2\)\. For more information about the License Included and Bring Your Own License \(BYOL\) models, see [Oracle Licensing](CHAP_Oracle.md#Oracle.Concepts.Licensing)\. 


****  

| Your Existing Configuration | Supported SE2 Configuration | 
| --- | --- | 
|  12\.2\.0\.1 SE2, BYOL  |  12\.2\.0\.1 SE2, BYOL or License Included  | 
|  12\.1\.0\.2 SE2, BYOL  |  12\.2\.0\.1 SE2, BYOL or License Included 12\.1\.0\.2 SE2, BYOL or License Included  | 
|  11\.2\.0\.4 SE1, BYOL or License Included 11\.2\.0\.4 SE, BYOL  |  12\.2\.0\.1 SE2, BYOL or License Included 12\.1\.0\.2 SE2, BYOL or License Included  | 

To upgrade from your existing configuration to a supported SE2 configuration, use a supported upgrade path\. For more information, see [Major Version Upgrades](#USER_UpgradeDBInstance.Oracle.Major)\. 

## Option and Parameter Group Considerations<a name="USER_UpgradeDBInstance.Oracle.OGPG"></a>

### Option Group Considerations<a name="USER_UpgradeDBInstance.Oracle.OGPG.OG"></a>

If your DB instance uses a custom option group, in some cases Amazon RDS can't automatically assign your DB instance a new option group\. For example, this occurs when you upgrade to a new major version\. In those cases, you must specify a new option group when you upgrade\. We recommend that you create a new option group, and add the same options to it as in your existing custom option group\. 

For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create) or [Making a Copy of an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Copy)\. 

If your DB instance uses a custom option group that contains the APEX option, in some cases you can reduce the time it takes to upgrade your DB instance by upgrading your version of APEX at the same time as your DB instance\. For more information, see [Upgrading the APEX Version](Appendix.Oracle.Options.APEX.md#Appendix.Oracle.Options.APEX.Upgrade)\. 

### Parameter Group Considerations<a name="USER_UpgradeDBInstance.Oracle.OGPG.PG"></a>

If your DB instance uses a custom parameter group, in some cases Amazon RDS can't automatically assign your DB instance a new parameter group\. For example, this occurs when you upgrade to a new major version\. In those cases, you must specify a new parameter group when you upgrade\. We recommend that you create a new parameter group, and configure the parameters as in your existing custom parameter group\. 

For more information, see [Creating a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Creating) or [Copying a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Copying)\. 

## Testing an Upgrade<a name="USER_UpgradeDBInstance.Oracle.UpgradeTesting"></a>

Before you perform a major version upgrade on your DB instance, you should thoroughly test your database and all applications that access the database for compatibility with the new version\. We recommend that you use the following procedure\. 

**To test a major version upgrade**

1. Review the Oracle upgrade documentation for the new version of the database engine to see if there are compatibility issues that might affect your database or applications\. For more information, see [Database Upgrade Guide](https://docs.oracle.com/database/121/UPGRD/toc.htm) in the Oracle documentation\. 

1. If your DB instance uses a custom option group, create a new option group compatible with the new version you are upgrading to\. For more information, see [Option Group Considerations](#USER_UpgradeDBInstance.Oracle.OGPG.OG)\. 

1. If your DB instance uses a custom parameter group, create a new parameter group compatible with the new version you are upgrading to\. For more information, see [Parameter Group Considerations](#USER_UpgradeDBInstance.Oracle.OGPG.PG)\. 

1. Create a DB snapshot of the DB instance to be upgraded\. For more information, see [Creating a DB Snapshot](USER_CreateSnapshot.md)\. 

1. Restore the DB snapshot to create a new test DB instance\. For more information, see [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)\. 

1. Modify this new test DB instance to upgrade it to the new version, by using one of the following methods: 
   + [Console](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual.Console)
   + [AWS CLI](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual.CLI)
   + [RDS API](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual.API)

1. Perform testing: 
   + Run as many of your quality assurance tests against the upgraded DB instance as needed to ensure that your database and application work correctly with the new version\. 
   + Implement any new tests needed to evaluate the impact of any compatibility issues that you identified in step 1\. 
   + Test all stored procedures, functions, and triggers\. 
   + Direct test versions of your applications to the upgraded DB instance\. Verify that the applications work correctly with the new version\. 
   + Evaluate the storage used by the upgraded instance to determine if the upgrade requires additional storage\. You might need to choose a larger instance class to support the new version in production\. For more information, see [DB Instance Classes](Concepts.DBInstanceClass.md)\. 

1. If all tests pass, then perform the upgrade on your production DB instance\. We recommend that you don't allow write operations to the DB instance until you confirm that everything is working correctly\. 

## Upgrading an Oracle DB Instance<a name="USER_UpgradeDBInstance.Oracle.Upgrading"></a>

For information about manually or automatically upgrading an Oracle DB instance, see [Upgrading a DB Instance Engine Version](USER_UpgradeDBInstance.Upgrading.md)\.