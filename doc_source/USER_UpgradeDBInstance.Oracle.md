# Upgrading the Oracle DB Engine<a name="USER_UpgradeDBInstance.Oracle"></a>

When Amazon RDS supports a new version of Oracle, you can upgrade your DB instances to the new version\. Amazon RDS supports the following upgrades to an Oracle DB instance: 
+ **Major Version Upgrades** – from 11g to 12c\. 
+ **Minor Version Upgrades**  

You must perform all upgrades manually, and an outage occurs while the upgrade takes place\. The time for the outage varies based on your engine version and the size of your DB instance\. 

For information about what Oracle versions are available on Amazon RDS, see [Oracle Database Engine Release Notes](Appendix.Oracle.PatchComposition.md)\. 

## Overview of Upgrading<a name="USER_UpgradeDBInstance.Oracle.Overview"></a>

Amazon RDS takes two DB snapshots during the upgrade process\. The first DB snapshot is of the DB instance before any upgrade changes have been made\. If the upgrade doesn't work for your databases, you can restore this snapshot to create a DB instance running the old version\. The second DB snapshot is taken after the upgrade completes\. 

**Note**  
Amazon RDS only takes DB snapshots if you have set the backup retention period for your DB instance to a number greater than 0\. To change your backup retention period, see [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)\. 

After an upgrade is complete, you can't revert to the previous version of the database engine\. If you want to return to the previous version, restore the DB snapshot that was taken before the upgrade to create a new DB instance\. 

If your DB instance is in a Multi\-AZ deployment, both the primary and standby replicas are upgraded\. The primary and standby DB instances are upgraded at the same time, and you experience an outage until the upgrade is complete\. 

## Major Version Upgrades<a name="USER_UpgradeDBInstance.Oracle.Major"></a>

Amazon RDS supports upgrades of Oracle DB instances running Oracle version 11\.2\.0\.4 to Oracle version 12\.1\.0\.2\.v5 and higher\. You must modify the DB instance manually to perform a major version upgrade\. Major version upgrades do not occur automatically\. 

**Note**  
Major version upgrades are not supported for deprecated Oracle versions, such as Oracle version 11\.2\.0\.3 and 11\.2\.0\.2\.
Major version downgrades are not supported\.
A major version upgrade from 11g to 12c must upgrade to an Oracle PSU that was released in the same month or later\.  
For example, a major version upgrade from Oracle version 11\.2\.0\.4\.v14 to Oracle version 12\.1\.0\.2\.v11 is supported\. However, a major version upgrade from Oracle version 11\.2\.0\.4\.v14 to Oracle version 12\.1\.0\.2\.v9 is not supported, because Oracle version 11\.2\.0\.4\.v14 was released in October 2017 while Oracle version 12\.1\.0\.2\.v9 was released in July 2017\. For information about the release date for each Oracle PSU, see [Oracle Database Engine Release Notes](Appendix.Oracle.PatchComposition.md)\.

## Oracle Minor Version Upgrades<a name="USER_UpgradeDBInstance.Oracle.Minor"></a>

You must modify the DB instance manually to perform a minor version upgrade\. Minor version upgrades do not occur automatically\. A minor version upgrade applies an Oracle PSU\. 

The following minor version upgrades are not supported\. 


****  

| Current Version | Upgrade Not Supported | 
| --- | --- | 
| 12\.1\.0\.2\.v6 | 12\.1\.0\.2\.v7 | 
| 12\.1\.0\.2\.v5 | 12\.1\.0\.2\.v7 | 
| 12\.1\.0\.2\.v5 | 12\.1\.0\.2\.v6 | 

**Note**  
Minor version downgrades are not supported\.

## Oracle SE2 Upgrade Paths<a name="USER_UpgradeDBInstance.Oracle.SE2"></a>

