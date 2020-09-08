# Upgrading the Oracle DB Engine<a name="USER_UpgradeDBInstance.Oracle"></a>

When Amazon RDS supports a new version of Oracle, you can upgrade your DB instances to the new version\. Amazon RDS supports the following upgrades to an Oracle DB instance: 
+ Major version upgrades\. 

  In general, a *major version upgrade* for a database engine can introduce changes that aren't compatible with existing applications\. To perform a major version upgrade, modify the DB instance manually\.
**Important**  
On November 1, 2020, Amazon RDS automatically begins upgrading Oracle 11g SE1 License Included \(LI\) instances to Oracle 19c\. The automatic upgrade moves all 11g DB instances to the latest available Oracle Release Update\. For more information, see [Preparing for the Automatic Upgrade of Oracle 11g SE1](#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g)\.
+ Minor version upgrades\. 

  A *minor version upgrade* includes only changes that are backward\-compatible with existing applications\. If you enable auto minor version upgrades on your DB instance, minor version upgrades occur automatically\. In all other cases, you must modify the DB instance manually\.

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

1. Updates the operating system on the standby DB instance\.

1. Upgrades the standby DB instance\.

1. Fails over the primary instance to the standby DB instance\.

1. Upgrades the operating system on the new standby DB instance, which was formerly the primary instance\.

1. Upgrades the new standby DB instance\.

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

To perform a major version upgrade, modify the DB instance manually\. Major version upgrades don't occur automatically\. 

### Supported Versions for Major Upgrades<a name="USER_UpgradeDBInstance.Oracle.Major.supported-versions"></a>

Amazon RDS supports the following major version upgrades\.


****  

| Current Version | Upgrade Supported | 
| --- | --- | 
|  18\.0\.0\.0  |  19\.0\.0\.0  | 
|  12\.2\.0\.1  |  19\.0\.0\.0 18\.0\.0\.0  | 
|  12\.1\.0\.2  |  19\.0\.0\.0 18\.0\.0\.0 12\.2\.0\.1  | 
|  11\.2\.0\.4  |  19\.0\.0\.0 18\.0\.0\.0 12\.2\.0\.1 12\.1\.0\.2\.v5 and higher 12\.1 versions  | 

Major version upgrades aren't supported for deprecated Oracle versions, such as Oracle version 11\.2\.0\.3 and 11\.2\.0\.2\. Major version downgrades aren't supported for any Oracle versions\.

A major version upgrade from 11g to 12c must upgrade to an Oracle Patch Set Update \(PSU\) that was released in the same month or later\. For example, a major version upgrade from 11\.2\.0\.4\.v14 to 12\.1\.0\.2\.v11 is supported\. However, a major version upgrade from 11\.2\.0\.4\.v14 to 12\.1\.0\.2\.v9 isn't supported\. This is because Oracle version 11\.2\.0\.4\.v14 was released in October 2017, and Oracle version 12\.1\.0\.2\.v9 was released in July 2017\. For information about the release date for each Oracle PSU, see [Oracle Database Engine Release Notes](Appendix.Oracle.PatchComposition.md)\.

### Supported Instance Classes for Major Upgrades<a name="USER_UpgradeDBInstance.Oracle.Major.instance-classes"></a>

In some cases, your current Oracle DB instance might be running on a DB instance class that isn't supported for the version to which you are upgrading\. In such a case, make sure you migrate the DB instance to a supported DB instance class before you upgrade\. For more information about the supported DB instance classes for each version and edition of Amazon RDS Oracle, see [DB Instance Classes](Concepts.DBInstanceClass.md)\.

### Gathering Statistics Before Major Upgrades<a name="USER_UpgradeDBInstance.Oracle.Major.gathering-stats"></a>

Before you perform a major version upgrade, Oracle recommends that you gather optimizer statistics on the DB instance that you are upgrading\. Gathering optimizer statistics can reduce DB instance downtime during the upgrade\.

To gather optimizer statistics, connect to the DB instance as the master user, and run the `DBMS_STATS.GATHER_DICTIONARY_STATS` procedure, as in the following example\.

```
EXEC DBMS_STATS.GATHER_DICTIONARY_STATS;
```

For more information, see [ Gathering Optimizer Statistics to Decrease Oracle Database Downtime](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/upgrd/database-preparation-tasks-to-complete-before-upgrades.html#GUID-6719608D-F145-403C-8CCE-CF23120BCC2A) in the Oracle documentation\.

### Allowing Major Upgrades<a name="USER_UpgradeDBInstance.Oracle.Major.allowing-upgrades"></a>

A major engine version upgrade might be incompatible with your application\. The upgrade is irreversible\. If you specify a major version for the EngineVersion parameter that is different from the current major version, you must allow major version upgrades\.

If you upgrade a major version using the CLI command [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html), you must specify `--allow-major-version-upgrade`\. This permission is not a persistent setting, so you must specify `--allow-major-version-upgrade` whenever you perform a major upgrade\. This parameter has no impact on upgrades of minor engine versions\. For more information, see [Upgrading a DB Instance Engine Version](USER_UpgradeDBInstance.Upgrading.md)\.

If you upgrade a major version using the console, you don't need to choose an option to allow the upgrade\. Instead, the console displays a warning that major upgrades are irreversible\.

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

## Considerations for Oracle DB Upgrades<a name="USER_UpgradeDBInstance.Oracle.OGPG"></a>

Before upgrading, review the implications for option groups, parameter groups, and time zones\.

### Option Group Considerations<a name="USER_UpgradeDBInstance.Oracle.OGPG.OG"></a>

If your DB instance uses a custom option group, in some cases Amazon RDS can't automatically assign your DB instance a new option group\. For example, this occurs when you upgrade to a new major version\. In those cases, you must specify a new option group when you upgrade\. We recommend that you create a new option group, and add the same options to it as in your existing custom option group\. 

For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create) or [Making a Copy of an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Copy)\. 

If your DB instance uses a custom option group that contains the APEX option, in some cases you can reduce the time it takes to upgrade your DB instance\. To do this, upgrade your version of APEX at the same time as your DB instance\. For more information, see [Upgrading the APEX Version](Appendix.Oracle.Options.APEX.md#Appendix.Oracle.Options.APEX.Upgrade)\. 

### Parameter Group Considerations<a name="USER_UpgradeDBInstance.Oracle.OGPG.PG"></a>

If your DB instance uses a custom parameter group, sometimes Amazon RDS can't automatically assign your DB instance a new parameter group\. For example, this situation occurs when you upgrade to a new major version\. In these cases, make sure to specify a new parameter group when you upgrade\. We recommend that you create a new parameter group, and configure the parameters as in your existing custom parameter group\.

For more information, see [Creating a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Creating) or [Copying a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Copying)\. 

### Time Zone Considerations<a name="USER_UpgradeDBInstance.Oracle.OGPG.DST"></a>

You can use the time zone option to change the *system time zone* used by your Oracle DB instance\. For example, you might change the time zone of a DB instance to be compatible with an on\-premises environment, or a legacy application\. The time zone option changes the time zone at the host level\. Amazon RDS for Oracle updates the system time zone automatically throughout the year\. For more information about the system time zone, see [Oracle Time Zone](Appendix.Oracle.Options.Timezone.md)\.

When you create an Oracle DB instance, the database automatically sets the *database time zone*\. The database time zone is also known as the Daylight Saving Time \(DST\) time zone\. The database time zone is distinct from the system time zone\.

Between Oracle Database releases, patch sets or individual patches may include new DST versions\. These patches reflect the changes in transition rules for various time zone regions\. For example, a government might change when DST takes effect\. Changes to DST rules may affect existing data of the `TIMESTAMP WITH TIME ZONE` data type\.

If you upgrade an RDS for Oracle instance, Amazon RDS does not upgrade the database time zone automatically\. To upgrade the database time zone manually, create a new Oracle DB instance that has the desired DST patch\. Then migrate the data from your current instance to the new instance\. You can migrate data using several techniques, including the following:
+ Oracle GoldenGate
+ AWS Database Migration Service
+ Oracle Data Pump
+ Original Export/Import \(desupported for general use as of Oracle Database 11g\)

**Note**  
When you migrate data using Oracle Data Pump, the utility raises the error ORA\-39405 when the target time zone version is lower than the source time zone version\.

For more information, see [TIMESTAMP WITH TIMEZONE Restrictions](https://docs.oracle.com/en/database/oracle/oracle-database/19/sutil/oracle-data-pump-overview.html#GUID-9B6C92EE-860E-43DD-9728-735B17B9DA89) in the Oracle documentation\. 

## Testing an Upgrade<a name="USER_UpgradeDBInstance.Oracle.UpgradeTesting"></a>

Before you perform a major version upgrade on your DB instance, make sure to thoroughly test your database and all applications that access the database for compatibility with the new version\. We recommend that you use the following procedure\. 

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

1. If all tests pass, upgrade your production DB instance\. We recommend that you confirm that the DB instance working correctly before allowing write operations to the DB instance\.

## Preparing for the Automatic Upgrade of Oracle 11g SE1<a name="USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g"></a>

On November 1, 2020, we plan to begin automatically upgrading Oracle 11g SE1 License Included \(LI\) instances to Oracle 19c for Amazon RDS for Oracle\. All 11g instances, including reserved instances, will move to the latest available Oracle Release Update \(RU\)\.

**Important**  
If your DB instance is in the db\.t2\.micro or db\.t3\.micro instance class, your lease will be canceled\. You can purchase new reserved instances for the new instance class running on Oracle 19c SE2\. For more information, contact AWS Support\.

**Topics**
+ [Choosing an Upgrade Strategy](#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.strategy)
+ [Migrating from SE2 to EE Using Snapshots](#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.migrating-editions)
+ [Changing the License Model to BYOL](#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.changing-license-model)
+ [How the Automatic Upgrade of 11g SE1 Works](#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.how-it-works)

### Choosing an Upgrade Strategy<a name="USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.strategy"></a>

Before November 1, 2020, if your DB instance runs 11\.2\.0\.4 SE LI and you don't want Amazon RDS to upgrade it automatically, use one of the following strategies:
+ Upgrade your DB instance to Oracle versions 12\.1, 12\.2, 18c, or 19c on the LI model\.
+ Upgrade your 11\.2 snapshots, and then restore them\. For more information, see [Migrating from SE2 to EE Using Snapshots](#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.migrating-editions)\. 
+ Modify your instance to the Bring Your Own License \(BYOL\) model, if you have the appropriate SE1 license\. For more information, see [Changing the License Model to BYOL](#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.changing-license-model)\.

If your DB instance runs 11\.2\.0\.4 SE LI, and you want Amazon RDS to upgrade automatically starting on November 1, 2020, make sure that you understand the implications described in [How the Automatic Upgrade of 11g SE1 Works](#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.how-it-works)\.

### Migrating from SE2 to EE Using Snapshots<a name="USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.migrating-editions"></a>

To avoid the automatic upgrade, you can migrate from the SE editions of Oracle software to Enterprise Edition\. To use this technique, you need an unused Oracle license appropriate for the edition and class of DB instance that you plan to run\. You can't migrate from Enterprise Edition to other editions\.

When upgrading or restoring snapshots, provide new 19c SE2\-compliant parameter groups\. We recommend that you closely match your 11g SE1 and 19c SE2 option groups\. You have the following upgrade options:
+ If you have 11\.2\.0\.4 SE1 LI snapshots, upgrade them to 12\.1, 12\.2, 18c, or 19c SE2 on the LI model\.
+ If you have 11\.2\.0\.2 or 11\.2\.0\.3 SE1 LI snapshots, perform the following steps:

  1. Upgrade the snapshots from 11\.2\.0\.2 or 11\.2\.0\.3 to 11\.2\.0\.4\.

  1. Upgrade the snapshots from 11\.2\.0\.4 to 12\.1, 12\.2, 18c, or 19c SE2 LI\.

To change the Oracle edition and keep your data, take a snapshot of your running DB instance\. Then create a new DB instance of the edition that you want from the snapshot\. Unless you want to keep your old DB instance and you have the appropriate Oracle Database licenses, we recommend that you delete your old DB instance\.

**To migrate from 12\.2 SE2 to 12\.2 EE using snapshots**

1. Stop your application\.

1. Take a snapshot of your 12\.2 SE2 instance using the instructions in [Creating a DB Snapshot](USER_CreateSnapshot.md)\.

1. Restore the snapshot that you just created using the instructions in [Restoring from a Snapshot](USER_RestoreFromSnapshot.md#USER_RestoreFromSnapshot.Restoring)\. For **Engine**, choose **Oracle Enterprise Edition**\.

   To restore the snapshot, Oracle creates a new DB instance\.

1. Rename the original SE2 DB instance using the instructions in [Renaming a DB Instance](USER_RenameInstance.md)\. For example, if the original DB instance is `orcl1`, you might rename it to `orcl1-se2`\.

1. Rename the new EE instance to match the original name of the SE2 instance\. For example, if the original SE2 instance is named `orcl1`, rename the EE instance to `orcl1`\. Because the new instance has the same name as the original instance, it can reuse the listener endpoint\.

1. Start your application, and connect to the new EE instance\.

1. Ensure that the application works as expected, and then delete the SE2 instance\.

For more information about upgrading snapshots, see [Upgrading an Oracle DB Snapshot](USER_UpgradeDBSnapshot.Oracle.md)\.

### Changing the License Model to BYOL<a name="USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.changing-license-model"></a>

When you switch from LI to BYOL, your DB instance undergoes a few minutes of downtime\. During this period, RDS for Oracle moves your DB instance to a different host\. If you are concerned about downtime, we recommend that you test the license change on a restored production snapshot\.

#### Console<a name="USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.changing-license-model.console"></a>

One way of changing the license model to BYOL is by using the AWS Management Console\. 

**To change the license model to BYOL by using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. For **DB identifier**, choose the ID of the database whose license model you want to change\.

1. Choose **Modify**\.

1. For **License model**, choose **bring\-your\-own\-license**\.

1. Choose **Continue**\.

1. In the **Scheduling of modifications** section, choose **Apply immediately**\.

1. Choose **Modify DB Instance**\.

#### AWS CLI<a name="USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.changing-license-model.cli"></a>

To create an option group, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the `--license-model` parameter\.

**Example**  
The following example modifies switches the database named `orcl1` to the BYOL license model\.  
For Linux, macOS, or Unix:  

```
       
aws rds modify-db-instance \
    --db-instance-identifier orcl1 \
    --license_model bring-your-own-license
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier orcl1 ^
    --license_model bring-your-own-license
```
At this point, open a reserved instance cancellation request\. Ask your technical accounts manager to work with the reserved instance operations team\.

#### RDS API<a name="USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.changing-license-model.api"></a>

To create an option group, call the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) operation with the `LicenseModel` parameter\.

### How the Automatic Upgrade of 11g SE1 Works<a name="USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.how-it-works"></a>

We plan to begin automatically upgrading your RDS for Oracle 11\.2 instance on November 1, 2020, only if you haven't implemented a strategy from the previous section\. The automatic upgrade occurs during maintenance windows\. However, if maintenance windows aren't available when the upgrade needs to occur, Amazon RDS for Oracle upgrades the engine immediately\.

The upgrade goes in the following stages:

1. [Scaling Your Instance Class](#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.how-it-works.scaling)

1. [Upgrading 11\.2\.0\.4 SE1 to 19c SE2](#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.how-it-works.upgrading)

**Important**  
Automatic upgrades can have unexpected consequences for AWS CloudFormation stacks\. If you rely on Amazon RDS to upgrade your DB instances automatically, you might encounter issues with AWS CloudFormation\.

#### Scaling Your Instance Class<a name="USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.how-it-works.scaling"></a>

Some DB instance types aren't supported for 19c\. Before beginning the automatic upgrade, Amazon RDS scales the unsupported class to a corresponding supported class\. The following table lists the supported class that corresponds to each unsupported class\.


|  Unsupported Instance Class  |  vCPUs in Unsupported Class  | Memory in Unsupported Class |  Supported Instance Class  |  vCPUs in Supported Class  | Memory in Unsupported Class |  Huge Pages Is Default?  | 
| --- | --- | --- | --- | --- | --- | --- | 
|  t3\.micro  |  2  |  1 GB  |  t3\.small  |  2  |  2 GB  |  No  | 
|  t2\.micro  |  1  |  1 GB  |  t3\.small  |  2  |  2 GB  |  No  | 
|  t2\.small  |  1  |  2 GB  |  t3\.small  |  2  |  2 GB  |  No  | 
|  t2\.medium  |  2  |  4 GB  |  t3\.medium  |  2  |  4 GB  |  No  | 
|  t2\.large  |  2  |  8 GB  |  t3\.large  |  2  |  8 GB  |  No  | 
|  t2\.xlarge  |  4  |  16 GB  |  t3\.xlarge  |  4  |  16 GB  |  No  | 
|  t2\.2xlarge  |  8  |  32 GB  |  t3\.2xlarge  |  8  |  32 GB  |  No  | 
|  m3\.large  |  2  |  7\.5 GB  |  m5\.large  |  2  |  8 GB  |  No  | 
|  m3\.xlarge  |  4  |  15 GB  |  m5\.xlarge  |  4  |  16 GB  |  Yes  | 
|  m3\.2xlarge  |  8  |  30 GB  |  m5\.2xlarge  |  8  |  32 GB  |  Yes  | 
|  r3\.large  |  2  |  15\.25 GB  |  r5\.large  |  2  |  16 GB  |  Yes  | 
|  r3\.xlarge  |  4  |  30\.5 GB  |  r5\.xlarge  |  4  |  32 GB  |  Yes  | 
|  r3\.2xlarge  |  8  |  61 GB  |  r5\.2xlarge  |  8  |  64 GB  |  Yes  | 
|  r3\.4xlarge  |  16  |  122 GB  |  r5\.4xlarge  |  16  |  128 GB  |  Yes  | 
|  r3\.8xlarge  |  32  |  244 GB  |  r5\.8xlarge  |  32   |  256 GB  |  Yes  | 

If your new instance class has huge pages as the default, the upgrade performs the following steps:

1. Clones the parameter group\. Amazon RDS explicitly sets the initialization parameter `USE_LARGE_PAGES` to `FALSE`\.

1. Applies the cloned parameter group to the DB instance\.

1. Reboots the DB instance\.

1. Scales the DB instance to the new instance class\.

#### Upgrading 11\.2\.0\.4 SE1 to 19c SE2<a name="USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.how-it-works.upgrading"></a>

During the automatic upgrade, Amazon RDS performs the following steps:

1. Clones any custom parameter group to 19c parameter groups\. For more information, see [Cloning Parameter Groups](#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.how-it-works.upgrading.param-groups)\.

1. Clones any custom option groups to 19c option groups\. For more information, see [Cloning Option Groups](#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.how-it-works.upgrading.option-groups)\.

1. Patches the DB instance to 19c\. For more information, see [Applying the 19c Patches](#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.how-it-works.upgrading.patching)\.

##### Cloning Parameter Groups<a name="USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.how-it-works.upgrading.param-groups"></a>

During the automatic upgrade, RDS creates new 19c parameter groups for 11g instances\. The upgraded instance uses the new parameter groups\.

RDS copies all parameters and parameter values from 11g to 19c\. The exceptions are the following parameters, which RDS filters out before creating the parameter group:
+ `_sqlexec_progression_cost`
+ `exafusion_enabled`
+ `global_context_pool_size`
+ `max_connections`
+ `max_enabled_roles`
+ `o7_dictionary_access`
+ `optimizer_adaptive_features`
+ `parallel_automatic_tuning`
+ `parallel_degree_level`
+ `sec_case_sensitive_logon`
+ `standby_archive_dest`
+ `use_indirect_data_buffers`
+ `utl_file_dir`

##### Cloning Option Groups<a name="USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.how-it-works.upgrading.option-groups"></a>

During the automatic upgrade, RDS creates new 19c option groups for 11g instances\. The upgraded instance uses the new option groups\.

The following table describes options that require special handling\.


|  Option  |  Special Handling Notes  | 
| --- | --- | 
|  OEM\_AGENT  |  If you use a version of the agent older than 13\.1\.0\.0\.v1, Amazon RDS omits this option from cloned option group\. The 19c agent versions start at a higher version than what is available on 11g\.  | 
|  XMLDB  |  Amazon RDS omits this option from cloned option groups because this option isn't supported for releases later than 11g\.  | 
|  APEX  |  If you run APEX or APEX\-DEV version 4\.1\.1\.v1, 4\.2\.6\.v1, 5\.0\.4\.v1, 5\.1\.2\.v1, 5\.1\.4\.v1, 18\.1\.v1, or 18\.2\.v1, Amazon RDS updates it to version 19\.2\.v1\. If you run APEX 19\.1\.v1, Amazon RDS copies the option as it is\.  | 
|  SQLT  |  If you run SQLT 12\.1\.160429 or 12\.2\.180331, Amazon RDS uninstalls it and then upgrades it to 12\.2\.180725\.   The automatic upgrade deletes pre\-existing SQLT data on 12\.1\.160429 or 12\.2\.180331\.   | 

##### Applying the 19c Patches<a name="USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g.how-it-works.upgrading.patching"></a>

In the final stage of the upgrade, Amazon RDS applies the patches necessary to upgrade the DB engine to 19c\. Make sure that you specify the new 19c parameter and option groups to be used by the DB instance after completing the upgrade\.

## Upgrading an Oracle DB Instance<a name="USER_UpgradeDBInstance.Oracle.Upgrading"></a>

For information about manually or automatically upgrading an Oracle DB instance, see [Upgrading a DB Instance Engine Version](USER_UpgradeDBInstance.Upgrading.md)\.