# Upgrading the Oracle DB engine<a name="USER_UpgradeDBInstance.Oracle"></a>

When Amazon RDS supports a new version of Oracle, you can upgrade your DB instances to the new version\. For information about which Oracle versions are available on Amazon RDS, see [Oracle database engine release notes](Appendix.Oracle.PatchComposition.md)\.

**Important**  
RDS for Oracle 11g is deprecated\. You can't upgrade an Oracle 11g engine\. If you maintain Oracle 11g snapshots, you can upgrade them to a later release\.

**Topics**
+ [Overview of Oracle DB engine upgrades](#USER_UpgradeDBInstance.Oracle.Overview)
+ [Major version upgrades](#USER_UpgradeDBInstance.Oracle.Major)
+ [Oracle minor version upgrades](#USER_UpgradeDBInstance.Oracle.Minor)
+ [Oracle SE2 upgrade paths](#USER_UpgradeDBInstance.Oracle.SE2)
+ [Considerations for Oracle DB upgrades](#USER_UpgradeDBInstance.Oracle.OGPG)
+ [Preparing for the automatic upgrade of Oracle 18c](#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-18c)
+ [Testing an Oracle DB upgrade](#USER_UpgradeDBInstance.Oracle.UpgradeTesting)
+ [Upgrading an Oracle DB instance](#USER_UpgradeDBInstance.Oracle.Upgrading)

## Overview of Oracle DB engine upgrades<a name="USER_UpgradeDBInstance.Oracle.Overview"></a>

Before upgrading your Oracle DB instance, familiarize yourself with the following concepts\. 

**Topics**
+ [Major and minor version upgrades](#USER_UpgradeDBInstance.Oracle.Overview.versions)
+ [Oracle engine version management](#Oracle.Concepts.Patching)
+ [Automatic snapshots during engine upgrades](#USER_UpgradeDBInstance.Oracle.Overview.snapshots)
+ [Oracle upgrades in a Multi\-AZ deployment](#USER_UpgradeDBInstance.Oracle.Overview.multi-az)
+ [Oracle upgrades of read replicas](#USER_UpgradeDBInstance.Oracle.Overview.read-replicas)
+ [Oracle upgrades of micro DB instances](#USER_UpgradeDBInstance.Oracle.Overview.micro-db)

### Major and minor version upgrades<a name="USER_UpgradeDBInstance.Oracle.Overview.versions"></a>

 Amazon RDS supports the following upgrades to an Oracle DB instance: 
+ Major version upgrades

  In general, a *major version upgrade* for a database engine can introduce changes that aren't compatible with existing applications\. To upgrade your DB instance to a major version, you must perform the action manually\.
+ Minor version upgrades

  A *minor version upgrade* includes only changes that are backward\-compatible with existing applications\. If you enable auto minor version upgrades on your DB instance, minor version upgrades occur automatically\. In all other cases, you upgrade the DB instance manually\.

When you upgrade the DB engine, an outage occurs\. The duration of the outage depends on your engine version and instance size\. 

### Oracle engine version management<a name="Oracle.Concepts.Patching"></a>

With DB engine version management, you control when and how the database engine is patched and upgraded\. You get the flexibility to maintain compatibility with database engine patch versions\. You can also test new patch versions to ensure they work with your application before deploying them in production\. In addition, you upgrade the versions on your own terms and timelines\.

**Note**  
Amazon RDS periodically aggregates official Oracle database patches using an Amazon RDS\-specific DB engine version\. To see a list of which Oracle patches are contained in an Amazon RDS Oracle\-specific engine version, go to [Oracle database engine release notes](Appendix.Oracle.PatchComposition.md)\.

### Automatic snapshots during engine upgrades<a name="USER_UpgradeDBInstance.Oracle.Overview.snapshots"></a>

During upgrades of an Oracle DB instance, snapshots offer protection against upgrade issues\. If the backup retention period for your DB instance is greater than 0, Amazon RDS takes the following DB snapshots during the upgrade:

1. A snapshot of the DB instance before any upgrade changes have been made\. If the upgrade fails, you can restore this snapshot to create a DB instance running the old version\.

1. A snapshot of the DB instance after the upgrade completes\.

**Note**  
To change your backup retention period, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

After an upgrade, you can't revert to the previous engine version\. However, you can create a new Oracle DB instance by restoring the pre\-upgrade snapshot\.

### Oracle upgrades in a Multi\-AZ deployment<a name="USER_UpgradeDBInstance.Oracle.Overview.multi-az"></a>

If your DB instance is in a Multi\-AZ deployment, Amazon RDS upgrades both the primary and standby replicas\. If no operating system updates are required, the primary and standby upgrades occur simultaneously\. The instances are not available until the upgrade completes\.

If operating system updates are required in a Multi\-AZ deployment, Amazon RDS applies the updates when you request the DB upgrade\. Amazon RDS performs the following steps:

1. Updates the operating system on the standby DB instance\.

1. Upgrades the standby DB instance\.

1. Fails over the primary instance to the standby DB instance\.

1. Upgrades the operating system on the new standby DB instance, which was formerly the primary instance\.

1. Upgrades the new standby DB instance\.

### Oracle upgrades of read replicas<a name="USER_UpgradeDBInstance.Oracle.Overview.read-replicas"></a>

The Oracle DB engine version of the source DB instance and all of its read replicas must be the same\. Amazon RDS performs the upgrade in the following stages:

1. Upgrades the source DB instance\. The read replicas are available during this stage\.

1. Upgrades the read replicas in parallel, regardless of the replica maintenance windows\. The source DB is available during this stage\.

For major version upgrades of cross\-Region read replicas, Amazon RDS performs additional actions:
+ Generates an option group for the target version automatically
+ Copies all options and option settings from the original option group to the new option group
+ Associates the upgraded cross\-Region read replica with the new option group

### Oracle upgrades of micro DB instances<a name="USER_UpgradeDBInstance.Oracle.Overview.micro-db"></a>

We don't recommend upgrading databases running on micro DB instances\. Because these instances have limited CPU, the upgrade can take hours to complete\.

You can upgrade micro DB instances with small amounts of storage \(10–20 GiB\) by copying your data using Data Pump\. Before you migrate your production DB instances, we recommend that you test by copying data using Data Pump\.

## Major version upgrades<a name="USER_UpgradeDBInstance.Oracle.Major"></a>

Amazon RDS supports the following major version upgrades\.

To perform a major version upgrade, modify the DB instance manually\. Major version upgrades don't occur automatically\. 

### Supported versions for major upgrades<a name="USER_UpgradeDBInstance.Oracle.Major.supported-versions"></a>

Amazon RDS supports the following major version upgrades\.


****  

| Current version | Upgrade supported | 
| --- | --- | 
|  18\.0\.0\.0  |  19\.0\.0\.0  | 
|  12\.2\.0\.1  |  19\.0\.0\.0 18\.0\.0\.0  | 
|  12\.1\.0\.2  |  19\.0\.0\.0 18\.0\.0\.0 12\.2\.0\.1  | 

A major version upgrade of Oracle Database must upgrade to a Release Update \(RU\) that was released in the same month or later\. Major version downgrades aren't supported for any Oracle versions\.

### Supported instance classes for major upgrades<a name="USER_UpgradeDBInstance.Oracle.Major.instance-classes"></a>

Your current Oracle DB instance might run on a DB instance class that isn't supported for the version to which you are upgrading\. In this case, before you upgrade, migrate the DB instance to a supported DB instance class\. For more information about the supported DB instance classes for each version and edition of Amazon RDS for Oracle, see [DB instance classes](Concepts.DBInstanceClass.md)\.

### Gathering statistics before major upgrades<a name="USER_UpgradeDBInstance.Oracle.Major.gathering-stats"></a>

Before you perform a major version upgrade, Oracle recommends that you gather optimizer statistics on the DB instance that you are upgrading\. This action can reduce DB instance downtime during the upgrade\.

To gather optimizer statistics, connect to the DB instance as the master user, and run the `DBMS_STATS.GATHER_DICTIONARY_STATS` procedure, as in the following example\.

```
EXEC DBMS_STATS.GATHER_DICTIONARY_STATS;
```

For more information, see [ Gathering optimizer statistics to decrease Oracle database downtime](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/upgrd/database-preparation-tasks-to-complete-before-upgrades.html#GUID-6719608D-F145-403C-8CCE-CF23120BCC2A) in the Oracle documentation\.

### Allowing major upgrades<a name="USER_UpgradeDBInstance.Oracle.Major.allowing-upgrades"></a>

A major engine version upgrade might be incompatible with your application\. The upgrade is irreversible\. If you specify a major version for the EngineVersion parameter that is different from the current major version, you must allow major version upgrades\.

If you upgrade a major version using the CLI command [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html), specify `--allow-major-version-upgrade`\. This setting isn't persistent, so you must specify `--allow-major-version-upgrade` whenever you perform a major upgrade\. This parameter has no impact on upgrades of minor engine versions\. For more information, see [Upgrading a DB instance engine version](USER_UpgradeDBInstance.Upgrading.md)\.

If you upgrade a major version using the console, you don't need to choose an option to allow the upgrade\. Instead, the console displays a warning that major upgrades are irreversible\.

## Oracle minor version upgrades<a name="USER_UpgradeDBInstance.Oracle.Minor"></a>

A minor version upgrade applies an Oracle Database Patch Set Update \(PSU\) or Release Update \(RU\) in a major version\. 

An Amazon RDS for Oracle DB instance is scheduled to be upgraded automatically during its next maintenance window when it meets the following conditions:
+ The DB instance has the **Auto minor version upgrade** option enabled\.
+ The DB instance is not running the latest minor DB engine version\.

The DB instance is upgraded to the latest quarterly PSU or RU four to six weeks after it is made available by Amazon RDS for Oracle\. For more information about PSUs and RUs, see [Oracle database engine release notes](Appendix.Oracle.PatchComposition.md)\. 

The following minor version upgrades aren't supported\. 


****  

| Current version | Upgrade not supported | 
| --- | --- | 
| 12\.1\.0\.2\.v6 | 12\.1\.0\.2\.v7 | 
| 12\.1\.0\.2\.v5 | 12\.1\.0\.2\.v7 | 
| 12\.1\.0\.2\.v5 | 12\.1\.0\.2\.v6 | 

**Note**  
Minor version downgrades aren't supported\.

## Oracle SE2 upgrade paths<a name="USER_UpgradeDBInstance.Oracle.SE2"></a>

The following table shows supported upgrade paths to Standard Edition Two \(SE2\)\. For more information about the License Included and Bring Your Own License \(BYOL\) models, see [Oracle licensing options](CHAP_Oracle.md#Oracle.Concepts.Licensing)\. 


****  

| Your existing configuration | Supported SE2 configuration | 
| --- | --- | 
|  12\.2\.0\.1 SE2, BYOL  |  12\.2\.0\.1 SE2, BYOL or License Included  | 
|  12\.1\.0\.2 SE2, BYOL  |  12\.2\.0\.1 SE2, BYOL or License Included 12\.1\.0\.2 SE2, BYOL or License Included  | 

To upgrade from your existing configuration to a supported SE2 configuration, use a supported upgrade path\. For more information, see [Major version upgrades](#USER_UpgradeDBInstance.Oracle.Major)\. 

## Considerations for Oracle DB upgrades<a name="USER_UpgradeDBInstance.Oracle.OGPG"></a>

Before upgrading, review the implications for option groups, parameter groups, and time zones\.

### Option group considerations<a name="USER_UpgradeDBInstance.Oracle.OGPG.OG"></a>

If your DB instance uses a custom option group, sometimes Amazon RDS can't automatically assign a new option group\. For example, this situation occurs when you upgrade to a new major version\. In such cases, specify a new option group when you upgrade\. We recommend that you create a new option group, and add the same options to it as in your existing custom option group\. 

For more information, see [Creating an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create) or [Copying an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Copy)\. 

If your DB instance uses a custom option group that contains the APEX option, you can sometimes reduce the upgrade time\. To do this, upgrade your version of APEX at the same time as your DB instance\. For more information, see [Upgrading the APEX version](Appendix.Oracle.Options.APEX.md#Appendix.Oracle.Options.APEX.Upgrade)\. 

### Parameter group considerations<a name="USER_UpgradeDBInstance.Oracle.OGPG.PG"></a>

If your DB instance uses a custom parameter group, sometimes Amazon RDS can't automatically assign your DB instance a new parameter group\. For example, this situation occurs when you upgrade to a new major version\. In such cases, make sure to specify a new parameter group when you upgrade\. We recommend that you create a new parameter group, and configure the parameters as in your existing custom parameter group\.

For more information, see [Creating a DB parameter group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Creating) or [Copying a DB parameter group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Copying)\. 

### Time zone considerations<a name="USER_UpgradeDBInstance.Oracle.OGPG.DST"></a>

You can use the time zone option to change the *system time zone* used by your Oracle DB instance\. For example, you might change the time zone of a DB instance to be compatible with an on\-premises environment, or a legacy application\. The time zone option changes the time zone at the host level\. Amazon RDS for Oracle updates the system time zone automatically throughout the year\. For more information about the system time zone, see [Oracle time zone](Appendix.Oracle.Options.Timezone.md)\.

When you create an Oracle DB instance, the database automatically sets the *database time zone*\. The database time zone is also known as the Daylight Saving Time \(DST\) time zone\. The database time zone is distinct from the system time zone\.

Between Oracle Database releases, patch sets or individual patches may include new DST versions\. These patches reflect the changes in transition rules for various time zone regions\. For example, a government might change when DST takes effect\. Changes to DST rules may affect existing data of the `TIMESTAMP WITH TIME ZONE` data type\.

If you upgrade an RDS for Oracle instance, Amazon RDS does not upgrade the database time zone automatically\. To upgrade the database time zone manually, create a new Oracle DB instance that has the desired DST patch\. Then migrate the data from your current instance to the new instance\. You can migrate data using several techniques, including the following:
+ Oracle GoldenGate
+ AWS Database Migration Service
+ Oracle Data Pump
+ Original Export/Import \(desupported for general use\)

**Note**  
When you migrate data using Oracle Data Pump, the utility raises the error ORA\-39405 when the target time zone version is lower than the source time zone version\.

For more information, see [TIMESTAMP WITH TIMEZONE restrictions](https://docs.oracle.com/en/database/oracle/oracle-database/19/sutil/oracle-data-pump-overview.html#GUID-9B6C92EE-860E-43DD-9728-735B17B9DA89) in the Oracle documentation\. 

## Preparing for the automatic upgrade of Oracle 18c<a name="USER_UpgradeDBInstance.Oracle.auto-upgrade-of-18c"></a>

On July 1, 2021, Amazon RDS plans to begin automatically upgrading Oracle 18c instances to Oracle 19c\. The automatic upgrades are not guaranteed to occur in your maintenance window\. All Oracle 18c instances, including reserved instances, will move to the latest available Release Update \(RU\)\.

Before the automatic upgrades begin, we highly recommend that you upgrade your existing 18c DB instances to version 19c manually\. When you upgrade manually, you can validate that your applications work correctly\. To avoid the automatic upgrade, use one of the following strategies before July 1, 2021\.

**Topics**
+ [Upgrade your Oracle 18c DB instance](#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-18c.upgrade)
+ [Upgrade your Oracle 18c DB snapshots](#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-18c.snapshots)
+ [Downgrade your Oracle 18c DB instance](#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-18c.downgrade)

### Upgrade your Oracle 18c DB instance<a name="USER_UpgradeDBInstance.Oracle.auto-upgrade-of-18c.upgrade"></a>

You can upgrade your Oracle version 18c DB instance to Oracle version 19c\. Before upgrading, consider the following:
+ Your SQL statements might perform differently after the upgrade\. If so, you can use the `OPTIMIZER_FEATURES_ENABLE` parameter to retain the behavior of the 18c optimizer\. For more information, see [Influencing the Optimizer](https://docs.oracle.com/en/database/oracle/oracle-database/19/tgsql/influencing-the-optimizer.html#GUID-8758EF88-1CC6-41BD-8581-246702414D1D) in the Oracle documentation\.
+ If you have Extended Support for Oracle 18c on the BYOL model, consider the implications\. In this case, you must have Extended Support agreements from Oracle Support for Oracle 19c\. For details on licensing and support requirements for BYOL, see [Amazon RDS for Oracle FAQs](https://aws.amazon.com/rds/oracle/faqs/)\.

### Upgrade your Oracle 18c DB snapshots<a name="USER_UpgradeDBInstance.Oracle.auto-upgrade-of-18c.snapshots"></a>

You can upgrade your existing snapshots to Oracle 19c, and then restore them\. For more information, see [Upgrading an Oracle DB snapshot](USER_UpgradeDBSnapshot.Oracle.md)\.

If you plan to upgrade using snapshots, the planned deadline to avoid the automatic upgrade is June 30, 2021\.

### Downgrade your Oracle 18c DB instance<a name="USER_UpgradeDBInstance.Oracle.auto-upgrade-of-18c.downgrade"></a>

You may decide not to upgrade your DB instances to Oracle 19c\. In this case, you can downgrade your instance to Oracle Database version 12\.1\.0\.2 or 12\.2\.0\.1\. Use any of the following techniques:
+ Oracle Data Pump
+ AWS Database Migration Service \(DMS\)
+ Any supported logical replication tool

For more information about import options, see [Importing data into Oracle on Amazon RDS](Oracle.Procedural.Importing.md)\.

## Testing an Oracle DB upgrade<a name="USER_UpgradeDBInstance.Oracle.UpgradeTesting"></a>

Before you upgrade your DB instance to a major version, thoroughly test your database and all applications that access the database for compatibility with the new version\. We recommend that you use the following procedure\. 

**To test a major version upgrade**

1. Review the Oracle upgrade documentation for the new version of the database engine to see if there are compatibility issues that might affect your database or applications\. For more information, see [Database Upgrade Guide](https://docs.oracle.com/database/121/UPGRD/toc.htm) in the Oracle documentation\. 

1. If your DB instance uses a custom option group, create a new option group compatible with the new version you are upgrading to\. For more information, see [Option group considerations](#USER_UpgradeDBInstance.Oracle.OGPG.OG)\. 

1. If your DB instance uses a custom parameter group, create a new parameter group compatible with the new version you are upgrading to\. For more information, see [Parameter group considerations](#USER_UpgradeDBInstance.Oracle.OGPG.PG)\. 

1. Create a DB snapshot of the DB instance to be upgraded\. For more information, see [Creating a DB snapshot](USER_CreateSnapshot.md)\. 

1. Restore the DB snapshot to create a new test DB instance\. For more information, see [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)\. 

1. Modify this new test DB instance to upgrade it to the new version, by using one of the following methods: 
   + [Console](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual.Console)
   + [AWS CLI](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual.CLI)
   + [RDS API](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual.API)

1. Perform testing: 
   + Run as many of your quality assurance tests against the upgraded DB instance as needed to ensure that your database and application work correctly with the new version\. 
   + Implement any new tests needed to evaluate the impact of any compatibility issues that you identified in step 1\. 
   + Test all stored procedures, functions, and triggers\. 
   + Direct test versions of your applications to the upgraded DB instance\. Verify that the applications work correctly with the new version\. 
   + Evaluate the storage used by the upgraded instance to determine if the upgrade requires additional storage\. You might need to choose a larger instance class to support the new version in production\. For more information, see [DB instance classes](Concepts.DBInstanceClass.md)\. 

1. If all tests pass, upgrade your production DB instance\. We recommend that you confirm that the DB instance working correctly before allowing write operations to the DB instance\.

## Upgrading an Oracle DB instance<a name="USER_UpgradeDBInstance.Oracle.Upgrading"></a>

For information about manually or automatically upgrading an Oracle DB instance, see [Upgrading a DB instance engine version](USER_UpgradeDBInstance.Upgrading.md)\.