The following table shows supported upgrade paths to Standard Edition Two \(SE2\)\. For more information about the License Included and Bring Your Own License \(BYOL\) models, see [Oracle Licensing](CHAP_Oracle.md#Oracle.Concepts.Licensing)\. 


****  

| Your Existing Configuration | Supported SE2 Configuration | 
| --- | --- | 
|  12\.1\.0\.2 SE2, BYOL  |  12\.1\.0\.2 SE2, BYOL or License Included  | 
|  11\.2\.0\.4 SE1, BYOL or License Included 11\.2\.0\.4 SE, BYOL  |  12\.1\.0\.2 SE2, BYOL or License Included  | 

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
   + [AWS Management Console](#USER_UpgradeDBInstance.Oracle.Console)
   + [CLI](#USER_UpgradeDBInstance.Oracle.CLI)
   + [API](#USER_UpgradeDBInstance.Oracle.API)

1. Perform testing: 
   + Run as many of your quality assurance tests against the upgraded DB instance as needed to ensure that your database and application work correctly with the new version\. 
   + Implement any new tests needed to evaluate the impact of any compatibility issues that you identified in step 1\. 
   + Test all stored procedures, functions, and triggers\. 
   + Direct test versions of your applications to the upgraded DB instance\. Verify that the applications work correctly with the new version\. 
   + Evaluate the storage used by the upgraded instance to determine if the upgrade requires additional storage\. You might need to choose a larger instance class to support the new version in production\. For more information, see [DB Instance Class](Concepts.DBInstanceClass.md)\. 

1. If all tests pass, then perform the upgrade on your production DB instance\. We recommend that you don't allow write operations to the DB instance until you confirm that everything is working correctly\. 

## AWS Management Console<a name="USER_UpgradeDBInstance.Oracle.Console"></a>

To upgrade an Oracle DB instance by using the AWS Management Console, you follow the same procedure as when you modify the DB instance\. To upgrade, change the DB engine version\. For more detailed instructions, see [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)\. 

## CLI<a name="USER_UpgradeDBInstance.Oracle.CLI"></a>

To upgrade an Oracle DB instance by using the AWS CLI, call the [modify\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the following parameters: 
+ `--db-instance-identifier` – the name of the DB instance\. 
+ `--engine-version` – the version number of the database engine to upgrade to\. 
+ `--allow-major-version-upgrade` – to upgrade major version\. 
+ `--no-apply-immediately` – apply changes during the next maintenance window\. To apply changes immediately, use `--apply-immediately`\. For more information, see [The Impact of Apply Immediately](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\. 

You might also need to include the following parameters\. For more information, see [Option Group Considerations](#USER_UpgradeDBInstance.Oracle.OGPG.OG) and [Parameter Group Considerations](#USER_UpgradeDBInstance.Oracle.OGPG.PG)\. 
+ `--option-group-name` – the option group for the upgraded DB instance\. 
+ `--db-parameter-group-name` – the parameter group for the upgraded DB instance\. 

**Example**  
The following code upgrades a DB instance\. These changes are applied during the next maintenance window\.   
For Linux, OS X, or Unix:  

```
1. aws rds modify-db-instance \
2.     --db-instance-identifier <mydbinstance> \
3.     --engine-version <12.1.0.2.v10> \
4.     --option-group-name <default:oracle-ee-12-1> \
5.     --db-parameter-group-name <default.oracle-ee-12.1> \
6.     --allow-major-version-upgrade \
7.     --no-apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier <mydbinstance> ^
    --engine-version <12.1.0.2.v10> ^
    --option-group-name <default:oracle-ee-12-1> ^
    --db-parameter-group-name <default.oracle-ee-12.1> ^
    --allow-major-version-upgrade ^
    --no-apply-immediately
```

## API<a name="USER_UpgradeDBInstance.Oracle.API"></a>

To upgrade an Oracle DB instance by using the Amazon RDS API, call the [ModifyDBInstance](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) action with the following parameters: 
+ `DBInstanceIdentifier` – the name of the DB instance\. 
+ `EngineVersion` – the version number of the database engine to upgrade to\. 
+ `AllowMajorVersionUpgrade` – set to `true` to upgrade major version\. 
+ `ApplyImmediately` – whether to apply changes immediately or during the next maintenance window\. To apply changes immediately, set the value to `true`\. To apply changes during the next maintenance window, set the value to `false`\. For more information, see [The Impact of Apply Immediately](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\. 

You might also need to include the following parameters\. For more information, see [Option Group Considerations](#USER_UpgradeDBInstance.Oracle.OGPG.OG) and [Parameter Group Considerations](#USER_UpgradeDBInstance.Oracle.OGPG.PG)\. 
+ `OptionGroupName` – the option group for the upgraded DB instance\. 
+ `DBParameterGroupName` – the parameter group for the upgraded DB instance\. 

**Example**  
The following code upgrades a DB instance\. These changes are applied during the next maintenance window\.   

```
 1. https://rds.amazonaws.com/
 2.     ?Action=ModifyDBInstance
 3.     &AllowMajorVersionUpgrade=true
 4.     &ApplyImmediately=false
 5.     &DBInstanceIdentifier=mydbinstance
 6.     &DBParameterGroupName=default.oracle-ee-12.1
 7.     &EngineVersion=12.1.0.2.v10
 8.     &OptionGroupName=default:oracle-ee-12-1
 9.     &SignatureMethod=HmacSHA256
10.     &SignatureVersion=4
11.     &Version=2014-10-31
12.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
13.     &X-Amz-Credential=AKIADQKE4SARGYLE/20131016/us-west-1/rds/aws4_request
14.     &X-Amz-Date=20131016T233051Z
15.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
16.     &X-Amz-Signature=087a8eb41cb1ab5f99e81575f23e73757ffc6a1e42d7d2b30b9cc0be988cff97
```

## Related Topics<a name="USER_UpgradeDBInstance.Oracle.Related"></a>
+ [Upgrading an Oracle DB Snapshot](USER_UpgradeDBSnapshot.Oracle.md)
+ [Applying Updates for a DB Instance](USER_UpgradeDBInstance.Maintenance.md#USER_UpgradeDBInstance.OSUpgrades)
+ [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)