# Upgrading the PostgreSQL DB Engine<a name="USER_UpgradeDBInstance.PostgreSQL"></a>

When Amazon RDS supports a new version of a database engine, you can upgrade your DB instances to the new version\. There are two kinds of upgrades: major version upgrades and minor version upgrades\. 

Amazon RDS supports major and minor version upgrades for PostgreSQL DB instances\. 

Major version upgrades can contain database changes that are not backward\-compatible with existing applications\. As a result, Amazon RDS doesn't apply major version upgrades automatically; you must manually modify your DB instance\. You can initiate a major version upgrade manually by modifying your instance\. However, there are recommended steps to follow when performing a major version upgrade\. For details, see [Major Version Upgrades](#USER_UpgradeDBInstance.PostgreSQL.MajorVersion)\. 

You can initiate a minor version upgrade manually by modifying your instance, or select the **Auto Minor Version Upgrade** option when creating or modifying a DB instance to have your instance automatically upgraded once the new version is tested and approved by Amazon RDS\. 

AWS RDS does not automatically upgrade PostgreSQL extensions\. To upgrade an extension, you must use the ALTER EXTENSION UPDATE command\. For example, to upgrade PostGIS when you upgrade the PostgreSQL DB engine from 9\.4\.x to 9\.5\.x, you would run the following command: 

```
ALTER EXTENSION POSTGIS UPDATE TO '2.2.2'
```

