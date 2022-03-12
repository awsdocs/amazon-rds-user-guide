# Upgrading the Oracle DB engine<a name="USER_UpgradeDBInstance.Oracle"></a>

When Amazon RDS supports a new version of Oracle, you can upgrade your DB instances to the new version\. For information about which Oracle versions are available on Amazon RDS, see [Oracle database engine release notes](Appendix.Oracle.PatchComposition.md)\.

**Important**  
RDS for Oracle Database 11g is deprecated\. If you maintain Oracle Database 11g snapshots, you can upgrade them to a later release\. For more information, see [Upgrading an Oracle DB snapshot](USER_UpgradeDBSnapshot.Oracle.md)\.

**Topics**
+ [Overview of Oracle DB engine upgrades](USER_UpgradeDBInstance.Oracle.Overview.md)
+ [Oracle major version upgrades](#USER_UpgradeDBInstance.Oracle.Major)
+ [Oracle minor version upgrades](#USER_UpgradeDBInstance.Oracle.Minor)
+ [Oracle SE2 upgrade paths](#USER_UpgradeDBInstance.Oracle.SE2)
+ [Considerations for Oracle DB upgrades](USER_UpgradeDBInstance.Oracle.OGPG.md)
+ [Testing an Oracle DB upgrade](USER_UpgradeDBInstance.Oracle.UpgradeTesting.md)
+ [Upgrading an Oracle DB instance](USER_UpgradeDBInstance.Oracle.Upgrading.md)
+ [Upgrading an Oracle DB snapshot](USER_UpgradeDBSnapshot.Oracle.md)

## Oracle major version upgrades<a name="USER_UpgradeDBInstance.Oracle.Major"></a>

To perform a major version upgrade, modify the DB instance manually\. Major version upgrades don't occur automatically\. 

### Supported versions for major upgrades<a name="USER_UpgradeDBInstance.Oracle.Major.supported-versions"></a>

Amazon RDS supports the following major version upgrades\.


****  

| Current version | Upgrade supported | 
| --- | --- | 
|  19\.0\.0\.0 using the CDB architecture  |  21\.0\.0\.0  | 
|  12\.2\.0\.1  |  19\.0\.0\.0 using the non\-CDB architecture  | 
|  12\.1\.0\.2  |  19\.0\.0\.0 using the non\-CDB architecture 12\.2\.0\.1  | 

A major version upgrade of Oracle Database must upgrade to a Release Update \(RU\) that was released in the same month or later\. Major version downgrades aren't supported for any Oracle Database versions\.

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

The following table shows supported upgrade paths to Standard Edition Two \(SE2\)\. For more information about the License Included and Bring Your Own License \(BYOL\) models, see [Oracle licensing options](Oracle.Concepts.Licensing.md)\. 


****  

| Your existing configuration | Supported SE2 configuration | 
| --- | --- | 
|  12\.2\.0\.1 SE2, BYOL  |  12\.2\.0\.1 SE2, BYOL or License Included  | 
|  12\.1\.0\.2 SE2, BYOL  |  12\.2\.0\.1 SE2, BYOL or License Included 12\.1\.0\.2 SE2, BYOL or License Included  | 

To upgrade from your existing configuration to a supported SE2 configuration, use a supported upgrade path\. For more information, see [Oracle major version upgrades](#USER_UpgradeDBInstance.Oracle.Major)\. 