# Upgrading the PostgreSQL DB engine for Amazon RDS<a name="USER_UpgradeDBInstance.PostgreSQL"></a>

There are two types of upgrades you can manage for your PostgreSQL DB instance:
+ Operating system updates – Occasionally, Amazon RDS might need to update the underlying operating system of your DB instance to apply security fixes or OS changes\. You can decide when Amazon RDS applies OS updates by using the RDS console, AWS Command Line Interface \(AWS CLI\), or RDS API\. For more information about OS updates, see [Applying updates for a DB instance](USER_UpgradeDBInstance.Maintenance.md#USER_UpgradeDBInstance.OSUpgrades)\.
+  Database engine upgrades – When Amazon RDS supports a new version of a database engine, you can upgrade your DB instances to the new version\. 

When Amazon RDS supports a new version of a database engine, you can upgrade your DB instances to the new version\. There are two kinds of upgrades for PostgreSQL DB instances: major version upgrades and minor version upgrades\. 

**Major version upgrades**  
*Major version upgrades* can contain database changes that are not backward\-compatible with existing applications\. As a result, you must manually perform major version upgrades of your DB instances\. You can initiate a major version upgrade by modifying your DB instance\. However, before you perform a major version upgrade, we recommend that you follow the steps described in [ Choosing a major version upgrade for PostgreSQL ](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)\. During a major version upgrade, Amazon RDS also upgrades all of your in\-Region read replicas along with the primary DB instance\.

**Minor version upgrades**  
In contrast, *minor version upgrades* include only changes that are backward\-compatible with existing applications\. You can initiate a minor version upgrade manually by modifying your DB instance\. Or you can enable the **Auto minor version upgrade** option when creating or modifying a DB instance\. Doing so means that your DB instance is automatically upgraded after Amazon RDS tests and approves the new version\. If your PostgreSQL DB instance is using read replicas, you must first upgrade all of the read replicas before upgrading the primary instance\. If your DB instance is in a Multi\-AZ deployment, then the writer and any standby replicas are upgraded simultaneously\. Therefore, your DB instance might not be available until the upgrade is complete\. For more details, see [Automatic minor version upgrades for PostgreSQL](#USER_UpgradeDBInstance.PostgreSQL.Minor)\. For information about manually performing a minor version upgrade, see [Manually upgrading the engine version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual)\.

For more information about database engine versions, and the policy for deprecating database engine versions, see [Database Engine Versions in the Amazon RDS FAQs](http://aws.amazon.com/rds/faqs/#Database_Engine_Versions)\.

**Topics**
+ [Overview of upgrading PostgreSQL](#USER_UpgradeDBInstance.PostgreSQL.Overview)
+ [PostgreSQL version numbers](#USER_UpgradeDBInstance.PostgreSQL.VersionID)
+ [Choosing a major version upgrade for PostgreSQL](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)
+ [How to perform a major version upgrade](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Process)
+ [Automatic minor version upgrades for PostgreSQL](#USER_UpgradeDBInstance.PostgreSQL.Minor)
+ [Upgrading PostgreSQL extensions](#USER_UpgradeDBInstance.PostgreSQL.ExtensionUpgrades)

## Overview of upgrading PostgreSQL<a name="USER_UpgradeDBInstance.PostgreSQL.Overview"></a>

To safely upgrade your DB instances, Amazon RDS uses the `pg_upgrade` utility described in the [PostgreSQL documentation](https://www.postgresql.org/docs/current/pgupgrade.html)\. 

When you use the AWS Management Console to upgrade a DB instance, it shows the valid upgrade targets for the DB instance\. You can also use the following AWS CLI command to identify the valid upgrade targets for a DB instance:

For Linux, macOS, or Unix:

```
aws rds describe-db-engine-versions \
  --engine postgres \
  --engine-version version-number \
  --query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" --output text
```

For Windows:

```
aws rds describe-db-engine-versions ^
  --engine postgres ^
  --engine-version version-number ^
  --query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" --output text
```

For example, to identify the valid upgrade targets for a PostgreSQL version 10\.11 DB instance, run the following AWS CLI command:

For Linux, macOS, or Unix:

```
aws rds describe-db-engine-versions \
  --engine postgres \
  --engine-version 10.11 \
  --query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" --output text
```

For Windows:

```
aws rds describe-db-engine-versions ^
  --engine postgres ^
  --engine-version 10.11 ^
  --query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" --output text
```

If your backup retention period is greater than 0, Amazon RDS takes two DB snapshots during the upgrade process\. The first DB snapshot is of the DB instance before any upgrade changes have been made\. If the upgrade doesn't work for your databases, you can restore this snapshot to create a DB instance running the old version\. The second DB snapshot is taken after the upgrade completes\. 

**Note**  
Amazon RDS takes DB snapshots during the upgrade process only if you have set the backup retention period for your DB instance to a number greater than 0\. To change your backup retention period, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

When you perform a major version upgrade of the primary DB instance, all the in\-Region read replicas are also automatically upgraded\. After the upgrade workflow starts, the read replicas wait for the `pg_upgrade` to complete successfully on the primary DB instance\. Then the primary DB instance upgrade waits for the read replica upgrades to complete\. You experience an outage until the upgrade is complete\.

If your DB instance is in a Multi\-AZ deployment, both the primary writer DB instance and standby DB instances are upgraded\. The writer and standby DB instances are upgraded at the same time\. 

After an upgrade is complete, you can't revert to the previous version of the database engine\. If you want to return to the previous version, restore the DB snapshot that was taken before the upgrade to create a new DB instance\. 

## PostgreSQL version numbers<a name="USER_UpgradeDBInstance.PostgreSQL.VersionID"></a>

The version numbering sequence for the PostgreSQL database engine is as follows: 
+ For PostgreSQL versions 10 and later, the engine version number is in the form *major\.minor*\. The major version number is the integer part of the version number\. The minor version number is the fractional part of the version number\. 

  A major version upgrade increases the integer part of the version number, such as upgrading from 10\.*minor* to 11\.*minor*\.
+ For PostgreSQL versions earlier than 10, the engine version number is in the form *major\.major\.minor*\. The major engine version number is both the integer and the first fractional part of the version number\. For example, 9\.6 is a major version\. The minor version number is the third part of the version number\. For example, for version 9\.6\.12, the 12 is the minor version number\.

  A major version upgrade increases the major part of the version number\. For example, an upgrade from *9\.6*\.12 to *10*\.11 is a major version upgrade, where *9\.6* and *10* are the major version numbers\.

## Choosing a major version upgrade for PostgreSQL<a name="USER_UpgradeDBInstance.PostgreSQL.MajorVersion"></a>

Major version upgrades can contain changes that are not backward\-compatible with previous versions of the database\. New functionality can cause your existing applications to stop working correctly\. For this reason, Amazon RDS doesn't apply major version upgrades automatically\. To perform a major version upgrade, you modify your DB instance manually\. Make sure that you thoroughly test any upgrade to verify that your applications work correctly before applying the upgrade to your production DB instances\. When you do a PostgreSQL major version upgrade, we recommend that you follow the steps described in [How to perform a major version upgrade](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Process)\. 

When you upgrade a PostgreSQL DB instance to its next major version, any read replicas associated with the DB instance are also upgraded to that next major version\. In some cases, you can skip to a higher major version when upgrading\. If your upgrade skips a major version, the read replicas are also upgraded to that target major version\. Upgrades to version 11 that skip other major versions have certain limitations\. You can find the details in the steps described in [How to perform a major version upgrade](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Process)\.

Most PostgreSQL extensions aren't upgraded during a PostgreSQL engine upgrade\. These must be upgraded separately\. For more information, see [Upgrading PostgreSQL extensions](#USER_UpgradeDBInstance.PostgreSQL.ExtensionUpgrades)\.

You can find out which major versions are available for your RDS for PostgreSQL DB instance by running the following AWS CLI query:

```
aws rds describe-db-engine-versions --engine postgres  --engine-version your-version --query "DBEngineVersions[*].ValidUpgradeTarget[*].{EngineVersion:EngineVersion}" --output text
```

The following table summarizes the results of this query for all available versions\. An asterisk \(\*\) on the version number means that version is deprecated\. If your current version is deprecated, we recommend that you upgrade to the newest minor version upgrade target or to one of the other available upgrade targets for that version\. For more information about RDS for PostgreSQL 9\.6 deprecation, see [Deprecation of PostgreSQL version 10Deprecation of PostgreSQL version 9\.6](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.DBVersions.Deprecation96)\. 


| Current source version \(\*deprecated\) | Newest minor version upgrade target | Newest major version upgrade target | Other available upgrade targets | 
| --- | --- | --- | --- | 
| 14\.5 |  | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version146) |  |  |  |  |  |  |  |  |  |  |  |  | 
| 14\.4 |  | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version146) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version145) |  |  |  |  |  |  |  |  |  |  |  | 
| 14\.3 |  | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version146) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version145) | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version144) |  |  |  |  |  |  |  |  |  |  | 
| 14\.2 |  | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version146) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version145) | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version144) | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version143) |  |  |  |  |  |  |  |  |  | 
| 14\.1 |  | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version146) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version145) | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version144) | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version143) | [14\.2](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version142) |  |  |  |  |  |  |  |  | 
| 13\.9 |  | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version146) |  |  |  |  |  |  |  |  |  |  |  |  | 
| 13\.8 |  | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version146) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version145) |  |  |  |  |  |  |  |  |  |  |  | 
| 13\.7 |  | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version146) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version145) | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version144) | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version143) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version139) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version138) |  |  |  |  |  |  |  | 
| 13\.6 |  | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version146) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version145) | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version144) | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version143) | [14\.2](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version142) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version139) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version138) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version137) |  |  |  |  |  | 
| 13\.5 |  | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version146) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version145) | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version144) | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version143) | [14\.2](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version142) | [14\.1](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version141) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version139) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version138) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version137) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version136) |  |  |  | 
| 13\.4 |  | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version146) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version145) | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version144) | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version143) | [14\.2](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version142) | [14\.1](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version141) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version139) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version138) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version137) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version136) | [13\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version135) |  |  | 
| 13\.3 |  | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version146) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version145) | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version144) | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version143) | [14\.2](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version142) | [14\.1](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version141) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version139) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version138) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version137) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version136) | [13\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version135) | [13\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version134) |  | 
| 13\.2\*, 13\.1\* | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version138) | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version146) | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version145) | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version144) | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version143) | [14\.2](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version142) | [14\.1](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version141) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version139) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version138) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version137) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version136) | [13\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version135) | [13\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version134) |  | 
| 12\.13 |  | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version145) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version137) |  |  |  |  |  |  |  |  |  |  |  | 
| 12\.12 |  | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version145) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version139) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version138) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1213) |  |  |  |  |  |  |  |  |  | 
| 12\.11 |  | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version144) | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version143) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version139) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version138) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version137) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1213) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1212) |  |  |  |  |  |  | 
| 12\.10 |  | [14\.2](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version142) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version139) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version138) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version137) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version136) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1213) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1212) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1211) |  |  |  |  |  | 
| 12\.9 |  | [14\.1](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version141) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version139) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version138) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version137) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version136) | [13\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version135) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1213) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1212) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1211) | [12\.10](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1210) |  |  |  | 
| 12\.8 |  | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version139) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version138) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version137) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version136) | [13\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version135) | [13\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version134) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1213) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1212) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1211) | [12\.10](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1210) | [12\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version129) |  |  | 
| 12\.7 |  | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version139) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version138) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version137) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version136) | [13\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version135) | [13\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version134) | [13\.3](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version133) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1213) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1212) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1211) | [12\.10](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1210) | [12\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version129) |  | 
| 12\.6\*, 12\.5\*, 12\.4\*, 12\.3\*, 12\.2\*, 12\.1\* | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1212) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version137) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version136) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1213) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1212) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1211) | [12\.10](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1210) | [12\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version129) | [12\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version128) | [12\.7](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version127) |  |  |  |  | 
| 11\.18 |  | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version146) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version139) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1213) |  |  |  |  |  |  |  |  |  |  | 
| 11\.17 |  | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version145) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version138) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1213) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1212) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1118) |  |  |  |  |  |  | 
| 11\.16 |  | [14\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version144) | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version143) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version137) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1213) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1212) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1211) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1118) | [11\.17](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1117) |  |  |  |  |  | 
| 11\.15 |  | [14\.2](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version142) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version136) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1213) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1212) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1211) | [12\.10](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1210) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1118) | [11\.17](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1117) | [11\.16](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1116) |  |  |  |  | 
| 11\.14 |  | [14\.1](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version141) | [13\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version135) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1213) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1212) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1211) | [12\.10](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1210) | [12\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version129) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1118) | [11\.17](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1117) | [11\.16](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1116) | [11\.15](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1115) |  |  | 
| 11\.13 |  | [13\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version134) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1213) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1212) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1211) | [12\.10](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1210) | [12\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version129) | [12\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version128) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1118) | [11\.17](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1117) | [11\.16](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1116) | [11\.15](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1115) | [11\.14](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1114) |  | 
| 11\.12 |  | [13\.3](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version133) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1213) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1212) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1211) | [12\.10](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1210) | [12\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version129) | [12\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version128) | [12\.7](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version127) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1118) | [11\.17](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1117) | [11\.16](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1116) | [11\.15](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1115) | [11\.14](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1114) | 
| 10\.23 |  | [14\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version146) | [13\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version139) | [12\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1213) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1118) |  |  |  |  |  |  |  |  |  | 
| 10\.22 |  | [14\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version145) | [13\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version138) | [12\.12](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1212) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1118) | [11\.17](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1117) | [10\.23](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1023) |  |  |  |  |  |  |  | 
| 10\.21 |  | [14\.3](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version143) | [13\.7](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version137) | [12\.11](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1211) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1118) | [11\.17](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1117) | [11\.16](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1116) | [10\.23](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1023) | [10\.22](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1022) |  |  |  |  |  | 
| 10\.20 |  | [14\.2](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version142) | [13\.6](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version136) | [12\.10](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1210) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1118) | [11\.17](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1117) | [11\.16](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1116) | [11\.15](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1115) | [10\.23](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1023) | [10\.22](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1022) | [10\.21](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1021) |  |  |  | 
| 10\.19 |  | [14\.1](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version141) | [13\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version135) | [12\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version129) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1118) | [11\.17](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1117) | [11\.16](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1116) | [11\.15](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1115) | [11\.14](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1114) | [10\.23](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1023) | [10\.22](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1022) | [10\.21](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1021) | [10\.20](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1020) |  | 
| 10\.18 |  | [13\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version134) | [12\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version128) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1118) | [11\.17](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1117) | [11\.16](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1116) | [11\.15](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1115) | [11\.14](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1114) | [11\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1113) | [10\.23](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1023) | [10\.22](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1022) | [10\.21](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1021) | [10\.20](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1020) | [10\.19](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1019) | 
| 10\.17 |  | [13\.3](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version133) | [12\.7](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version127) | [11\.18](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1118) | [11\.17](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1117) | [11\.16](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1116) | [11\.15](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1115) | [11\.14](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1114) | [11\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1113) | [11\.12](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1112) | [10\.23](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1023) | [10\.22](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1022) | [10\.21](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1021) | [10\.20](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1020) | 
| 9\.6\.24 |  | [14\.1](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version141) | [13\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version135) | [12\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version129) | [11\.14](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1114) | [10\.20](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1019) | [10\.19](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1019) |  |  |  |  |  |  |  | 
| 9\.6\.23 |  | [13\.4](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version134) | [12\.8](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version128) | [11\.13](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1113) | [10\.20](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1020) | [10\.19](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1019) | [10\.18](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1018) | [9\.6\.24](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version9624) |  |  |  |  |  |  | 
| 9\.6\.22 |  | [13\.3](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version133) | [12\.7](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version127) | [11\.12](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1112) | [10\.20](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1020) | [10\.19](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1019) | [10\.18](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1018) | [10\.17](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1017) | [9\.6\.24](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version9624) | [9\.6\.23](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version9623) |  |  |  |  | 
| 9\.6\.19\*, 9\.6\.18\*, 9\.6\.17\*, 9\.6\.16\*, 9\.6\.15\*, 9\.6\.14\*, 9\.6\.12\*, 9\.6\.11\*9\.6\.10\*, 9\.6\.9\*, 9\.6\.8\*, 9\.6\.6\*, 9\.6\.5\*, 9\.6\.3\*, 9\.6\.2\*, 9\.6\.1\* | [9\.6\.24](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version9624) | [14\.1](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version141) | [13\.5](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version135) | [12\.9](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version129) | [11\.14](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1114) | [10\.20](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1019) | [10\.19](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version1019) | [9\.6\.23](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version9623) | [9\.6\.22](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html#postgresql-versions-version9622) |  |  |  |  |  | 

## How to perform a major version upgrade<a name="USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Process"></a>

We recommend the following process when upgrading an Amazon RDS PostgreSQL DB instance:

1. **Have a version\-compatible parameter group ready** – If you are using a custom parameter group, you have two options\. You can specify a default parameter group for the new DB engine version\. Or you can create your own custom parameter group for the new DB engine version\. For more information, see [Working with parameter groups](USER_WorkingWithParamGroups.md)

1. **Check for unsupported DB instance classes** – Check that your database's instance class is compatible with the PostgreSQL version you are upgrading to\. For more information, see [Supported DB engines for DB instance classes](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Support)\.

1. **Check for unsupported usage:**
   + **Prepared transactions** – Commit or roll back all open prepared transactions before attempting an upgrade\. 

     You can use the following query to verify that there are no open prepared transactions on your instance\. 

     ```
     SELECT count(*) FROM pg_catalog.pg_prepared_xacts;
     ```
   + **Reg\* data types** – Remove all uses of the *reg\** data types before attempting an upgrade\. Except for `regtype` and `regclass`, you can't upgrade the *reg\** data types\. The pg\_upgrade utility can't persist this data type, which is used by Amazon RDS to do the upgrade\. 

     To verify that there are no uses of unsupported *reg\** data types, use the following query for each database\. 

     ```
     SELECT count(*) FROM pg_catalog.pg_class c, pg_catalog.pg_namespace n, pg_catalog.pg_attribute a
       WHERE c.oid = a.attrelid
           AND NOT a.attisdropped
           AND a.atttypid IN ('pg_catalog.regproc'::pg_catalog.regtype,
                              'pg_catalog.regprocedure'::pg_catalog.regtype,
                              'pg_catalog.regoper'::pg_catalog.regtype,
                              'pg_catalog.regoperator'::pg_catalog.regtype,
                              'pg_catalog.regconfig'::pg_catalog.regtype,
                              'pg_catalog.regdictionary'::pg_catalog.regtype)
           AND c.relnamespace = n.oid
           AND n.nspname NOT IN ('pg_catalog', 'information_schema');
     ```

   

1. **Handle logical replication slots** – An upgrade can't occur if the instance has any logical replication slots\. Logical replication slots are typically used for AWS DMS migration and for replicating tables from the database to data lakes, BI tools, and other targets\. Before upgrading, make sure that you know the purpose of any logical replication slots that are in use, and confirm that it's okay to delete them\. If the logical replication slots are still being used, you shouldn't delete them, and you can't proceed with the upgrade\. 

   If the logical replication slots aren't needed, you can delete them using the following SQL:

   ```
   SELECT * FROM pg_replication_slots;
   SELECT pg_drop_replication_slot(slot_name);
   ```

   Logical replication setups that use the `pglogical` extension also need to have slots dropped for a successful major version upgrade\. For information about how to identify and drop slots created using the `pglogical` extension, see [Managing logical replication slots for RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Extensions.md#Appendix.PostgreSQL.CommonDBATasks.pglogical.handle-slots)\.

1. **Handle read replicas** – An upgrade also upgrades the in\-Region read replicas along with the primary DB instance\.

   You can't upgrade read replicas separately\. If you could, it could lead to situations where the primary and replica DB instances have different PostgreSQL major versions\. However, read replica upgrades might increase downtime on the primary DB instance\. To prevent a read replica upgrade, promote the replica to a standalone instance or delete it before starting the upgrade process\.

   The upgrade process recreates the read replica's parameter group based on the read replica's current parameter group\. You can apply a custom parameter group to a read replica only after the upgrade completes by modifying the read replica\. For more information about read replicas, see [Working with read replicas for Amazon RDS for PostgreSQL](USER_PostgreSQL.Replication.ReadReplicas.md)\.

1. **Perform a backup** – We recommend that you perform a backup before performing the major version upgrade so that you have a known restore point for your database\. If your backup retention period is greater than 0, the upgrade process creates DB snapshots of your DB instance before and after upgrading\. To change your backup retention period, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. To perform a backup manually, see [Creating a DB snapshot](USER_CreateSnapshot.md)\.

1. **Upgrade certain extensions before a major version upgrade** – If you plan to skip a major version with the upgrade, you need to update certain extensions *before* performing the major version upgrade\. For example, upgrading from versions 9\.5\.x or 9\.6\.x to version 11\.x skips a major version\. The extensions to update include PostGIS and related extensions for processing spatial data\. 
   + `address_standardizer`
   + `address_standardizer_data_us`
   + `postgis`
   + `postgis_tiger_geocoder`
   + `postgis_topology`

   Run the following command for each extension that you're using:

   ```
   ALTER EXTENSION PostgreSQL-extension UPDATE TO 'new-version'
   ```

   For more information, see [Upgrading PostgreSQL extensions](#USER_UpgradeDBInstance.PostgreSQL.ExtensionUpgrades)\. To learn more about upgrading PostGIS, see [Step 6: Upgrade the PostGIS extension](Appendix.PostgreSQL.CommonDBATasks.PostGIS.md#Appendix.PostgreSQL.CommonDBATasks.PostGIS.Update)\.

1. **Drop certain extensions before the major version upgrade** – An upgrade that skips a major version to version 11\.x doesn't support updating the `pgRouting` extension\. Upgrading from versions 9\.4\.x, 9\.5\.x, or 9\.6\.x to versions 11\.x skip a major version\. It's safe to drop the `pgRouting` extension and then reinstall it to a compatible version after the upgrade\. For the extension versions you can update to, see [Supported PostgreSQL extension versions](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.FeatureSupport.Extensions)\.

   The `tsearch2` and `chkpass` extensions are no longer supported for PostgreSQL versions 11 or later\. If you are upgrading to version 11\.x, drop the `tsearch2`, and `chkpass` extensions before the upgrade\.

1. **Drop unknown data types** – Drop `unknown` data types depending on the target version\.

   PostgreSQL version 10 stopped supporting the `unknown` data type\. If a version 9\.6 database uses the `unknown` data type, an upgrade to a version 10 shows an error message such as the following: 

   ```
   Database instance is in a state that cannot be upgraded: PreUpgrade checks failed:
   The instance could not be upgraded because the 'unknown' data type is used in user tables.
   Please remove all usages of the 'unknown' data type and try again."
   ```

   To find the `unknown` data type in your database so you can remove the offending column or change it to a supported data type, use the following SQL:

   ```
   SELECT DISTINCT data_type FROM information_schema.columns WHERE data_type ILIKE 'unknown';
   ```

1. **Perform an upgrade dry run** – We highly recommend testing a major version upgrade on a duplicate of your production database before attempting the upgrade on your production database\. To create a duplicate test instance, you can either restore your database from a recent snapshot or do a point\-in\-time restore of your database to its latest restorable time\. For more information, see [Restoring from a snapshot](USER_RestoreFromSnapshot.md#USER_RestoreFromSnapshot.Restoring) or [Restoring a DB instance to a specified time](USER_PIT.md)\. For details on performing the upgrade, see [Manually upgrading the engine version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual)\. 

   In upgrading a version 9\.6 DB instance to version 10, be aware that PostgreSQL 10 enables parallel queries by default\. You can test the impact of parallelism *before* the upgrade by changing the `max_parallel_workers_per_gather` parameter on your test DB instance to 2\. 
**Note**  
 The default value for `max_parallel_workers_per_gather` parameter in the `default.postgresql10` DB parameter group is 2\. 

   For more information, see [Parallel Query](https://www.postgresql.org/docs/10/parallel-query.html) in the PostgreSQL documentation\. To disable parallelism on version 10, set the `max_parallel_workers_per_gather` parameter to 0\. 

   During the major version upgrade, the `public` and `template1` databases and the `public` schema in every database on the instance are temporarily renamed\. These objects appear in the logs with their original name and a random string appended\. The string is appended so that custom settings such as `locale` and `owner` are preserved during the major version upgrade\. After the upgrade completes, the objects are renamed back to their original names\. 
**Note**  
During the major version upgrade process, you can't do a point\-in\-time restore of your instance\. After Amazon RDS performs the upgrade, it takes an automatic backup of the instance\. You can perform a point\-in\-time restore to times before the upgrade began and after the automatic backup of your instance has completed\. 

1. **If an upgrade fails with precheck procedure errors, resolve the issues** – During the major version upgrade process, Amazon RDS for PostgreSQL first runs a precheck procedure to identify any issues that might cause the upgrade to fail\. The precheck procedure checks all potential incompatible conditions across all databases in the instance\. 

   If the precheck encounters an issue, it creates a log event indicating the upgrade precheck failed\. The precheck process details are in an upgrade log named `pg_upgrade_precheck.log` for all the databases of a DB instance\. Amazon RDS appends a timestamp to the file name\. For more information about viewing logs, see [Monitoring Amazon RDS log files](USER_LogAccess.md)\.

   If a read replica upgrade fails at precheck, replication on the failed read replica is broken and the read replica is put in the terminated state\. Delete the read replica and recreate a new read replica based on the upgraded primary DB instance\.

   Resolve all of the issues identified in the precheck log and then retry the major version upgrade\. The following is an example of a precheck log\.

   ```
   ------------------------------------------------------------------------
   Upgrade could not be run on Wed Apr 4 18:30:52 2018
   -------------------------------------------------------------------------
   The instance could not be upgraded from 9.6.11 to 10.6 for the following reasons.
   Please take appropriate action on databases that have usage incompatible with the requested major engine version upgrade and try the upgrade again.
   
   * There are uncommitted prepared transactions. Please commit or rollback all prepared transactions.* One or more role names start with 'pg_'. Rename all role names that start with 'pg_'.
   
   * The following issues in the database 'my"million$"db' need to be corrected before upgrading:** The ["line","reg*"] data types are used in user tables. Remove all usage of these data types.
   ** The database name contains characters that are not supported by RDS for PostgreSQL. Rename the database.
   ** The database has extensions installed that are not supported on the target database version. Drop the following extensions from your database: ["tsearch2"].
   
   * The following issues in the database 'mydb' need to be corrected before upgrading:** The database has views or materialized views that depend on 'pg_stat_activity'. Drop the views.
   ```

1. **If a read replica upgrade fails while upgrading the database, resolve the issue** – A failed read replica is placed in the `incompatible-restore` state and replication is terminated on the DB instance\. Delete the read replica and recreate a new read replica based on the upgraded primary DB instance\.

   A read replica upgrade might fail for the following reasons:
   + It was unable to catch up with the primary DB instance even after a wait time\.
   + It was in a terminal or incompatible lifecycle state such as storage\-full, incompatible\-restore, and so on\.
   + When the primary DB instance upgrade started, there was a separate minor version upgrade running on the read replica\.
   + The read replica used incompatible parameters\.
   + The read replica was unable to communicate with the primary DB instance to synchronize the data folder\.

1. **Upgrade your production instance** – When the dry\-run major version upgrade is successful, you should be able to upgrade your production database with confidence\. For more information, see [Manually upgrading the engine version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual)\. 

1. Run the `ANALYZE` operation to refresh the `pg_statistic` table\. You should do this for every database on all your PostgreSQL DB instances\. Optimizer statistics aren't transferred during a major version upgrade, so you need to regenerate all statistics to avoid performance issues\. Run the command without any parameters to generate statistics for all regular tables in the current database, as follows:

   ```
   ANALYZE VERBOSE
   ```

   The `VERBOSE` flag is optional, but using it shows you the progress\. For more information, see [ANALYZE](https://www.postgresql.org/docs/10/sql-analyze.html) in the PostgreSQL documentation\. 
**Note**  
Run ANALYZE on your system after the upgrade to avoid performance issues\.

After the major version upgrade is complete, we recommend the following:
+ A PostgreSQL upgrade doesn't upgrade any PostgreSQL extensions\. To upgrade extensions, see [Upgrading PostgreSQL extensions](#USER_UpgradeDBInstance.PostgreSQL.ExtensionUpgrades)\. 
+ Optionally, use Amazon RDS to view two logs that the pg\_upgrade utility produces\. These are `pg_upgrade_internal.log` and `pg_upgrade_server.log`\. Amazon RDS appends a timestamp to the file name for these logs\. You can view these logs as you can any other log\. For more information, see [Monitoring Amazon RDS log files](USER_LogAccess.md)\.

  You can also upload the upgrade logs to Amazon CloudWatch Logs\. For more information, see [Publishing PostgreSQL logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.PostgreSQL.md#USER_LogAccess.Concepts.PostgreSQL.PublishtoCloudWatchLogs)\.
+ To verify that everything works as expected, test your application on the upgraded database with a similar workload\. After the upgrade is verified, you can delete this test instance\.

## Automatic minor version upgrades for PostgreSQL<a name="USER_UpgradeDBInstance.PostgreSQL.Minor"></a>

If you enable the **Auto minor version upgrade** option when creating or modifying a DB instance, you can have your DB instance automatically upgraded\.

For each RDS for PostgreSQL major version, one minor version is designated by RDS as the automatic upgrade version\. After a minor version has been tested and approved by Amazon RDS, the minor version upgrade occurs automatically during your maintenance window\. RDS doesn't automatically set newer released minor versions as the automatic upgrade version\. Before RDS designates a newer automatic upgrade version, several criteria are considered, such as the following:
+ Known security issues
+ Bugs in the PostgreSQL community version
+ Overall fleet stability since the minor version was released

You can use the following AWS CLI command to determine the current automatic minor upgrade target version for a specified PostgreSQL minor version in a specific AWS Region\. 

For Linux, macOS, or Unix:

```
aws rds describe-db-engine-versions \
--engine postgres \
--engine-version minor-version \
--region region \
--query "DBEngineVersions[*].ValidUpgradeTarget[*].{AutoUpgrade:AutoUpgrade,EngineVersion:EngineVersion}" \
--output text
```

For Windows:

```
aws rds describe-db-engine-versions ^
--engine postgres ^
--engine-version minor-version ^
--region region ^
--query "DBEngineVersions[*].ValidUpgradeTarget[*].{AutoUpgrade:AutoUpgrade,EngineVersion:EngineVersion}" ^
--output text
```

For example, the following AWS CLI command determines the automatic minor upgrade target for PostgreSQL minor version 10\.11 in the US East \(Ohio\) AWS Region \(us\-east\-2\)\.

For Linux, macOS, or Unix:

```
aws rds describe-db-engine-versions \
--engine postgres \
--engine-version 10.11 \
--region us-east-2 \
--query "DBEngineVersions[*].ValidUpgradeTarget[*].{AutoUpgrade:AutoUpgrade,EngineVersion:EngineVersion}" \
--output table
```

For Windows:

```
aws rds describe-db-engine-versions ^
--engine postgres ^
--engine-version 10.11 ^
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
|  False       |  10.12          |
|  False       |  10.13          |
|  False       |  10.14          |
|  False       |  10.15          |
|  False       |  10.16          |
|  True        |  10.17          |
|  False       |  10.18          |
|  False       |  11.6           |
|  False       |  11.7           |
|  False       |  11.8           |
|  False       |  11.9           |
|  False       |  11.10          |
|  False       |  11.11          |
|  False       |  11.12          |
|  False       |  11.13          |
+--------------+-----------------+
```

In this example, the `AutoUpgrade` value is `True` for PostgreSQL version 10\.17\. So, the automatic minor upgrade target is PostgreSQL version 10\.17, which is highlighted in the output\.

A PostgreSQL DB instance is automatically upgraded during your maintenance window if the following criteria are met:
+ The DB instance has the **Auto minor version upgrade** option enabled\.
+ The DB instance is running a minor DB engine version that is less than the current automatic upgrade minor version\.

For more information, see [Automatically upgrading the minor engine version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.AutoMinorVersionUpgrades)\. 

**Note**  
A PostgreSQL upgrade doesn't upgrade PostgreSQL extensions\. To upgrade extensions, see [Upgrading PostgreSQL extensions](#USER_UpgradeDBInstance.PostgreSQL.ExtensionUpgrades)\. 

## Upgrading PostgreSQL extensions<a name="USER_UpgradeDBInstance.PostgreSQL.ExtensionUpgrades"></a>

A PostgreSQL engine upgrade doesn't upgrade most PostgreSQL extensions\. To update an extension after a version upgrade, use the `ALTER EXTENSION UPDATE` command\. 

**Note**  
For information about updating the PostGIS extension, see [Managing spatial data with the PostGIS extension](Appendix.PostgreSQL.CommonDBATasks.PostGIS.md) \([Step 6: Upgrade the PostGIS extension](Appendix.PostgreSQL.CommonDBATasks.PostGIS.md#Appendix.PostgreSQL.CommonDBATasks.PostGIS.Update)\)\.  
To update the `pg_repack` extension, drop the extension and then create the new version in the upgraded DB instance\. For more information, see [pg\_repack installation](https://reorg.github.io/pg_repack/) in the `pg_repack` documentation\.

To upgrade an extension, use the following command\. 

```
ALTER EXTENSION extension_name UPDATE TO 'new_version'
```

For the list of supported versions of PostgreSQL extensions, see [Supported PostgreSQL extension versions](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.FeatureSupport.Extensions)\.

To list your currently installed extensions, use the PostgreSQL [pg\_extension](https://www.postgresql.org/docs/current/catalog-pg-extension.html) catalog in the following command\.

```
SELECT * FROM pg_extension;
```

To view a list of the specific extension versions that are available for your installation, use the PostgreSQL [ pg\_available\_extension\_versions](https://www.postgresql.org/docs/current/view-pg-available-extension-versions.html) view in the following command\.

```
SELECT * FROM pg_available_extension_versions;
```