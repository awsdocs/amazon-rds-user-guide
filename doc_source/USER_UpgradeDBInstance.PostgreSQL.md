# Upgrading the PostgreSQL DB Engine for Amazon RDS<a name="USER_UpgradeDBInstance.PostgreSQL"></a>

When Amazon RDS supports a new version of a database engine, you can upgrade your DB instances to the new version\. There are two kinds of upgrades for PostgreSQL DB instances: major version upgrades and minor version upgrades\. 

*Major version upgrades* can contain database changes that are not backward\-compatible with existing applications\. As a result, you must manually perform major version upgrades of your DB instances\. You can initiate a major version upgrade by modifying your DB instance\. However, before you perform a major version upgrade, we recommended that you follow the steps described in [Major Version Upgrades for PostgreSQL](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)\. 

In contrast, *minor version upgrades* include only changes that are backward\-compatible with existing applications\. You can initiate a minor version upgrade manually by modifying your DB instance\. Alternatively, you can enable the **Auto minor version upgrade** option when creating or modifying a DB instance\. Doing so means that your DB instance is automatically upgraded after Amazon RDS tests and approves the new version\. For more details, see [Automatic Minor Version Upgrades for PostgreSQL](#USER_UpgradeDBInstance.PostgreSQL.Minor)\. 

**Topics**
+ [Overview of Upgrading PostgreSQL](#USER_UpgradeDBInstance.PostgreSQL.Overview)
+ [Major Version Upgrades for PostgreSQL](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)
+ [Automatic Minor Version Upgrades for PostgreSQL](#USER_UpgradeDBInstance.PostgreSQL.Minor)

## Overview of Upgrading PostgreSQL<a name="USER_UpgradeDBInstance.PostgreSQL.Overview"></a>

To safely upgrade your DB instances, Amazon RDS uses the `pg_upgrade` utility found in the [PostgreSQL documentation](https://www.postgresql.org/docs/current/pgupgrade.html)\. 

Amazon RDS takes two DB snapshots during the upgrade process if your backup retention period is greater than 0\. The first DB snapshot is of the DB instance before any upgrade changes have been made\. If the upgrade doesn't work for your databases, you can restore this snapshot to create a DB instance running the old version\. The second DB snapshot is taken after the upgrade completes\. 

**Note**  
Amazon RDS takes DB snapshots during the upgrade process only if you have set the backup retention period for your DB instance to a number greater than 0\. To change your backup retention period, see [Modifying a DB Instance Running the PostgreSQL Database Engine](USER_ModifyPostgreSQLInstance.md)\. 

If your DB instance is in a Multi\-AZ deployment, both the primary writer DB instance and standby DB instances are upgraded\. The writer and standby DB instances are upgraded at the same time, and you experience an outage until the upgrade is complete\. 

After an upgrade is complete, you can't revert to the previous version of the database engine\. If you want to return to the previous version, restore the DB snapshot that was taken before the upgrade to create a new DB instance\. 

A PostgreSQL upgrade doesn't upgrade any PostgreSQL extensions\. To upgrade an extension, use the ALTER EXTENSION UPDATE command\. For example, to upgrade PostGIS when you upgrade the PostgreSQL DB engine from 9\.4\.x to 9\.5\.12, run the following command\. 

```
ALTER EXTENSION POSTGIS UPDATE TO '2.2.5'
```

For the list of supported versions of PostgreSQL extensions, see [PostgreSQL Extensions and Modules Supported on Amazon RDS](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.FeatureSupport.Extensions)\.

**Note**  
If you are running the PostGIS extension in your Amazon RDS PostgreSQL DB instance, make sure that you follow the [PostGIS upgrade instructions](https://postgis.net/docs/postgis_installation.html#upgrading) in the PostGIS documentation before you upgrade\. 

## Major Version Upgrades for PostgreSQL<a name="USER_UpgradeDBInstance.PostgreSQL.MajorVersion"></a>

Major version upgrades can contain database changes that are not backward\-compatible with previous versions of the database\. This functionality can cause your existing applications to stop working correctly\. 

As a result, Amazon RDS doesn't apply major version upgrades automatically\. To perform a major version upgrade, you modify your DB instance manually\. Make sure that you thoroughly test any upgrade to verify that your applications work correctly before applying the upgrade to your production DB instances\. When you do a PostgreSQL major version upgrade, we recommend that you follow the steps described in [How to Perform a Major Version Upgrade](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Process)\. 

A read replica can't undergo a major version upgrade but the read replica's source instance can\. If a read replica's source instance undergoes a major version upgrade, all read replicas for that source instance remain with the previous engine version\. In this case, the read replicas can no longer replicate changes performed on the source instance\. We recommend that you either promote your read replicas, or delete and recreate them after the source instance has upgraded to a different major version\. For more information, see [Working with PostgreSQL Read Replicas](USER_PostgreSQL.Replication.ReadReplicas.md)\.

### Choosing from Multiple Major Versions<a name="USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Multi"></a>

You can upgrade a PostgreSQL database to its next major version\. From some PostgreSQL database versions, you can skip to a higher major version when upgrading\. The following table lists the source PostgreSQL database versions and the associated target major versions available for upgrading\.


****  

| Source Version | Major Version Targets | Additional Major Version Targets \(without PostGIS Extension\) | 
| --- | --- | --- | 
| 9\.3\.x | 9\.4\.x |  | 
| 9\.3\.23 | 9\.4\.x, [9\.5\.13 ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version9513) | [9\.6\.9 ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version969)  | 
| 9\.3\.24 | 9\.4\.x, [9\.5\.14 ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version9514) | [9\.6\.10 ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version9610)  | 
| 9\.3\.25 | 9\.4\.x, [9\.5\.15 ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version9515) | [9\.6\.11 ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version9611) | 
| 9\.4\.x | 9\.5\.x |  | 
| 9\.4\.20 | 9\.5\.x | [11\.1 ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version111) | 
| 9\.4\.21 | 9\.5\.x |  [10\.7](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version107), [11\.2](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version112) | 
| 9\.4\.23 | 9\.5\.x |  [10\.9](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version109), [11\.4](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version114) | 
| 9\.5\.x | 9\.6\.x |  | 
| 9\.5\.15 | 9\.6\.x | [11\.1 ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version111) | 
| 9\.5\.16 | 9\.6\.x | [10\.7](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version107), [11\.2](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version112) | 
| 9\.5\.18 | 9\.6\.x |  [10\.9](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version109), [11\.4](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version114) | 
| 9\.6\.x | 10\.x |  | 
| 9\.6\.11 | 10\.x | [11\.1 ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version111) | 
| 9\.6\.12 | 10\.x | [11\.2](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version112) | 
| 9\.6\.14 | 10\.x | [11\.4](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.version114)  | 
| 10\.x | 11\.x |  | 

**Important**  
Upgrade targets are enabled to a higher version released at the same time as the source minor version or later\.  
If a database uses the PostGIS extension, you cannot skip major versions for some source to target combinations\. For these circumstances, upgrade in steps to the next major version one at a time until you reach the desired target version\. 

### How to Perform a Major Version Upgrade<a name="USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Process"></a>

We recommend the following process when upgrading an Amazon RDS PostgreSQL DB instance:
+ **Have a version\-compatible parameter group ready** – If you are using a custom parameter group, you have two options\. You can specify a default parameter group for the new DB engine version\. Or you can create your own custom parameter group for the new DB engine version\. 

  If you associate a new parameter group with a DB instance, reboot the database after the upgrade completes\. If the instance needs to be rebooted to apply the parameter group changes, the instance's parameter group status shows `pending-reboot`\. You can view an instance's parameter group status in the console or by using a describe command, such as [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html)\.
+ **Check for unsupported usage:**
  + **Prepared transactions** – Commit or roll back all open prepared transactions before attempting an upgrade\. 

    You can use the following query to verify that there are no open prepared transactions on your instance\. 

    ```
    SELECT count(*) FROM pg_catalog.pg_prepared_xacts;
    ```
  + **The line data type ** – If you are upgrading an RDS PostgreSQL 9\.3 instance, remove all uses of the `line` data type before attempting an upgrade\. The `line` data type wasn't fully implemented in PostgreSQL until version 9\.4\. 

    To verify that there are no uses of the `line` data type, use the following query on each database to be upgraded\. 

    ```
    SELECT count(*) FROM pg_catalog.pg_class c, pg_catalog.pg_namespace n, pg_catalog.pg_attribute a 
      WHERE c.oid = a.attrelid 
          AND NOT a.attisdropped
          AND a.atttypid = 'pg_catalog.line'::pg_catalog.regtype 
          AND c.relnamespace = n.oid 
          AND n.nspname !~ '^pg_temp_' 
          AND n.nspname !~ '^pg_toast_temp_' 
          AND n.nspname NOT IN ('pg_catalog', 'information_schema');
    ```
**Note**  
To list all databases for an instance, use the following query\.  

    ```
    SELECT d.datname FROM pg_catalog.pg_database d WHERE d.datallowconn = true;
    ```
  + **Reg\* data types** – Remove all uses of the *reg\** data types before attempting an upgrade\. Except for `regtype` and `regclass`, you can't upgrade the *reg\** data types\. The `pg_upgrade` utility can't persist this data type, which is used by Amazon RDS to do the upgrade\. 

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
+ **Perform a VACUUM** – To reduce downtime, perform a `VACUUM` operation before upgrading your DB instance\. If you don't perform a `VACUUM` operation, the upgrade process can take much longer\. This occurs because the `pg_upgrade` utility vacuums each database when you upgrade to a different major version\.
+ **Perform a backup** – We recommend that you perform a backup before performing the major version upgrade so that you have a known restore point for your database\. If your backup retention period is greater than 0, the upgrade process creates DB snapshots of your DB instance before and after upgrading\. To change your backup retention period, see [Modifying a DB Instance Running the PostgreSQL Database Engine](USER_ModifyPostgreSQLInstance.md)\. To perform a backup manually, see [Creating a DB Snapshot](USER_CreateSnapshot.md)\.
+ **Perform a dry run** – We highly recommend testing a major version upgrade on a duplicate of your production database before attempting the upgrade on your production database\. To create a duplicate test instance, you can either restore your database from a recent snapshot or point\-in\-time restore your database to its latest restorable time\. For more information, see [Restoring from a Snapshot](USER_RestoreFromSnapshot.md#USER_RestoreFromSnapshot.Restoring) or [Restoring a DB Instance to a Specified Time](USER_PIT.md)\. For details on performing the upgrade, see [Manually Upgrading the Engine Version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual)\. 
+ **Upgrade your production instance** – If the dry\-run major version upgrade was successful, you should now be able to upgrade your production database with confidence\. For more information, see [Manually Upgrading the Engine Version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual)\. 

**Note**  
During the upgrade process, you can't do a point\-in\-time restore of your instance\. After Amazon RDS performs the upgrade, it takes an automatic backup of the instance\. You can perform a point\-in\-time restore to times before the upgrade began and after the automatic backup of your instance has completed\. 

For information about an upgrade in progress, you can use Amazon RDS to view two logs that the `pg_upgrade` utility produces\. These are `pg_upgrade_internal.log` and `pg_upgrade_server.log`\. Amazon RDS appends a timestamp to the file name for these logs\. You can view these logs as you can any other log\. For more information, see [Amazon RDS Database Log Files](USER_LogAccess.md)\.

During the major version upgrade, the `public` and `template1` databases, and the `public` schema in every database on the instance are temporarily renamed\. These objects appear in the logs with their original name and a random string appended\. The string is appended so that custom settings such as the `locale` and `owner` are preserved during the major version upgrade\. After the upgrade completes, the objects are renamed back to their original names\. 

**Note**  
After you have completed the upgrade, you should run the `ANALYZE` operation to refresh the `pg_statistic` table\.

After you have completed the major version upgrade, consider testing your application on the upgraded database with a similar workload in order to verify that everything works as expected\. After the upgrade is verified, you can delete this test instance\. 

## Automatic Minor Version Upgrades for PostgreSQL<a name="USER_UpgradeDBInstance.PostgreSQL.Minor"></a>

If you enable the **Auto minor version upgrade** option when creating or modifying a DB instance, you can have your DB instance automatically upgraded\.

For each RDS for PostgreSQL major version, one minor version is designated by RDS as the automatic upgrade version\. After a minor version has been tested and approved by Amazon RDS, the minor version upgrade occurs automatically during your maintenance window\. RDS doesn't automatically set newer released minor versions as the automatic upgrade version\. Before RDS designates a newer automatic upgrade version, several criteria are considered, such as the following:
+ Known security issues
+ Bugs in the PostgreSQL community version
+ Overall fleet stability since the minor version was released

You can use the following AWS CLI command and script to determine the current automatic upgrade minor versions\. 

```
aws rds describe-db-engine-versions --engine postgres | grep -A 1 AutoUpgrade| grep -A 2 true |grep PostgreSQL | sort --unique | sed -e 's/"Description": "//g'
```

A PostgreSQL DB instance is automatically upgraded during your maintenance window if the following criteria are met:
+ The DB instance has the **Auto minor version upgrade** option enabled\.
+ The DB instance is running a minor DB engine version that is less than the current automatic upgrade minor version\.

For more information, see [Automatically Upgrading the Minor Engine Version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.AutoMinorVersionUpgrades)\. For more information about manually performing a minor version upgrade, see [Manually Upgrading the Engine Version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.Manual)\.

If your PostgreSQL DB instance is using read replication, you must upgrade all of the read replicas before upgrading the source instance\. If the DB instance is in a Multi\-AZ deployment, both the writer and standby replicas are upgraded, and the instance might not be available until the upgrade is complete\. 