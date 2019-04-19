# Upgrading the Microsoft SQL Server DB Engine<a name="USER_UpgradeDBInstance.SQLServer"></a>

When Amazon RDS supports a new version of Microsoft SQL Server, you can upgrade your DB instances to the new version\. Amazon RDS supports the following upgrades to a Microsoft SQL Server DB instance: 
+ Major Version Upgrades
+ Minor Version Upgrades

In general, a major engine version upgrade can introduce changes that are not compatible with existing applications\. In contrast, a minor version upgrade includes only changes that are backward\-compatible with existing applications\. 

You must modify the DB instance manually to perform a major version upgrade\. Minor version upgrades occur automatically if you enable auto minor version upgrades on your DB instance\. In all other cases, you must modify the DB instance manually to perform a minor version upgrade\.

For information about what SQL Server versions are available on Amazon RDS, see [Microsoft SQL Server on Amazon RDS](CHAP_SQLServer.md)\. 

**Topics**
+ [Overview of Upgrading](#USER_UpgradeDBInstance.SQLServer.Overview)
+ [Major Version Upgrades](#USER_UpgradeDBInstance.SQLServer.Major)
+ [Multi\-AZ and In\-Memory Optimization Considerations](#USER_UpgradeDBInstance.SQLServer.MAZ)
+ [Option and Parameter Group Considerations](#USER_UpgradeDBInstance.SQLServer.OGPG)
+ [Testing an Upgrade](#USER_UpgradeDBInstance.SQLServer.UpgradeTesting)
+ [Upgrading a SQL Server DB Instance](#USER_UpgradeDBInstance.SQLServer.Upgrading)

## Overview of Upgrading<a name="USER_UpgradeDBInstance.SQLServer.Overview"></a>

Amazon RDS takes two DB snapshots during the upgrade process\. The first DB snapshot is of the DB instance before any upgrade changes have been made\. If the upgrade doesn't work for your databases, you can restore this snapshot to create a DB instance running the old version\. The second DB snapshot is taken after the upgrade completes\. 

**Note**  
Amazon RDS only takes DB snapshots if you have set the backup retention period for your DB instance to a number greater than 0\. To change your backup retention period, see [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)\. 

After an upgrade is complete, you can't revert to the previous version of the database engine\. If you want to return to the previous version, restore the DB snapshot that was taken before the upgrade to create a new DB instance\. 

During a minor or major version upgrade of SQL Server, the **Free Storage Space** and **Disk Queue Depth** metrics will display `-1`\. After the upgrade is complete, both metrics will return to normal\. 

## Major Version Upgrades<a name="USER_UpgradeDBInstance.SQLServer.Major"></a>

Amazon RDS currently supports the following major version upgrades to a Microsoft SQL Server DB instance\. 

You can upgrade your existing DB instance to SQL Server 2017 from any version except SQL Server 2008\. To upgrade from SQL Server 2008, first upgrade to one of the other versions\.


****  

| Current Version | Supported Upgrade Versions | 
| --- | --- | 
|  SQL Server 2014  |  SQL Server 2017 SQL Server 2016  | 
|  SQL Server 2012  |  SQL Server 2017 SQL Server 2016 SQL Server 2014  | 
|  SQL Server 2008 R2  |  SQL Server 2016 SQL Server 2014 SQL Server 2012  | 

### Database Compatibility Level<a name="USER_UpgradeDBInstance.SQLServer.Major.Compatibility"></a>

You can use Microsoft SQL Server database compatibility levels to adjust some database behaviors to mimic previous versions of SQL Server\. For more information, see [Compatibility Level](https://msdn.microsoft.com/en-us/library/bb510680.aspx) in the Microsoft documentation\. 

When you upgrade your DB instance, all existing databases remain at their original compatibility level\. For example, if you upgrade from SQL Server 2012 to SQL Server 2014, all existing databases have a compatibility level of 110\. Any new database created after the upgrade have compatibility level 120\. 

You can change the compatibility level of a database by using the ALTER DATABASE command\. For example, to change a database named `customeracct` to be compatible with SQL Server 2014, issue the following command: 

```
1. ALTER DATABASE customeracct SET COMPATIBILITY_LEVEL = 120
```

## Multi\-AZ and In\-Memory Optimization Considerations<a name="USER_UpgradeDBInstance.SQLServer.MAZ"></a>

Amazon RDS supports Multi\-AZ deployments for DB instances running Microsoft SQL Server by using SQL Server Database Mirroring or Always On\. For more information, see [Multi\-AZ Deployments for Microsoft SQL Server](USER_SQLServerMultiAZ.md)\. 

If your DB instance is in a Multi\-AZ deployment, both the primary and standby instances are upgraded\. Amazon RDS does rolling upgrades\. You have an outage only for the duration of a failover\. 

SQL Server 2014/2016/2017 Enterprise Edition supports in\-memory optimization\. 

## Option and Parameter Group Considerations<a name="USER_UpgradeDBInstance.SQLServer.OGPG"></a>

### Option Group Considerations<a name="USER_UpgradeDBInstance.SQLServer.OGPG.OG"></a>

If your DB instance uses a custom option group, in some cases Amazon RDS can't automatically assign your DB instance a new option group\. For example, when you upgrade to a new major version\. In that case, you must specify a new option group when you upgrade\. We recommend that you create a new option group, and add the same options to it as your existing custom option group\. 

For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create) or [Making a Copy of an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Copy)\. 

### Parameter Group Considerations<a name="USER_UpgradeDBInstance.SQLServer.OGPG.PG"></a>

If your DB instance uses a custom parameter group, in some cases Amazon RDS can't automatically assign your DB instance a new parameter group\. For example, when you upgrade to a new major version\. In that case, you must specify a new parameter group when you upgrade\. We recommend that you create a new parameter group, and configure the parameters as in your existing custom parameter group\. 

For more information, see [Creating a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Creating) or [Copying a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Copying)\. 

## Testing an Upgrade<a name="USER_UpgradeDBInstance.SQLServer.UpgradeTesting"></a>

Before you perform a major version upgrade on your DB instance, you should thoroughly test your database, and all applications that access the database, for compatibility with the new version\. We recommend that you use the following procedure\. 

**To test a major version upgrade**

1. Review the upgrade documentation for the new version of the database engine to see if there are compatibility issues that might affect your database or applications: 
   +  [Upgrade to SQL Server 2016 or 2017](https://msdn.microsoft.com/en-us/library/bb677622%28v=sql.130%29.aspx) 
   +  [Upgrade to SQL Server 2014](https://msdn.microsoft.com/en-us/library/bb677622%28v=sql.120%29.aspx) 
   +  [Upgrade to SQL Server 2012](https://msdn.microsoft.com/en-us/library/bb677622%28v=sql.110%29.aspx) 

1. If your DB instance uses a custom option group, create a new option group compatible with the new version you are upgrading to\. For more information, see [Option Group Considerations](#USER_UpgradeDBInstance.SQLServer.OGPG.OG)\. 

1. If your DB instance uses a custom parameter group, create a new parameter group compatible with the new version you are upgrading to\. For more information, see [Parameter Group Considerations](#USER_UpgradeDBInstance.SQLServer.OGPG.PG)\. 

1. Create a DB snapshot of the DB instance to be upgraded\. For more information, see [Creating a DB Snapshot](USER_CreateSnapshot.md)\. 

1. Restore the DB snapshot to create a new test DB instance\. For more information, see [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)\. 

1. Modify this new test DB instance to upgrade it to the new version, by using one of the following methods: 
   + [Upgrading the Engine Version of a DB Instance Using the Console](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual.Console)
   + [Upgrading the Engine Version of a DB Instance Using the AWS CLI](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual.CLI)
   + [Upgrading the Engine Version of a DB Instance Using the RDS API](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual.API)

1. Evaluate the storage used by the upgraded instance to determine if the upgrade requires additional storage\. 

1. Run as many of your quality assurance tests against the upgraded DB instance as needed to ensure that your database and application work correctly with the new version\. Implement any new tests needed to evaluate the impact of any compatibility issues you identified in step 1\. Test all stored procedures and functions\. Direct test versions of your applications to the upgraded DB instance\. 

1. If all tests pass, then perform the upgrade on your production DB instance\. We recommend that you do not allow write operations to the DB instance until you confirm that everything is working correctly\. 

## Upgrading a SQL Server DB Instance<a name="USER_UpgradeDBInstance.SQLServer.Upgrading"></a>

For information about manually or automatically upgrading a SQL Server DB instance, see [Upgrading a DB Instance Engine Version](USER_UpgradeDBInstance.Upgrading.md)\.