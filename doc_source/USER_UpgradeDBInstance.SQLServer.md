# Upgrading the Microsoft SQL Server DB Engine<a name="USER_UpgradeDBInstance.SQLServer"></a>

When Amazon RDS supports a new version of Microsoft SQL Server, you can upgrade your DB instances to the new version\. Amazon RDS supports the following upgrades to a Microsoft SQL Server DB instance: 
+ Major Version Upgrades
+ Minor Version Upgrades

You must perform all upgrades manually, and an outage occurs while the upgrade takes place\. The time for the outage varies based on your engine version and the size of your DB instance\. 

For information about what SQL Server versions are available on Amazon RDS, see [Microsoft SQL Server on Amazon RDS](CHAP_SQLServer.md)\. 

## Overview of Upgrading<a name="USER_UpgradeDBInstance.SQLServer.Overview"></a>

Amazon RDS takes two DB snapshots during the upgrade process\. The first DB snapshot is of the DB instance before any upgrade changes have been made\. If the upgrade doesn't work for your databases, you can restore this snapshot to create a DB instance running the old version\. The second DB snapshot is taken after the upgrade completes\. 

**Note**  
Amazon RDS only takes DB snapshots if you have set the backup retention period for your DB instance to a number greater than 0\. To change your backup retention period, see [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)\. 

After an upgrade is complete, you can't revert to the previous version of the database engine\. If you want to return to the previous version, restore the DB snapshot that was taken before the upgrade to create a new DB instance\. 

During a minor or major version upgrade of SQL Server, the **Free Storage Space** and **Disk Queue Depth** metrics will display `-1`\. After the upgrade is complete, both metrics will return to normal\. 

## Major Version Upgrades<a name="USER_UpgradeDBInstance.SQLServer.Major"></a>

Amazon RDS currently supports the following major version upgrades to a Microsoft SQL Server DB instance\. 

**Note**  
Currently, you can't upgrade your existing DB instance to SQL Server 2017\.


****  

| Current Version | Supported Upgrade Versions | 
| --- | --- | 
|  SQL Server 2014  |  SQL Server 2016  | 
|  SQL Server 2012  |  SQL Server 2016 SQL Server 2014  | 
|  SQL Server 2008 R2  |  SQL Server 2016 SQL Server 2014 SQL Server 2012  | 

### Database Compatibility Level<a name="USER_UpgradeDBInstance.SQLServer.Major.Compatibility"></a>