**Note**  
 If you are running the PostGIS extension in your Amazon RDS PostgreSQL instance, make sure and follow the [PostGIS upgrade instructions ](https://postgis.net/docs/postgis_installation.html#upgrading)before you upgrade PostgreSQL\. 

## Overview of Upgrading<a name="USER_UpgradeDBInstance.PostgreSQL.Overview"></a>

If your backup retention period is greater than 0, Amazon RDS takes two DB snapshots during both the major and minor upgrade process\. The first DB snapshot is of the DB instance before any upgrade changes have been made\. If the upgrade doesn't work for your databases, you can restore this snapshot to create a DB instance running the old version\. The second DB snapshot is taken after the upgrade completes\. 

**Note**  
Amazon RDS only takes DB snapshots if you have set the backup retention period for your DB instance to a number greater than 0\. To change your backup retention period, see [Modifying a DB Instance Running the PostgreSQL Database Engine](USER_ModifyPostgreSQLInstance.md)\. 

After an upgrade is complete, you can't revert to the previous version of the database engine\. If you want to return to the previous version, restore the DB snapshot that was taken before the upgrade to create a new DB instance\. 

If your DB instance is in a Multi\-AZ deployment, both the primary and standby DB instances are upgraded\. The primary and standby DB instances are upgraded at the same time, and you experience an outage until the upgrade is complete\. 

## Major Version Upgrades<a name="USER_UpgradeDBInstance.PostgreSQL.MajorVersion"></a>

Major version upgrades can contain database changes that are not backward\-compatible with previous versions of the database\. This functionality can cause your existing applications to stop working correctly\. As a result, Amazon RDS doesn't apply major version upgrades automatically; you must modify your DB instance manually to perform a major version upgrade\. You should thoroughly test any upgrade to verify that your applications work correctly before applying the upgrade to your production DB instances\. A best practice we recommend is to perform the major version upgrade on a restored instance that you create from a DB snapshot\.

Amazon RDS supports an in\-place upgrade from the following:
+ A PostgreSQL 9\.3\.x DB instance to a PostgreSQL 9\.4\.x DB instance
+ A PostgreSQL 9\.4\.x DB instance to a PostgreSQL 9\.5\.x DB instance
+ A PostgreSQL 9\.5\.x DB instance to a PostgreSQL 9\.6\.x DB instance

 Amazon RDS uses the `pg_upgrade` utility found at [http://www\.postgresql\.org/docs/9\.4/static/pgupgrade\.html](http://www.postgresql.org/docs/9.4/static/pgupgrade.html) to safely upgrade your instance\. 

Because some PostgreSQL minor versions updates for 9\.3 were released after major version 9\.4 was released, you cannot upgrade from version 9\.3\.9 to 9\.4\.1, and you cannot upgrade from version 9\.3\.10 to 9\.4\.1 or 9\.4\.4\. 

Read Replicas cannot undergo a major version upgrade\. The source instance can undergo a major version upgrade, but all Read Replicas remain as readable nodes on the previous engine version\. After a source instance is upgraded, its Read Replicas can no longer replicate changes performed on the source instance\. We recommend that you either promote your Read Replicas, or delete and recreate them after the source instance has upgraded to a different major version\. 

### Major Version Upgrade Process<a name="USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Process"></a>

We recommend the following process when upgrading an Amazon RDS PostgreSQL DB instance:

1. **Have a version\-compatible parameter group ready** – If you are using a custom parameter group, you must specify either a default parameter group for the new DB engine version or create your own custom parameter group for the new DB engine version\. Associating the new parameter group with the DB instance requires a customer\-initiated database reboot after the upgrade completes\. The instance's parameter group status will show `pending-reboot` if the instance needs to be rebooted to apply the parameter group changes\. An instance's parameter group status can be viewed in the AWS console or by using a "describe" call such as `describe-db-instances`\.

1. **Check for unsupported usage:**

   1. **Prepared transactions** – Commit or roll back all open prepared transactions before attempting an upgrade\. 

      You can use the following query to verify that there are no open prepared transactions on your instance: 

      ```
      SELECT count(*) FROM pg_catalog.pg_prepared_xacts;
      ```

   1. **The `line` data type ** – If you are upgrading an RDS PostgreSQL 9\.3 instance, you must remove all uses of the `line` data type before attempting an upgrade, because the `line` data type was not fully implemented in PostgreSQL until version 9\.4\. 

      You can use the following query on each database to be upgraded to verify that there are no uses of the `line` data type in each database: 

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
To list all databases on an instance, use the following query:  

      ```
      SELECT d.datname FROM pg_catalog.pg_database d WHERE d.datallowconn = true;
      ```

   1. **Reg\* data types** – Remove all uses of the *reg\** data types before attempting an upgrade, because these data types contain information that cannot be persisted with `pg_upgrade`\. Uses of *reg\** data types cannot be upgraded, except for `regtype` and `regclass`\. Remove all usages before attempting an upgrade\. 

      You can use the following query to verify that there are no uses of unsupported *reg\** data types in each database: 

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

   Perform a `VACUUM` operation before upgrading your instance\. The `pg_upgrade` utility vacuums each database when you upgrade to a different major version\. If you haven't performed a `VACUUM` operation, the upgrade process can take much longer, causing increased downtime for your RDS instance\. 

1. Perform a dry run of your major version upgrade\. We highly recommend testing major version upgrade on a duplicate of your production database before attempting it on your production database\. To create a duplicate test instance, you can either restore your database from a recent snapshot or point\-in\-time restore your database to its latest restorable time\. After you have completed the major version upgrade, consider testing your application on the upgraded database with a similar workload in order to verify that everything works as expected\. After the upgrade is verified, you can delete this test instance\. 

1. We recommend that you perform a backup before performing the major version upgrade so that you have a known restore point for your database\. Note that we create a DB snapshot of your DB instance before and after upgrading\.

1. Upgrade your production instance\. If the dry\-run major version upgrade was successful, you should now be able to upgrade your production database with confidence\. 

You can use Amazon RDS to view two logs that the `pg_upgrade` utility produces: `pg_upgrade_internal.log` and `pg_upgrade_server.log`\. Amazon RDS appends a timestamp to the file name for these logs\. You can view these logs as you can any other log\. 

You cannot perform a point\-in\-time restore of your instance to a point in time during the upgrade process\. During the upgrade process, RDS takes an automatic backup of the instance after the upgrade has been performed\. You can perform a point\-in\-time restore to times before the upgrade began and after the automatic backup of your instance has completed\. 

The `public` and `template1` databases and the `public` schema in every database on the instance are renamed during the major version upgrade\. These objects will appear in the logs with their original name and a random string appended\. The string is appended so that custom settings such as the `locale` and `owner` are preserved during the major version upgrade\. Once the upgrade completes, the objects are renamed back to their original names\. 

**Note**  
After you have completed the upgrade, you should run the `ANALYZE` operation to refresh the `pg_statistic` table\.

## Minor Version Upgrades for PostgreSQL<a name="USER_UpgradeDBInstance.PostgreSQL.Minor"></a>

Minor version upgrades occur automatically if a minor upgrade has been tested and approved by Amazon RDS and you selected the **Auto Minor Version Upgrade** option\. In all other cases, you must modify the DB instance manually to perform a minor version upgrade\. If you select the **Auto Minor Version Upgrade** option when creating or modifying a DB instance, you can have your instance automatically upgraded after the new version is tested and approved by Amazon RDS\. 

If your PostgreSQL DB instance is using read replication, you must upgrade all of the Read Replicas before upgrading the source instance\. If the DB instance is in a Multi\-AZ deployment, both the primary and standby replicas are upgraded, and the instance might not be available until the upgrade is complete\. 

## AWS Management Console<a name="USER_UpgradeDBInstance.PostgreSQL.Console"></a>

**To apply a DB engine major version upgrade to a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\. 

1. Choose the check box for the DB instance that you want to upgrade\. 

1. Choose **Instance Actions**, and then choose **Modify**\. 

1. For **DB Engine Version**, choose the new version\.

1. To upgrade immediately, select **Apply Immediately**\. To delay the upgrade to the next maintenance window, clear **Apply Immediately**\. 

1. Choose **Continue**\. 

1. Review the modification summary information\. To proceed with the upgrade, choose **Modify DB Instance**\. To cancel the upgrade, choose **Cancel** or **Back**\. 

## CLI<a name="USER_UpgradeDBInstance.PostgreSQL.CLI"></a>

To upgrade the engine version of a DB instance, use the AWS CLI [modify\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. Specify the following parameters: 
+ `--db-instance-identifier` – the name of the db instance\. 
+ `--engine-version` – the version number of the database engine to upgrade to\. 
+ `--allow-major-version-upgrade` – to to upgrade major version\. 
+ `--no-apply-immediately` – apply changes during the next maintenance window\. To apply changes immediately, use `--apply-immediately`\. 

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds modify-db-instance \
2.     --db-instance-identifier <mydbinstance> \
3.     --engine-version <new_version> \
4.     --allow-major-version-upgrade \
5.     --apply-immediately
```
For Windows:  

```
1. aws rds modify-db-instance ^
2.     --db-instance-identifier <mydbinstance> ^
3.     --engine-version <new_version> ^
4.     --allow-major-version-upgrade ^
5.     --apply-immediately
```

## API<a name="USER_UpgradeDBInstance.PostgreSQL.API"></a>

To upgrade the engine version of a DB instance, use the [ ModifyDBInstance](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html) action\. Specify the following parameters: 
+ `DBInstanceIdentifier` – the name of the db instance, for example *mydbinstance*\. 
+ `EngineVersion` – the version number of the database engine to upgrade to\. 
+ `AllowMajorVersionUpgrade` – set to `true` to upgrade major version\. 
+ `ApplyImmediately` – whether to apply changes immediately or during the next maintenance window\. To apply changes immediately, set the value to *true*\. To apply changes during the next maintenance window, set the value to *false*\. 

**Example**  

```
 1. https://rds.us-east-1.amazonaws.com/
 2.    ?Action=ModifyDBInstance
 3.    &ApplyImmediately=false
 4.    &DBInstanceIdentifier=mydbinstance
 5.    &EngineVersion=new_version
 6.    &SignatureMethod=HmacSHA256
 7.    &SignatureVersion=4
 8.    &Version=2013-09-09
 9.    &X-Amz-Algorithm=AWS4-HMAC-SHA256
10.    &X-Amz-Credential=AKIADQKE4SARGYLE/20131016/us-east-1/rds/aws4_request
11.    &X-Amz-Date=20131016T233051Z
12.    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
13.    &X-Amz-Signature=087a8eb41cb1ab5f99e81575f23e73757ffc6a1e42d7d2b30b9cc0be988cff97
```

## Related Topics<a name="USER_UpgradeDBInstance.PostgreSQL.Related"></a>
+ [Amazon RDS Maintenance](USER_UpgradeDBInstance.Maintenance.md)
+ [Updating the Operating System for a DB Instance or DB Cluster](USER_UpgradeDBInstance.OSUpgrades.md)