# Modifying a DB Instance Running the MariaDB Database Engine<a name="USER_ModifyInstance.MariaDB"></a>

You can change the settings of a DB instance to accomplish tasks such as adding additional storage or changing the DB instance class\. This topic guides you through modifying an Amazon RDS MariaDB DB instance, and describes the settings for MariaDB instances\. 

We recommend that you test any changes on a test instance before modifying a production instance, so that you fully understand the impact of each change\. This is especially important when upgrading database versions\. 

After you modify your DB instance settings, you can apply the changes immediately, or apply them during the next maintenance window for the DB instance\. Some modifications cause an interruption by restarting the DB instance\. 

## Console<a name="USER_ModifyInstance.MariaDB.Console"></a>

**To modify a MariaDB DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to modify\. 

1. Choose **Modify**\. The **Modify DB instance** page appears\.

1. Change any of the settings that you want\. For information about each setting, see [Settings for MariaDB DB Instances](#USER_ModifyInstance.MariaDB.Settings)\. 

1. When all the changes are as you want them, choose **Continue** and check the summary of modifications\. 

1. To apply the changes immediately, choose **Apply immediately**\. Choosing this option can cause an outage in some cases\. For more information, see [Using the Apply Immediately Parameter](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\. 

1. On the confirmation page, review your changes\. If they are correct, choose **Modify DB Instance** to save your changes\. 

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to cancel your changes\. 

## AWS CLI<a name="USER_ModifyInstance.MariaDB.CLI"></a>

To modify a MariaDB DB instance by using the AWS CLI, call the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. Specify the DB instance identifier, and the parameters for the settings that you want to modify\. For information about each parameter, see [Settings for MariaDB DB Instances](#USER_ModifyInstance.MariaDB.Settings)\. 

**Example**  
The following code modifies `mydbinstance` by setting the backup retention period to 1 week \(7 days\)\. The code enables automatic minor version upgrades by using `--auto-minor-version-upgrade`\. To disable automatic minor version upgrades, use `--no-auto-minor-version-upgrade`\. The changes are applied during the next maintenance window by using `--no-apply-immediately`\. Use `--apply-immediately` to apply the changes immediately\. For more information, see [Using the Apply Immediately Parameter](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\.   
For Linux, OS X, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --backup-retention-period 7 \
    --auto-minor-version-upgrade \
    --no-apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier mydbinstance ^
    --backup-retention-period 7 ^
    --auto-minor-version-upgrade ^
    --no-apply-immediately
```

## RDS API<a name="USER_ModifyInstance.MariaDB.API"></a>

To modify a MariaDB instance by using the Amazon RDS API, call the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) operation\. Specify the DB instance identifier, and the parameters for the settings that you want to modify\. For information about each parameter, see [Settings for MariaDB DB Instances](#USER_ModifyInstance.MariaDB.Settings)\. 

**Example**  
The following code modifies `mydbinstance` by setting the backup retention period to 1 week \(7 days\) and enabling automatic minor version upgrades\. These changes are applied during the next maintenance window\.   

```
 1. https://rds.amazonaws.com/
 2.     ?Action=ModifyDBInstance
 3.     &ApplyImmediately=false
 4.     &AutoMinorVersionUpgrade=true
 5.     &BackupRetentionPeriod=7
 6.     &DBInstanceIdentifier=mydbinstance
 7.     &SignatureMethod=HmacSHA256
 8.     &SignatureVersion=4
 9.     &Version=2014-10-31
10.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
11.     &X-Amz-Credential=AKIADQKE4SARGYLE/20131016/us-west-1/rds/aws4_request
12.     &X-Amz-Date=20131016T233051Z
13.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
14.     &X-Amz-Signature=087a8eb41cb1ab0fc9ec1575f23e73757ffc6a1e42d7d2b30b9cc0be988cff97
```

## Settings for MariaDB DB Instances<a name="USER_ModifyInstance.MariaDB.Settings"></a>

The following table contains details about which settings you can modify, which settings you can't modify, when the changes can be applied, and whether the changes cause downtime for the DB instance\. 


****  

| Setting | Setting Description | When the Change Occurs | Downtime Notes | 
| --- | --- | --- | --- | 
|  Allocated storage  |  The storage, in gigabytes, that you want to allocate for your DB instance\.  You can't modify allocated storage if the DB instance status is **storage\-optimization** or if the allocated storage for the DB instance has been modified in the last six hours\. The maximum storage allowed depends on the storage type\. For more information, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.   |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false, the change occurs during the next maintenance window\.   |  No downtime\. Performance might be degraded during the change\.   | 
|  Auto minor version upgrade  |  **Enable auto minor version upgrade** to enable your DB instance to receive preferred minor DB engine version upgrades automatically when they become available\. Amazon RDS performs automatic minor version upgrades in the maintenance window\.  | – | – | 
|  Backup retention period  |  The number of days that automatic backups are retained\. To disable automatic backups, set the backup retention period to 0\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false and you change the setting from a nonzero value to another nonzero value, the change is applied asynchronously, as soon as possible\. Otherwise, the change occurs during the next maintenance window\.   |  An outage occurs if you change from 0 to a nonzero value, or from a nonzero value to 0\.   | 
|  Backup window  |  The time range during which automated backups of your databases occur\. The backup window is a start time in Universal Coordinated Time \(UTC\), and a duration in hours\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   |  The change is applied asynchronously, as soon as possible\.   | – | 
|  Certificate authority  |  The certificate that you want to use\.   |  If **Apply Immediately** is set to true, the change occurs immediately\.  If **Apply Immediately** is set to false, the change occurs during the next maintenance window\.  | An outage occurs during this change\. | 
|  Copy tags to snapshots  |  If you have any DB instance tags, this option copies them when you create a DB snapshot\.  For more information, see [Tagging Amazon RDS Resources](USER_Tagging.md)\.   |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.   | – | 
|  Database port  |  The port that you want to use to access the database\.  The port value must not match any of the port values specified for options in the option group for the DB instance\.   |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.   |  The DB instance is rebooted immediately\.  | 
|  DB engine version  |  The version of the MariaDB database engine that you want to use\. Before you upgrade your production DB instances, we recommend that you test the upgrade process on a test instance to verify its duration and to validate your applications\.  For more information, see [Upgrading the MariaDB DB Engine](USER_UpgradeDBInstance.MariaDB.md)\.   |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false, the change occurs during the next maintenance window\.   |  An outage occurs during this change\.  | 
|  DB instance class  |  The DB instance class that you want to use\.  For more information, see [Choosing the DB Instance Class](Concepts.DBInstanceClass.md)\.   |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false, the change occurs during the next maintenance window\.   |  An outage occurs during this change\.  | 
|  DB instance identifier  |  The DB instance identifier\. This value is stored as a lowercase string\.  For more information about the effects of renaming a DB instance, see [Renaming a DB Instance](USER_RenameInstance.md)\.   |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false, the change occurs during the next maintenance window\.   |  An outage occurs during this change\. The DB instance is rebooted\.   | 
|  DB parameter group  |  The parameter group that you want associated with the DB instance\.  For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.   |  The parameter group change occurs immediately\.   |  An outage doesn't occur during this change\. When you change the parameter group, changes to some parameters are applied to the DB instance immediately without a reboot\. Changes to other parameters are applied only after the DB instance is rebooted\. For more information, see [Rebooting a DB Instance](USER_RebootInstance.md)\.   | 
| Deletion protection | **Enable deletion protection** to prevent your DB instance from being deleted\. For more information, see [Deleting a DB Instance](USER_DeleteInstance.md)\. | – | – | 
|  Enhanced Monitoring  |  **Enable enhanced monitoring** to enable gathering metrics in real time for the operating system that your DB instance runs on\.   For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.   | – | – | 
|  Log exports  |  The types of MariaDB database log files to publish to Amazon CloudWatch Logs\.  For more information, see [MariaDB Database Log Files](USER_LogAccess.Concepts.MariaDB.md)\.   |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.   |  –  | 
|  Maintenance window  |  The time range during which system maintenance occurs\. System maintenance includes upgrades, if applicable\. The maintenance window is a start time in Universal Coordinated Time \(UTC\), and a duration in hours\.  If you set the window to the current time, there must be at least 30 minutes between the current time and end of the window to ensure any pending changes are applied\.  For more information, see [The Amazon RDS Maintenance Window](USER_UpgradeDBInstance.Maintenance.md#Concepts.DBMaintenance)\.   |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.   |  If there are one or more pending actions that cause an outage, and the maintenance window is changed to include the current time, then those pending actions are applied immediately, and an outage occurs\.   | 
|  Multi\-AZ deployment  |  **Yes** to deploy your DB instance in multiple Availability Zones\. Otherwise, **No**\. For more information, see [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)\.   |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false, the change occurs during the next maintenance window\.   |  –  | 
|  New master password  |  The password for your master user\. The password must contain from 8 to 41 alphanumeric characters\.   |  The change is applied asynchronously, as soon as possible\. This setting ignores the **Apply immediately** setting\.   |  –  | 
|  Option group  |  The option group that you want associated with the DB instance\.  For more information, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\.   |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false, the change occurs during the next maintenance window\.   |  –  | 
| Performance Insights |  **Enable Performance Insights** to monitor your DB instance load so that you can analyze and troubleshoot your database performance\. Choose a retention period to determine how much rolling data history to retain\. The default of seven days is in the free tier\. Long\-term retention \(two years\) is priced per vCPU per month\. You can't change the master key after the database is created\. For more information, see [Using Amazon RDS Performance Insights](USER_PerfInsights.md)\.  |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.   | – | 
|  Public accessibility  |  **Yes** to give the DB instance a public IP address, meaning that it's accessible outside the VPC\. To be publicly accessible, the DB instance also has to be in a public subnet in the VPC\. **No** to make the DB instance accessible only from inside the VPC\. For more information, see [Hiding a DB Instance in a VPC from the Internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\.   |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.   |  –  | 
|  Security group  |  The security group that you want associated with the DB instance\.  For more information, see [Working with DB Security Groups \(EC2\-Classic Platform\)](USER_WorkingWithSecurityGroups.md)\.   |  The change is applied asynchronously, as soon as possible\. This setting ignores the **Apply immediately** setting\.   |  –  | 
| Storage autoscaling |  **Enable storage autoscaling** to enable Amazon RDS to automatically increase storage when needed to avoid having your DB instance run out of storage space\. Use **Maximum storage threshold** to set the upper limit for Amazon RDS to automatically increase storage for your DB instance\. The default is 1,000 GiB\. For more information, see [Managing Capacity Automatically with Amazon RDS Storage Autoscaling](USER_PIOPS.StorageTypes.md#USER_PIOPS.Autoscaling)\.   |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.  |  –  | 
|  Storage type  |  The storage type that you want to use\.  For more information, see [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage)\.   |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false, the change occurs during the next maintenance window\.   |  The following changes all result in a brief outage while the process starts\. After that, you can use your database normally while the change takes place\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ModifyInstance.MariaDB.html)  | 
|  Subnet group  |  The subnet group for the DB instance\. You can use this setting to move your DB instance to a different VPC\.  If your DB instance isn't in a VPC, you can use this setting to move your DB instance into a VPC\.  For more information, see [Moving a DB Instance Not in a VPC into a VPC](USER_VPC.md#USER_VPC.Non-VPC2VPC)\.   |  If **Apply Immediately** is set to true, the change occurs immediately\.  If **Apply Immediately** is set to false, the change occurs during the next maintenance window\.   |  An outage occurs during this change\. The DB instance is rebooted\.   | 