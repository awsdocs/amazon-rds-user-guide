# Modifying a DB Instance Running the PostgreSQL Database Engine<a name="USER_ModifyPostgreSQLInstance"></a>

You can change the settings of a DB instance to accomplish tasks such as adding additional storage or changing the DB instance class\. This topic guides you through modifying an Amazon RDS PostgreSQL DB instance, and describes the settings for PostgreSQL instances\. For information about additional tasks, such as renaming, rebooting, deleting, tagging, or upgrading an Amazon RDS DB instance, see [Amazon RDS DB Instance Lifecycle](CHAP_CommonTasks.md)\. We recommend that you test any changes on a test instance before modifying a production instance so you better understand the impact of a change\. This is especially important when upgrading database versions\.

You can have the changes apply immediately or have them applied during the DB instance's next maintenance window\. Applying changes immediately can cause an outage in some cases; for more information on the impact of the **Apply Immediately** option when modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

## Console<a name="USER_ModifyPostgreSQLInstance.Console"></a>

**To modify a PostgreSQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to modify\. 

1. Choose **Modify**\. The **Modify DB Instance** page appears\.

1. Change any of the settings that you want\. For information about each setting, see [Settings for PostgreSQL DB Instances](#USER_ModifyInstance.Postgres.Settings)\. 

1. When all the changes are as you want them, choose **Continue**\. 

1. To apply the changes immediately, choose **Apply Immediately**\. Choosing this option can cause an outage in some cases\. For more information, see [Using the Apply Immediately Parameter](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\. 

1. On the confirmation page, review your changes\. If they are correct, choose **Modify DB Instance** to save your changes\. 

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to cancel your changes\. 

## AWS CLI<a name="USER_ModifyPostgreSQLInstance.CLI"></a>

To modify a PostgreSQL DB instance, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)\. 

**Example**  
The following code modifies `pgdbinstance` by setting the backup retention period to 1 week \(7 days\) and disabling automatic minor version upgrades\. These changes are applied during the next maintenance window\.  

**Parameters**
+ `--db-instance-identifier`—the name of the DB instance
+ `--backup-retention-period`—the number of days to retain automatic backups\.
+ `--auto-minor-version-upgrade`—allow automatic minor version upgrades\. To disallow automatic minor version upgrades, use `--no-auto-minor-version-upgrade`\.
+ `--no-apply-immediately`—apply changes during the next maintenance window\. To apply changes immediately, use `--apply-immediately`\.
For Linux, OS X, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier pgdbinstance \
    --backup-retention-period 7 \
    --auto-minor-version-upgrade \
    --no-apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier pgdbinstance ^
    --backup-retention-period 7 ^
    --auto-minor-version-upgrade ^
    --no-apply-immediately
```

## API<a name="USER_ModifyPostgreSQLInstance.API"></a>

To modify a PostgreSQL DB instance, use the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) operation\.

**Example**  
The following code modifies `pgdbinstance` by setting the backup retention period to 1 week \(7 days\) and disabling automatic minor version upgrades\. These changes are applied during the next maintenance window\.  

**Parameters**
+ `DBInstanceIdentifier`—the name of the DB instance
+ `BackupRetentionPeriod`—the number of days to retain automatic backups\.
+ `AutoMinorVersionUpgrade`=`true`—allow automatic minor version upgrades\. To disallow automatic minor version upgrades, set the value to `false`\.
+ `ApplyImmediately`=`false`—apply changes during the next maintenance window\. To apply changes immediately, set the value to `true`\.

```
 1. https://rds.us-east-1.amazonaws.com/
 2.    ?Action=ModifyDBInstance
 3.    &ApplyImmediately=false
 4.    &AutoMinorVersionUpgrade=true
 5.    &BackupRetentionPeriod=7
 6.    &DBInstanceIdentifier=mydbinstance
 7.    &SignatureMethod=HmacSHA256
 8.    &SignatureVersion=4
 9.    &Version=2013-09-09
10.    &X-Amz-Algorithm=AWS4-HMAC-SHA256
11.    &X-Amz-Credential=AKIADQKE4SARGYLE/20131016/us-east-1/rds/aws4_request
12.    &X-Amz-Date=20131016T233051Z
13.    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
14.    &X-Amz-Signature=087a8eb41cb1ab0fc9ec1575f23e73757ffc6a1e42d7d2b30b9cc0be988cff97
```

## Settings for PostgreSQL DB Instances<a name="USER_ModifyInstance.Postgres.Settings"></a>

The following table contains details about which settings you can modify, which settings you can't modify, when the changes can be applied, and whether the changes cause downtime for the DB instance\. 


****  

| Setting | Setting Description | When the Change Occurs | Downtime Notes | 
| --- | --- | --- | --- | 
|  Allocated storage  |  The storage, in gigabytes, that you want to allocate for your DB instance\. You can only increase the allocated storage, you can't reduce the allocated storage\.  You can't modify allocated storage if the DB instance status is **storage\-optimization** or if the allocated storage for the DB instance has been modified in the last six hours\. The maximum storage allowed depends on the storage type\. For more information, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.   |  If **Apply Immediately** is set to true, the change occurs immediately\.  If **Apply Immediately** is set to false, the change occurs during the next maintenance window\.   |  No downtime\. Performance might be degraded during the change\.   | 
|  Auto minor version upgrade  |  **Enable auto minor version upgrade** to enable your DB instance to receive preferred minor DB engine version upgrades automatically when they become available\. Amazon RDS performs automatic minor version upgrades in the maintenance window\.  | – | – | 
|  Backup retention period  |  The number of days that automatic backups are retained\. To disable automatic backups, set the backup retention period to 0\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   |  If **Apply Immediately** is set to true, the change occurs immediately\.  If **Apply Immediately** is set to false and you change the setting from a nonzero value to another nonzero value, the change is applied asynchronously, as soon as possible\. Otherwise, the change occurs during the next maintenance window\.   |  An outage occurs if you change from 0 to a nonzero value, or from a nonzero value to 0\.   | 
|  Backup window  |  The time range during which automated backups of your databases occur\. The backup window is a start time in Universal Coordinated Time \(UTC\), and a duration in hours\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   |  The change is applied asynchronously, as soon as possible\.   | – | 
|  Certificate authority  |  The certificate that you want to use\.   |  If **Apply Immediately** is set to true, the change occurs immediately\.  If **Apply Immediately** is set to false, the change occurs during the next maintenance window\.  | An outage occurs during this change\. | 
|  Copy tags to snapshots  |  If you have any DB instance tags, this option copies them when you create a DB snapshot\.  For more information, see [Tagging Amazon RDS Resources](USER_Tagging.md)\.   |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.   | – | 
|  Database authentication  |  The database authentication option you want to use\. Choose **Password authentication** to authenticate database users with database passwords only\. Choose **Password and IAM DB authentication** to authenticate database users with database passwords and user credentials through IAM users and roles\. For more information, see [IAM Database Authentication for MySQL and PostgreSQL](UsingWithRDS.IAMDBAuth.md)\.  |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false, the change occurs during the next maintenance window\.   | – | 
|  Database port  |  The port that you want to use to access the database\.  The port value must not match any of the port values specified for options in the option group for the DB instance\.   |  The change occurs immediately\. This setting ignores the **Apply Immediately** setting\.   |  The DB instance is rebooted immediately\.  | 
|  DB engine version  |  The version of the PostgreSQL database engine that you want to use\. Before you upgrade your production DB instances, we recommend that you test the upgrade process on a test instance to verify its duration and to validate your applications\.   |  If **Apply Immediately** is set to true, the change occurs immediately\.  If **Apply Immediately** is set to false, the change occurs during the next maintenance window\.   |  An outage occurs during this change\.  | 
|  DB instance class  |  The DB instance class that you want to use\.  For more information, see [Choosing the DB Instance Class](Concepts.DBInstanceClass.md)   |  If **Apply Immediately** is set to true, the change occurs immediately\.  If **Apply Immediately** is set to false, the change occurs during the next maintenance window\.   |  An outage occurs during this change\.  | 
|  DB instance identifier  |  The DB instance identifier\. This value is stored as a lowercase string\.  For more information about the effects of renaming a DB instance, see [Renaming a DB Instance](USER_RenameInstance.md)\.   |  If **Apply Immediately** is set to true, the change occurs immediately\.  If **Apply Immediately** is set to false, the change occurs during the next maintenance window\.   |  An outage occurs during this change\. The DB instance is rebooted\.   | 
|  DB parameter group  |  The parameter group that you want associated with the DB instance\.  For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.   |  The parameter group change occurs immediately\.   |  An outage doesn't occur during this change\. When you change the parameter group, changes to some parameters are applied to the DB instance immediately without a reboot\. Changes to other parameters are applied only after the DB instance is rebooted\. For more information, see [Rebooting a DB Instance](USER_RebootInstance.md)\.   | 
| Deletion protection |  **Enable deletion protection** to prevent your DB instance from being deleted\.  For more information, see [Deleting a DB Instance](USER_DeleteInstance.md)\.  | – | – | 
|  Enhanced monitoring  |  **Enable enhanced monitoring** to enable gathering metrics in real time for the operating system that your DB instance runs on\.   For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.   | – | – | 
|  License model  |  You can't change the license model because PostgreSQL has only one license model\.  | – | – | 
|  Log exports  |  The types of PostgreSQL database log files to publish to Amazon CloudWatch Logs\.  For more information, see [PostgreSQL Database Log Files](USER_LogAccess.Concepts.PostgreSQL.md)\.   |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.   |  –  | 
|  Maintenance window  |  The time range during which system maintenance occurs\. System maintenance includes upgrades, if applicable\. The maintenance window is a start time in Universal Coordinated Time \(UTC\), and a duration in hours\.  If you set the window to the current time, there must be at least 30 minutes between the current time and end of the window to ensure any pending changes are applied\.  For more information, see [The Amazon RDS Maintenance Window](USER_UpgradeDBInstance.Maintenance.md#Concepts.DBMaintenance)\.   |  The change occurs immediately\. This setting ignores the **Apply Immediately** setting\.   |  If there are one or more pending actions that cause an outage, and the maintenance window is changed to include the current time, then those pending actions are applied immediately, and an outage occurs\.   | 
|  Multi\-AZ deployment  |  **Yes** to deploy your DB instance in multiple Availability Zones\. Otherwise, **No**\. For more information, see [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)\.   |  If **Apply Immediately** is set to true, the change occurs immediately\.  If **Apply Immediately** is set to false, the change occurs during the next maintenance window\.   |  –  | 
|  New master password  |  The password for your master user\. The password must contain from 8 to 30 alphanumeric characters\.   |  The change is applied asynchronously, as soon as possible\. This setting ignores the **Apply Immediately** setting\.   |  –  | 
|  Option group  |  No options are available for PostgreSQL DB instances\.  For more information, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\.   |  –  |  –  | 
| Performance Insights |  **Enable Performance Insights** to monitor your DB instance load so that you can analyze and troubleshoot your database performance\. Choose a retention period to determine how much rolling data history to retain\. The default of seven days is in the free tier\. Long\-term retention \(two years\) is priced per vCPU per month\. You can't change the master key after the database is created\. For more information, see [Using Amazon RDS Performance Insights](USER_PerfInsights.md)\.  |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.   | – | 
|  Public accessibility  |  **Yes** to give the DB instance a public IP address, meaning that it's accessible outside the VPC\. To be publicly accessible, the DB instance also has to be in a public subnet in the VPC\. **No** to make the DB instance accessible only from inside the VPC\. For more information, see [Hiding a DB Instance in a VPC from the Internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\.   |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.   |  –  | 
|  Security group  |  The security group that you want associated with the DB instance\.  For more information, see [Working with DB Security Groups \(EC2\-Classic Platform\)](USER_WorkingWithSecurityGroups.md)\.   |  The change is applied asynchronously, as soon as possible\. This setting ignores the **Apply Immediately** setting\.   |  –  | 
| Storage autoscaling |  **Enable storage autoscaling** to enable Amazon RDS to automatically increase storage when needed to avoid having your DB instance run out of storage space\. Use **Maximum storage threshold** to set the upper limit for Amazon RDS to automatically increase storage for your DB instance\. The default is 1,000 GiB\. For more information, see [Managing Capacity Automatically with Amazon RDS Storage Autoscaling](USER_PIOPS.StorageTypes.md#USER_PIOPS.Autoscaling)\.   |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.  |  –  | 
|  Storage type  |  The storage type that you want to use\.  For more information, see [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage)\.   |  If **Apply Immediately** is set to true, the change occurs immediately\.  If **Apply Immediately** is set to false, the change occurs during the next maintenance window\.   |  The following changes all result in a brief outage while the process starts\. After that, you can use your database normally while the change takes place\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ModifyPostgreSQLInstance.html)  | 
|  Subnet group  |  The subnet group for the DB instance\. You can use this setting to move your DB instance to a different VPC\.  If your DB instance is not in a VPC, you can use this setting to move your DB instance into a VPC\.  For more information, see [Moving a DB Instance Not in a VPC into a VPC](USER_VPC.md#USER_VPC.Non-VPC2VPC)\.   |  If **Apply Immediately** is set to true, the change occurs immediately\.  If **Apply Immediately** is set to false, the change occurs during the next maintenance window\.   |  An outage occurs during this change\. The DB instance is rebooted\.   | 