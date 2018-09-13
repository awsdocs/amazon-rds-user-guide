# Upgrading the MariaDB DB Engine<a name="USER_UpgradeDBInstance.MariaDB"></a>

When Amazon RDS supports a new version of a database engine, you can upgrade your DB instances to the new version\. There are two kinds of upgrades: major version upgrades and minor version upgrades\. You must modify the DB instance manually to perform a major version upgrade\.  

For more information about MariaDB supported versions and version management, see [MariaDB on Amazon RDS Versions](CHAP_MariaDB.md#MariaDB.Concepts.VersionMgmt)\. 

## Overview of Upgrading<a name="USER_UpgradeDBInstance.MariaDB.Overview"></a>

Major version upgrades can contain database changes that are not backward\-compatible with existing applications\. As a result, Amazon RDS doesn't apply major version upgrades automatically; you must manually modify your DB instance\. You should thoroughly test any upgrade before applying it to your production instances\. 

Minor version upgrades that contain database changes that are backward\-compatible with the previous version might be applied automatically\. Amazon RDS doesn't automatically upgrade an Amazon RDS DB instance until after posting an announcement to the forums announcement page, and sending customers an e\-mail notification\. Automatic upgrades are scheduled so that you can plan around them, because downtime is required to upgrade a DB instance, even for Multi\-AZ instances\. 

Amazon RDS takes two DB snapshots during the upgrade process\. The first DB snapshot is of the DB instance before any upgrade changes have been made\. If the upgrade doesn't work for your databases, you can restore this snapshot to create a DB instance running the old version\. The second DB snapshot is taken when the upgrade completes\. 

**Note**  
Amazon RDS only takes DB snapshots if you have set the backup retention period for your DB instance to a number greater than 0\. To change your backup retention period, see [Modifying a DB Instance Running the MariaDB Database Engine](USER_ModifyInstance.MariaDB.md)\. 

After the upgrade is complete, you can't revert to the previous version of the database engine\. If you want to return to the previous version, restore the first DB snapshot taken to create a new DB instance\. 

You control when to upgrade your DB instance to a new version supported by Amazon RDS\. This level of control helps you maintain compatibility with specific database versions and test new versions with your application before deploying in production\. When you are ready, you can perform version upgrades at the times that best fit your schedule\. 

If your DB instance is using read replication, you must upgrade all of the Read Replicas before upgrading the source instance\. 

If your DB instance is in a Multi\-AZ deployment, both the primary and standby DB instances are upgraded\. The primary and standby DB instances are upgraded at the same time and you will experience an outage until the upgrade is complete\. The time for the outage varies based on your database engine, engine version, and the size of your DB instance\. 

If you are using a custom parameter group, and you perform a major version upgrade, you must specify either a default parameter group for the new DB engine version or create your own custom parameter group for the new DB engine version\. Associating the new parameter group with the DB instance requires a customer\-initiated database reboot after the upgrade completes\. The instance's parameter group status will show `pending-reboot` if the instance needs to be rebooted to apply the parameter group changes\. An instance's parameter group status can be viewed in the AWS console or by using a "describe" call such as `describe-db-instances`\.

## AWS Management Console<a name="USER_UpgradeDBInstance.MariaDB.Console"></a>

**To upgrade the engine version of a DB instance by using the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**, and then select the DB instance that you want to upgrade\. 

1. Choose **Instance actions**, and then choose **Modify**\. The **Modify DB Instance** page appears\.

1. For **DB engine version**, choose the new version\.

1. Choose **Continue** and check the summary of modifications\. 

1. To apply the changes immediately, select **Apply immediately**\. Selecting this option can cause an outage in some cases\. For more information, see [The Impact of Apply Immediately](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\. 

1. On the confirmation page, review your changes\. If they are correct, choose **Modify DB Instance** to save your changes\. 

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to cancel your changes\. 

## CLI<a name="USER_UpgradeDBInstance.MariaDB.CLI"></a>

To upgrade the engine version of a DB instance, use the AWS CLI [modify\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. Specify the following parameters: 
+ `--db-instance-identifier` – the name of the DB instance\. 
+ `--engine-version` – the version number of the database engine to upgrade to\. 
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

## API<a name="USER_UpgradeDBInstance.MariaDB.API"></a>

To upgrade the engine version of a DB instance, use the [ ModifyDBInstance](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html) action\. Specify the following parameters: 
+ `DBInstanceIdentifier` – the name of the DB instance, for example *mydbinstance*\. 
+ `EngineVersion` – the version number of the database engine to upgrade to\. 
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

## Related Topics<a name="USER_UpgradeDBInstance.MariaDB.Related"></a>
+ [Maintaining a DB Instance](USER_UpgradeDBInstance.Maintenance.md)
+ [Applying Updates for a DB Instance](USER_UpgradeDBInstance.Maintenance.md#USER_UpgradeDBInstance.OSUpgrades)