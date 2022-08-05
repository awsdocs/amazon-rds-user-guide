# Upgrading the Microsoft SQL Server DB engine<a name="USER_UpgradeDBInstance.SQLServer"></a>

When Amazon RDS supports a new version of a database engine, you can upgrade your DB instances to the new version\. There are two kinds of upgrades for SQL Server DB instances: major version upgrades and minor version upgrades\. 

*Major version upgrades* can contain database changes that are not backward\-compatible with existing applications\. As a result, you must manually perform major version upgrades of your DB instances\. You can initiate a major version upgrade by modifying your DB instance\. However, before you perform a major version upgrade, we recommend that you test the upgrade by following the steps described in [Testing an upgrade](#USER_UpgradeDBInstance.SQLServer.UpgradeTesting)\. 

In contrast, *minor version upgrades* include only changes that are backward\-compatible with existing applications\. You can initiate a minor version upgrade manually by modifying your DB instance\.

Alternatively, you can enable the **Auto minor version upgrade** option when creating or modifying a DB instance\. Doing so means that your DB instance is automatically upgraded after Amazon RDS tests and approves the new version\. You can confirm whether the minor version upgrade will be automatic by using the `describe-db-engine-versions` AWS CLI command\. For example:

```
aws rds describe-db-engine-versions --engine sqlserver-se --engine-version 14.00.3049.1.v1
```

In the following example, the CLI command returns a response indicating that upgrades are automatic\.

```
...

"ValidUpgradeTarget": [
    {
        "Engine": "sqlserver-se",
        "EngineVersion": "14.00.3192.2.v1",
        "Description": "SQL Server 2017 14.00.3192.2.v1",
        "AutoUpgrade": true,
        "IsMajorVersionUpgrade": false
    }

...
```

For more information about performing upgrades, see [Upgrading a SQL Server DB instance](#USER_UpgradeDBInstance.SQLServer.Upgrading)\. For information about what SQL Server versions are available on Amazon RDS, see [Amazon RDS for Microsoft SQL Server](CHAP_SQLServer.md)\.

**Topics**
+ [Overview of upgrading](#USER_UpgradeDBInstance.SQLServer.Overview)
+ [Major version upgrades](#USER_UpgradeDBInstance.SQLServer.Major)
+ [Multi\-AZ and in\-memory optimization considerations](#USER_UpgradeDBInstance.SQLServer.MAZ)
+ [Option group considerations](#USER_UpgradeDBInstance.SQLServer.OGPG.OG)
+ [Parameter group considerations](#USER_UpgradeDBInstance.SQLServer.OGPG.PG)
+ [Testing an upgrade](#USER_UpgradeDBInstance.SQLServer.UpgradeTesting)
+ [Upgrading a SQL Server DB instance](#USER_UpgradeDBInstance.SQLServer.Upgrading)
+ [Upgrading deprecated DB instances before support ends](#USER_UpgradeDBInstance.SQLServer.DeprecatedVersions)

## Overview of upgrading<a name="USER_UpgradeDBInstance.SQLServer.Overview"></a>

Amazon RDS takes two DB snapshots during the upgrade process\. The first DB snapshot is of the DB instance before any upgrade changes have been made\. The second DB snapshot is taken after the upgrade finishes\.

**Note**  
Amazon RDS only takes DB snapshots if you have set the backup retention period for your DB instance to a number greater than 0\. To change your backup retention period, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

After an upgrade is completed, you can't revert to the previous version of the database engine\. If you want to return to the previous version, restore from the DB snapshot that was taken before the upgrade to create a new DB instance\. 

During a minor or major version upgrade of SQL Server, the **Free Storage Space** and **Disk Queue Depth** metrics will display `-1`\. After the upgrade is completed, both metrics will return to normal\.

## Major version upgrades<a name="USER_UpgradeDBInstance.SQLServer.Major"></a>

Amazon RDS currently supports the following major version upgrades to a Microsoft SQL Server DB instance\.

You can upgrade your existing DB instance to SQL Server 2017 or 2019 from any version except SQL Server 2008\. To upgrade from SQL Server 2008, first upgrade to one of the other versions\.


****  

| Current version | Supported upgrade versions | 
| --- | --- | 
|  SQL Server 2017  |  SQL Server 2019  | 
|  SQL Server 2016  |  SQL Server 2019 SQL Server 2017  | 
| SQL Server 2014 |  SQL Server 2019 SQL Server 2017 SQL Server 2016  | 
| SQL Server 2012 \(end of support\) |  SQL Server 2019 SQL Server 2017 SQL Server 2016 SQL Server 2014  | 
| SQL Server 2008 R2 \(end of support\)  |  SQL Server 2016 SQL Server 2014 SQL Server 2012  | 

You can use an AWS CLI query, such as the following example, to find the available upgrades for a particular database engine version\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds describe-db-engine-versions \
    --engine sqlserver-se \
    --engine-version 14.00.3049.1.v1 \
    --query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" \
    --output table
```
For Windows:  

```
aws rds describe-db-engine-versions ^
    --engine sqlserver-se ^
    --engine-version 14.00.3049.1.v1 ^
    --query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" ^
    --output table
```
The output shows that you can upgrade version 14\.00\.3049\.1 to the latest SQL Server 2017 or 2019 versions\.  

```
--------------------------
|DescribeDBEngineVersions|
+------------------------+
|      EngineVersion     |
+------------------------+
|  14.00.3294.2.v1       |
|  14.00.3356.20.v1      |
|  14.00.3381.3.v1       |
|  15.00.4043.16.v1      |
|  15.00.4073.23.v1      |
+------------------------+
```

### Database compatibility level<a name="USER_UpgradeDBInstance.SQLServer.Major.Compatibility"></a>

You can use Microsoft SQL Server database compatibility levels to adjust some database behaviors to mimic previous versions of SQL Server\. For more information, see [Compatibility level](https://msdn.microsoft.com/en-us/library/bb510680.aspx) in the Microsoft documentation\. 

When you upgrade your DB instance, all existing databases remain at their original compatibility level\. For example, if you upgrade from SQL Server 2014 to SQL Server 2016, all existing databases have a compatibility level of 120\. Any new database created after the upgrade have compatibility level 130\. 

You can change the compatibility level of a database by using the ALTER DATABASE command\. For example, to change a database named `customeracct` to be compatible with SQL Server 2014, issue the following command: 

```
1. ALTER DATABASE customeracct SET COMPATIBILITY_LEVEL = 120
```

## Multi\-AZ and in\-memory optimization considerations<a name="USER_UpgradeDBInstance.SQLServer.MAZ"></a>

Amazon RDS supports Multi\-AZ deployments for DB instances running Microsoft SQL Server by using SQL Server Database Mirroring \(DBM\) or Always On Availability Groups \(AGs\)\. For more information, see [Multi\-AZ deployments for Amazon RDS for Microsoft SQL Server](USER_SQLServerMultiAZ.md)\.

If your DB instance is in a Multi\-AZ deployment, both the primary and standby instances are upgraded\. Amazon RDS does rolling upgrades\. You have an outage only for the duration of a failover\.

SQL Server 2014 through 2019 Enterprise Edition support in\-memory optimization\.

## Option group considerations<a name="USER_UpgradeDBInstance.SQLServer.OGPG.OG"></a>

If your DB instance uses a custom DB option group, in some cases Amazon RDS can't automatically assign your DB instance a new option group\. For example, when you upgrade to a new major version, you must specify a new option group\. We recommend that you create a new option group, and add the same options to it as your existing custom option group\.

For more information, see [Creating an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create) or [Copying an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Copy)\.

## Parameter group considerations<a name="USER_UpgradeDBInstance.SQLServer.OGPG.PG"></a>

If your DB instance uses a custom DB parameter group:
+ Amazon RDS automatically reboots the DB instance after an upgrade\.
+ In some cases, RDS can't automatically assign a new parameter group to your DB instance\.

  For example, when you upgrade to a new major version, you must specify a new parameter group\. We recommend that you create a new parameter group, and configure the parameters as in your existing custom parameter group\.

For more information, see [Creating a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Creating) or [Copying a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Copying)\.

## Testing an upgrade<a name="USER_UpgradeDBInstance.SQLServer.UpgradeTesting"></a>

Before you perform a major version upgrade on your DB instance, you should thoroughly test your database, and all applications that access the database, for compatibility with the new version\. We recommend that you use the following procedure\.

**To test a major version upgrade**

1. Review [Upgrade SQL Server](https://docs.microsoft.com/en-us/sql/database-engine/install-windows/upgrade-sql-server) in the Microsoft documentation for the new version of the database engine to see if there are compatibility issues that might affect your database or applications\.

1. If your DB instance uses a custom option group, create a new option group compatible with the new version you are upgrading to\. For more information, see [Option group considerations](#USER_UpgradeDBInstance.SQLServer.OGPG.OG)\.

1. If your DB instance uses a custom parameter group, create a new parameter group compatible with the new version you are upgrading to\. For more information, see [Parameter group considerations](#USER_UpgradeDBInstance.SQLServer.OGPG.PG)\.

1. Create a DB snapshot of the DB instance to be upgraded\. For more information, see [Creating a DB snapshot](USER_CreateSnapshot.md)\.

1. Restore the DB snapshot to create a new test DB instance\. For more information, see [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)\.

1. Modify this new test DB instance to upgrade it to the new version, by using one of the following methods:
   + [Console](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual.Console)
   + [AWS CLI](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual.CLI)
   + [RDS API](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual.API)

1. Evaluate the storage used by the upgraded instance to determine if the upgrade requires additional storage\. 

1. Run as many of your quality assurance tests against the upgraded DB instance as needed to ensure that your database and application work correctly with the new version\. Implement any new tests needed to evaluate the impact of any compatibility issues you identified in step 1\. Test all stored procedures and functions\. Direct test versions of your applications to the upgraded DB instance\. 

1. If all tests pass, then perform the upgrade on your production DB instance\. We recommend that you do not allow write operations to the DB instance until you confirm that everything is working correctly\. 

## Upgrading a SQL Server DB instance<a name="USER_UpgradeDBInstance.SQLServer.Upgrading"></a>

For information about manually or automatically upgrading a SQL Server DB instance, see the following:
+ [Upgrading a DB instance engine version](USER_UpgradeDBInstance.Upgrading.md)
+ [Best practices for upgrading SQL Server 2008 R2 to SQL Server 2016 on Amazon RDS for SQL Server](http://aws.amazon.com/blogs/database/best-practices-for-upgrading-sql-server-2008-r2-to-sql-server-2016-on-amazon-rds-for-sql-server/)

**Important**  
If you have any snapshots that are encrypted using AWS KMS, we recommend that you initiate an upgrade before support ends\. 

## Upgrading deprecated DB instances before support ends<a name="USER_UpgradeDBInstance.SQLServer.DeprecatedVersions"></a>

After a major version is deprecated, you can't install it on new DB instances\. RDS will try to automatically upgrade all existing DB instances\. 

If you need to restore a deprecated DB instance, you can do point\-in\-time recovery \(PITR\) or restore a snapshot\. Doing this gives you temporary access a DB instance that uses the version that is being deprecated\. However, after a major version is fully deprecated, these DB instances will also be automatically upgraded to a supported version\. 