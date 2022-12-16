# Upgrading a DB instance engine version<a name="USER_UpgradeDBInstance.Upgrading"></a>

Amazon RDS provides newer versions of each supported database engine so you can keep your DB instance up\-to\-date\. Newer versions can include bug fixes, security enhancements, and other improvements for the database engine\. When Amazon RDS supports a new version of a database engine, you can choose how and when to upgrade your database DB instances\.

There are two kinds of upgrades: major version upgrades and minor version upgrades\. In general, a *major engine version upgrade* can introduce changes that are not compatible with existing applications\. In contrast, a *minor version upgrade* includes only changes that are backward\-compatible with existing applications\.

The version numbering sequence is specific to each database engine\. For example, RDS for MySQL 5\.7 and 8\.0 are major engine versions and upgrading from any 5\.7 version to any 8\.0 version is a major version upgrade\. RDS for MySQL version 5\.7\.22 and 5\.7\.23 are minor versions and upgrading from 5\.7\.22 to 5\.7\.23 is a minor version upgrade\.

**Important**  
You can't modify a DB instance when it is being upgraded\. During an upgrade, the DB instance status is `upgrading`\.

For more information about major and minor version upgrades for a specific DB engine, see the following documentation for your DB engine: 
+ [Upgrading the MariaDB DB engine](USER_UpgradeDBInstance.MariaDB.md)
+ [Upgrading the Microsoft SQL Server DB engine](USER_UpgradeDBInstance.SQLServer.md)
+ [Upgrading the MySQL DB engine](USER_UpgradeDBInstance.MySQL.md)
+ [Upgrading the RDS for Oracle DB engine](USER_UpgradeDBInstance.Oracle.md)
+ [Upgrading the PostgreSQL DB engine for Amazon RDS](USER_UpgradeDBInstance.PostgreSQL.md)

For major version upgrades, you must manually modify the DB engine version through the AWS Management Console, AWS CLI, or RDS API\. For minor version upgrades, you can manually modify the engine version, or you can choose to enable auto minor version upgrades\.

**Note**  
Database engine upgrades require downtime\. You can minimize the downtime required for DB instance upgrade by using a blue/green deployment\. For more information, see [Using Amazon RDS Blue/Green Deployments for database updates](blue-green-deployments.md)\.