You can use Microsoft SQL Server database compatibility levels to adjust some database behaviors to mimic previous versions of SQL Server\. For more information, see [Compatibility Level](https://msdn.microsoft.com/en-us/library/bb510680.aspx) in the Microsoft documentation\. 

The following are the compatibility levels of the SQL Server versions: 
+ SQL Server 2016 – compatibility level 130
+ SQL Server 2014 – compatibility level 120
+ SQL Server 2012 – compatibility level 110

When you upgrade your DB instance, all existing databases remain at their original compatibility level\. For example, if you upgrade from SQL Server 2012 to SQL Server 2014, all existing databases have a compatibility level of 110\. Any new database created after the upgrade have compatibility level 120\. 

You can change the compatibility level of a database by using the ALTER DATABASE command\. For example, to change a database named `customeracct` to be compatible with SQL Server 2014, issue the following command: 

```
1. ALTER DATABASE customeracct SET COMPATIBILITY_LEVEL = 120
```

## Multi\-AZ and In\-Memory Optimization Considerations<a name="USER_UpgradeDBInstance.SQLServer.MAZ"></a>

Amazon RDS supports Multi\-AZ deployments for DB instances running Microsoft SQL Server by using SQL Server Database Mirroring\. For more information, see [Multi\-AZ Deployments for Microsoft SQL Server with Database Mirroring](USER_SQLServerMultiAZ.md)\. 

If your DB instance is in a Multi\-AZ deployment, both the primary and standby instances are upgraded\. Amazon RDS does rolling upgrades\. You have an outage only for the duration of a failover\. 

SQL Server 2014 Enterprise Edition and SQL Server 2016 Enterprise Edition support in\-memory optimization\. Multi\-AZ deployments are not supported on DB instances that have in\-memory optimization enabled\. When you upgrade your DB instance with Multi\-AZ enabled, Amazon RDS automatically disables in\-memory optimization\. 

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
   +  [Upgrade to SQL Server 2016](https://msdn.microsoft.com/en-us/library/bb677622%28v=sql.130%29.aspx) 
   +  [Upgrade to SQL Server 2014](https://msdn.microsoft.com/en-us/library/bb677622%28v=sql.120%29.aspx) 
   +  [Upgrade to SQL Server 2012](https://msdn.microsoft.com/en-us/library/bb677622%28v=sql.110%29.aspx) 

1. If your DB instance uses a custom option group, create a new option group compatible with the new version you are upgrading to\. For more information, see [Option Group Considerations](#USER_UpgradeDBInstance.SQLServer.OGPG.OG)\. 

1. If your DB instance uses a custom parameter group, create a new parameter group compatible with the new version you are upgrading to\. For more information, see [Parameter Group Considerations](#USER_UpgradeDBInstance.SQLServer.OGPG.PG)\. 

1. Create a DB snapshot of the DB instance to be upgraded\. For more information, see [Creating a DB Snapshot](USER_CreateSnapshot.md)\. 

1. Restore the DB snapshot to create a new test DB instance\. For more information, see [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)\. 

1. Modify this new test DB instance to upgrade it to the new version, by using one of the following methods: 
   + [AWS Management Console](#USER_UpgradeDBInstance.SQLServer.Console)
   + [CLI](#USER_UpgradeDBInstance.SQLServer.CLI)
   + [API](#USER_UpgradeDBInstance.SQLServer.API)

1. Evaluate the storage used by the upgraded instance to determine if the upgrade requires additional storage\. 

1. Run as many of your quality assurance tests against the upgraded DB instance as needed to ensure that your database and application work correctly with the new version\. Implement any new tests needed to evaluate the impact of any compatibility issues you identified in step 1\. Test all stored procedures and functions\. Direct test versions of your applications to the upgraded DB instance\. 

1. If all tests pass, then perform the upgrade on your production DB instance\. We recommend that you do not allow write operations to the DB instance until you confirm that everything is working correctly\. 

## AWS Management Console<a name="USER_UpgradeDBInstance.SQLServer.Console"></a>

To upgrade a Microsoft SQL Server DB instance by using the AWS Management Console, you follow the same procedure as when you modify the DB instance\. For detailed instructions, see [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)\.

## CLI<a name="USER_UpgradeDBInstance.SQLServer.CLI"></a>

To upgrade a Microsoft SQL Server DB instance by using the AWS CLI, call the [modify\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the following parameters: 
+ `--db-instance-identifier` – the name of the db instance\. 
+ `--engine-version` – the version number of the database engine to upgrade to\. 
+ `--allow-major-version-upgrade` – to upgrade major version\. 
+ `--no-apply-immediately` – apply changes during the next maintenance window\. To apply changes immediately, use `--apply-immediately`\. For more information, see [The Impact of Apply Immediately](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\. 

You might also need to include the following parameters\. For more information, see [Option Group Considerations](#USER_UpgradeDBInstance.SQLServer.OGPG.OG) and [Parameter Group Considerations](#USER_UpgradeDBInstance.SQLServer.OGPG.PG)\. 
+ `--option-group-name` – the option group for the upgraded db instance\. 
+ `--db-parameter-group-name` – the parameter group for the upgraded db instance\. 

**Example**  
The following code upgrades a DB instance\. These changes are applied during the next maintenance window\.   
For Linux, OS X, or Unix:  

```
1. aws rds modify-db-instance \
2.     --db-instance-identifier <mydbinstance> \
3.     --engine-version <11.00.6020.0.v1> \
4.     --option-group-name <default:sqlserver-se-11-00> \
5.     --db-parameter-group-name <default.sqlserver-se-11.0> \
6.     --allow-major-version-upgrade \
7.     --no-apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier <mydbinstance> ^
    --engine-version <11.00.6020.0.v1> ^
    --option-group-name <default:sqlserver-se-11-00> ^
    --db-parameter-group-name <default.sqlserver-se-11.0> ^
    --allow-major-version-upgrade ^
    --no-apply-immediately
```

## API<a name="USER_UpgradeDBInstance.SQLServer.API"></a>

To upgrade a Microsoft SQL Server DB instance by using the Amazon RDS API, call the [ModifyDBInstance](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) action with the following parameters: 
+ `DBInstanceIdentifier` – the name of the db instance\. 
+ `EngineVersion` – the version number of the database engine to upgrade to\. 
+ `AllowMajorVersionUpgrade` – set to `true` to upgrade major version\. 
+ `ApplyImmediately` – whether to apply changes immediately or during the next maintenance window\. To apply changes immediately, set the value to `true`\. To apply changes during the next maintenance window, set the value to `false`\. For more information, see [The Impact of Apply Immediately](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\. 

You might also need to include the following parameters\. For more information, see [Option Group Considerations](#USER_UpgradeDBInstance.SQLServer.OGPG.OG) and [Parameter Group Considerations](#USER_UpgradeDBInstance.SQLServer.OGPG.PG)\. 
+ `OptionGroupName` – the option group for the upgraded db instance\. 
+ `DBParameterGroupName` – the parameter group for the upgraded db instance\. 

**Example**  
The following code upgrades a DB instance\. These changes are applied during the next maintenance window\.   

```
 1. https://rds.amazonaws.com/
 2.     ?Action=ModifyDBInstance
 3.     &AllowMajorVersionUpgrade=true
 4.     &ApplyImmediately=false
 5.     &DBInstanceIdentifier=mydbinstance
 6.     &DBParameterGroupName=default.sqlserver-se-11.0
 7.     &EngineVersion=11.00.6020.0.v1
 8.     &OptionGroupName=default:sqlserver-se-11-00
 9.     &SignatureMethod=HmacSHA256
10.     &SignatureVersion=4
11.     &Version=2014-10-31
12.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
13.     &X-Amz-Credential=AKIADQKE4SARGYLE/20131016/us-west-1/rds/aws4_request
14.     &X-Amz-Date=20131016T233051Z
15.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
16.     &X-Amz-Signature=087a8eb41cb1ab5f99e81575f23e73757ffc6a1e42d7d2b30b9cc0be988cff97
```

## Related Topics<a name="USER_UpgradeDBInstance.SQLServer.Related"></a>
+ [Maintaining an Amazon RDS DB Instance](USER_UpgradeDBInstance.Maintenance.md)
+ [Applying Updates for a DB Instance or DB Cluster](USER_UpgradeDBInstance.Maintenance.md#USER_UpgradeDBInstance.OSUpgrades)
+ [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)