**Topics**
+ [Manually upgrading the engine version](#USER_UpgradeDBInstance.Upgrading.Manual)
+ [Automatically upgrading the minor engine version](#USER_UpgradeDBInstance.Upgrading.AutoMinorVersionUpgrades)

## Manually upgrading the engine version<a name="USER_UpgradeDBInstance.Upgrading.Manual"></a>

To manually upgrade the engine version of a DB instance, you can use the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="USER_UpgradeDBInstance.Upgrading.Manual.Console"></a>

**To upgrade the engine version of a DB instance by using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to upgrade\. 

1. Choose **Modify**\. The **Modify DB Instance** page appears\.

1. For **DB engine version**, choose the new version\.

1. Choose **Continue** and check the summary of modifications\. 

1. To apply the changes immediately, choose **Apply immediately**\. Choosing this option can cause an outage in some cases\. For more information, see [Using the Apply Immediately setting](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\. 

1. On the confirmation page, review your changes\. If they are correct, choose **Modify DB Instance** to save your changes\. 

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to cancel your changes\. 

### AWS CLI<a name="USER_UpgradeDBInstance.Upgrading.Manual.CLI"></a>

To upgrade the engine version of a DB instance, use the CLI [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. Specify the following parameters: 
+ `--db-instance-identifier` – the name of the DB instance\. 
+ `--engine-version` – the version number of the database engine to upgrade to\. 

  For information about valid engine versions, use the AWS CLI [ describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) command\.
+ `--allow-major-version-upgrade` – to upgrade the major version\. 
+ `--no-apply-immediately` – to apply changes during the next maintenance window\. To apply changes immediately, use `--apply-immediately`\. 

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds modify-db-instance \
2.     --db-instance-identifier mydbinstance \
3.     --engine-version new_version \
4.     --allow-major-version-upgrade \
5.     --no-apply-immediately
```
For Windows:  

```
1. aws rds modify-db-instance ^
2.     --db-instance-identifier mydbinstance ^
3.     --engine-version new_version ^
4.     --allow-major-version-upgrade ^
5.     --no-apply-immediately
```

### RDS API<a name="USER_UpgradeDBInstance.Upgrading.Manual.API"></a>

To upgrade the engine version of a DB instance, use the [ ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) action\. Specify the following parameters: 
+ `DBInstanceIdentifier` – the name of the DB instance, for example *`mydbinstance`*\. 
+ `EngineVersion` – the version number of the database engine to upgrade to\. For information about valid engine versions, use the [ DescribeDBEngineVersions](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBEngineVersions.html) operation\.
+ `AllowMajorVersionUpgrade` – whether to allow a major version upgrade\. To do so, set the value to `true`\. 
+ `ApplyImmediately` – whether to apply changes immediately or during the next maintenance window\. To apply changes immediately, set the value to `true`\. To apply changes during the next maintenance window, set the value to `false`\. 

## Automatically upgrading the minor engine version<a name="USER_UpgradeDBInstance.Upgrading.AutoMinorVersionUpgrades"></a>

A *minor engine version* is an update to a DB engine version within a major engine version\. For example, a major engine version might be 9\.6 with the minor engine versions 9\.6\.11 and 9\.6\.12 within it\. 

If you want Amazon RDS to upgrade the DB engine version of a database automatically, you can enable auto minor version upgrades for the database\. 

When Amazon RDS designates a minor engine version as the preferred minor engine version, each database that meets both of the following conditions is upgraded to the minor engine version automatically:
+ The database is running a minor version of the DB engine that is lower than the preferred minor engine version\.
+ The database has auto minor version upgrade enabled\.

You can control whether auto minor version upgrade is enabled for a DB instance when you perform the following tasks:
+ [Creating a DB instance](USER_CreateDBInstance.md)
+ [Modifying a DB instance](Overview.DBInstance.Modifying.md)
+ [Creating a read replica](USER_ReadRepl.md#USER_ReadRepl.Create)
+ [Restoring a DB instance from a snapshot](USER_RestoreFromSnapshot.md)
+ [Restoring a DB instance to a specific time](USER_PIT.md)
+ [Importing a DB instance from Amazon S3](MySQL.Procedural.Importing.md) \(for a MySQL backup on Amazon S3\)

When you perform these tasks, you can control whether auto minor version upgrade is enabled for the DB instance in the following ways:
+ Using the console, set the **Auto minor version upgrade** option\.
+ Using the AWS CLI, set the `--auto-minor-version-upgrade|--no-auto-minor-version-upgrade` option\.
+ Using the RDS API, set the `AutoMinorVersionUpgrade` parameter\.

To determine whether a maintenance update, such as a DB engine version upgrade, is available for your DB instance, you can use the console, AWS CLI, or RDS API\. You can also upgrade the DB engine version manually and adjust the maintenance window\. For more information, see [Maintaining a DB instance](USER_UpgradeDBInstance.Maintenance.md)\.

You can use the following AWS CLI command to determine the current automatic minor upgrade target version for a specified minor DB engine version in a specific AWS Region\. You can find the possible `--engine` values for this command in the description for the `Engine` parameter in [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html)\.

For Linux, macOS, or Unix:

```
aws rds describe-db-engine-versions \
--engine engine \
--engine-version minor-version \
--region region \
--query "DBEngineVersions[*].ValidUpgradeTarget[*].{AutoUpgrade:AutoUpgrade,EngineVersion:EngineVersion}" \
--output text
```

For Windows:

```
aws rds describe-db-engine-versions ^
--engine engine ^
--engine-version minor-version ^
--region region ^
--query "DBEngineVersions[*].ValidUpgradeTarget[*].{AutoUpgrade:AutoUpgrade,EngineVersion:EngineVersion}" ^
--output text
```

For example, the following AWS CLI command determines the automatic minor upgrade target for MySQL minor version 8\.0\.11 in the US East \(Ohio\) AWS Region \(us\-east\-2\)\.

For Linux, macOS, or Unix:

```
aws rds describe-db-engine-versions \
--engine mysql \
--engine-version 8.0.11 \
--region us-east-2 \
--query "DBEngineVersions[*].ValidUpgradeTarget[*].{AutoUpgrade:AutoUpgrade,EngineVersion:EngineVersion}" \
--output table
```

For Windows:

```
aws rds describe-db-engine-versions ^
--engine mysql ^
--engine-version 8.0.11 ^
--region us-east-2 ^
--query "DBEngineVersions[*].ValidUpgradeTarget[*].{AutoUpgrade:AutoUpgrade,EngineVersion:EngineVersion}" ^
--output table
```

Your output is similar to the following\.

```
----------------------------------
|    DescribeDBEngineVersions    |
+--------------+-----------------+
|  AutoUpgrade |  EngineVersion  |
+--------------+-----------------+
|  False       |  8.0.15         |
|  False       |  8.0.16         |
|  False       |  8.0.17         |
|  False       |  8.0.19         |
|  False       |  8.0.20         |
|  False       |  8.0.21         |
|  True        |  8.0.23         |
|  False       |  8.0.25         |
+--------------+-----------------+
```

In this example, the `AutoUpgrade` value is `True` for MySQL version 8\.0\.23\. So, the automatic minor upgrade target is MySQL version 8\.0\.23, which is highlighted in the output\.

**Important**  
If you plan to migrate an RDS for PostgreSQL DB instance to an Aurora PostgreSQL DB cluster soon, we strongly recommend that you turn off auto minor version upgrades for the DB instance early during planning\. Migration to Aurora PostgreSQL might be delayed if the RDS for PostgreSQL version isn't yet supported by Aurora PostgreSQL\. For information about Aurora PostgreSQL versions, see [ Engine versions for Amazon Aurora PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraPostgreSQL.Updates.20180305.html)\.