# Modifying a Multi\-AZ DB cluster<a name="modify-multi-az-db-cluster"></a>

A Multi\-AZ DB cluster has a writer DB instance and two reader DB instances in three separate Availability Zones\. Multi\-AZ DB clusters provide high availability, increased capacity for read workloads, and lower latency when compared to Multi\-AZ deployments\. For more information about Multi\-AZ DB clusters, see [Multi\-AZ DB cluster deployments](multi-az-db-clusters-concepts.md)\.

You can modify a Multi\-AZ DB cluster to change its settings\. You can also perform operations on a Multi\-AZ DB cluster, such as taking a snapshot of it\. However, you can't modify the DB instances in a Multi\-AZ DB cluster, and the only supported operation is rebooting a DB instance\.

**Note**  
Multi\-AZ DB clusters are supported only for the MySQL and PostgreSQL DB engines\.

You can modify a Multi\-AZ DB cluster using the AWS Management Console, the AWS CLI, or the RDS API\.

## Console<a name="modify-multi-az-db-cluster-console"></a>

**To modify a Multi\-AZ DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the Multi\-AZ DB cluster that you want to modify\. 

1. Choose **Modify**\. The **Modify DB cluster** page appears\.

1. Change any of the settings that you want\. For information about each setting, see [Settings for modifying Multi\-AZ DB clusters](#modify-multi-az-db-cluster-settings)\. 

1. When all the changes are as you want them, choose **Continue** and check the summary of modifications\. 

1. \(Optional\) Choose **Apply immediately** to apply the changes immediately\. Choosing this option can cause downtime in some cases\. For more information, see [Using the Apply Immediately setting](#modify-multi-az-db-cluster-apply-immediately)\. 

1. On the confirmation page, review your changes\. If they're correct, choose **Modify DB cluster** to save your changes\. 

   Or choose **Back** to edit your changes or **Cancel** to cancel your changes\. 

## AWS CLI<a name="modify-multi-az-db-cluster-cli"></a>

To modify a Multi\-AZ DB cluster by using the AWS CLI, call the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) command\. Specify the DB cluster identifier and the values for the options that you want to modify\. For information about each option, see [Settings for modifying Multi\-AZ DB clusters](#modify-multi-az-db-cluster-settings)\. 

**Example**  
The following code modifies `my-multi-az-dbcluster` by setting the backup retention period to 1 week \(7 days\)\. The code turns on deletion protection by using `--deletion-protection`\. To turn off deletion protection, use `--no-deletion-protection`\. The changes are applied during the next maintenance window by using `--no-apply-immediately`\. Use `--apply-immediately` to apply the changes immediately\. For more information, see [Using the Apply Immediately setting](#modify-multi-az-db-cluster-apply-immediately)\.   
For Linux, macOS, or Unix:  

```
aws rds modify-db-cluster \
    --db-cluster-identifier my-multi-az-dbcluster \
    --backup-retention-period 7 \
    --deletion-protection \
    --no-apply-immediately
```
For Windows:  

```
aws rds modify-db-cluster ^
    --db-cluster-identifier my-multi-az-dbcluster ^
    --backup-retention-period 7 ^
    --deletion-protection ^
    --no-apply-immediately
```

## RDS API<a name="modify-multi-az-db-cluster-api"></a>

To modify a Multi\-AZ DB cluster by using the Amazon RDS API, call the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) operation\. Specify the DB cluster identifier, and the parameters for the settings that you want to modify\. For information about each parameter, see [Settings for modifying Multi\-AZ DB clusters](#modify-multi-az-db-cluster-settings)\. 

## Using the Apply Immediately setting<a name="modify-multi-az-db-cluster-apply-immediately"></a>

When you modify a Multi\-AZ DB cluster, you can apply the changes immediately\. To apply changes immediately, you choose the **Apply Immediately** option in the AWS Management Console\. Or you use the `--apply-immediately` option when calling the AWS CLI or set the `ApplyImmediately` parameter to `true` when using the Amazon RDS API\. 

If you don't choose to apply changes immediately, the changes are put into the pending modifications queue\. During the next maintenance window, any pending changes in the queue are applied\. If you choose to apply changes immediately, your new changes and any changes in the pending modifications queue are applied\. 

**Important**  
If any of the pending modifications require the DB cluster to be temporarily unavailable \(*downtime*\), choosing the apply immediately option can cause unexpected downtime\.  
When you choose to apply a change immediately, any pending modifications are also applied immediately, instead of during the next maintenance window\.   
If you don't want a pending change to be applied in the next maintenance window, you can modify the DB instance to revert the change\. You can do this by using the AWS CLI and specifying the `--apply-immediately` option\.

Changes to some database settings are applied immediately, even if you choose to defer your changes\. To see how the different database settings interact with the apply immediately setting, see [Settings for modifying Multi\-AZ DB clusters](#modify-multi-az-db-cluster-settings)\.

## Settings for modifying Multi\-AZ DB clusters<a name="modify-multi-az-db-cluster-settings"></a>

For details about settings that you can use to modify a Multi\-AZ DB cluster, see the following table\. For more information about the AWS CLI options, see [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html)\. For more information about the RDS API parameters, see [ ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html)\.


| Console setting | Setting description | CLI option and RDS API parameter | When the change occurs | Downtime notes | 
| --- | --- | --- | --- | --- | 
|  **Allocated storage**  |  The amount of storage to allocate for each DB instance in your DB cluster \(in gibibytes\)\. For more information, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.   |  **CLI option:** `--allocated-storage` **API parameter:**  `AllocatedStorage`  |  If you choose to apply the change immediately, it occurs immediately\. If you don't choose to apply the change immediately, it occurs during the next maintenance window\.  |  Downtime doesn't occur during this change\.  | 
| Auto minor version upgrade |  **Enable auto minor version upgrade** to have your DB cluster receive preferred minor DB engine version upgrades automatically when they become available\. Amazon RDS performs automatic minor version upgrades in the maintenance window\.  |  **CLI option:** `--auto-minor-version-upgrade` `--no-auto-minor-version-upgrade` **API parameter:** `AutoMinorVersionUpgrade`  |  The change occurs immediately\. This setting ignores the apply immediately setting\.   |  Downtime doesn't occur during this change\.  | 
| Backup retention period  |  The number of days that you want automatic backups of your DB cluster to be retained\. For any nontrivial DB cluster, set this value to **1** or greater\. For more information, see [Working with backups](USER_WorkingWithAutomatedBackups.md)\.  |  **CLI option:** `--backup-retention-period` **API parameter:** `BackupRetentionPeriod`  |  If you choose to apply the change immediately, it occurs immediately\.  If you don't choose to apply the change immediately, and you change the setting from a nonzero value to another nonzero value, the change is applied asynchronously, as soon as possible\. Otherwise, the change occurs during the next maintenance window\.   |  Downtime occurs if you change from 0 to a nonzero value, or from a nonzero value to 0\.  | 
|  Backup window |  The time period during which Amazon RDS automatically takes a backup of your DB cluster\. Unless you have a specific time that you want to have your database backed up, use the default of **No preference**\. For more information, see [Working with backups](USER_WorkingWithAutomatedBackups.md)\.  |  **CLI option:** `--preferred-backup-window` **API parameter:** `PreferredBackupWindow`  |  The change is applied asynchronously, as soon as possible\.   |  Downtime doesn't occur during this change\.  | 
|  Copy tags to snapshots  |  This option copies any DB cluster tags to a DB snapshot when you create a snapshot\. For more information, see [Tagging Amazon RDS resources](USER_Tagging.md)\.   |  **CLI option:** `-copy-tags-to-snapshot` `-no-copy-tags-to-snapshot` **RDS API parameter:** `CopyTagsToSnapshot`  |  The change occurs immediately\. This setting ignores the apply immediately setting\.   |  Downtime doesn't occur during this change\.  | 
|  Database authentication  |  For Multi\-AZ DB clusters, only **Password authentication** is supported\.  |  None because password authentication is the default\.  |  If you choose to apply the change immediately, it occurs immediately\. If you don't choose to apply the change immediately, it occurs during the next maintenance window\.  |  Downtime doesn't occur during this change\.  | 
|  **DB cluster identifier**  |  The DB cluster identifier\. This value is stored as a lowercase string\. When you change the DB cluster identifier, the DB cluster endpoint changes\. The identifiers and endpoints of the DB instances in the DB cluster also change\. The new DB cluster name must be unique\. The maximum length is 63 characters\. The names of the DB instances in the DB cluster are changed to correspond with the new name of the DB cluster\. A new DB instance name can't be the same as the name of an existing DB instance\. For example, if you change the DB cluster name to `maz`, a DB instance name might be changed to `maz-instance-1`\. In this case, there can't be an existing DB instance named `maz-instance-1`\. For more information, see [Renaming a Multi\-AZ DB cluster](multi-az-db-cluster-rename.md)\.  |  **CLI option:** `--new-db-cluster-identifier` **RDS API parameter:** `NewDBClusterIdentifier`  |  If you choose to apply the change immediately, it occurs immediately\. If you don't choose to apply the change immediately, it occurs during the next maintenance window\.  |  An outage doesn't occur during this change\.  | 
|  DB cluster instance class  |  The compute and memory capacity of each DB instance in the Multi\-AZ DB cluster, for example `db.r6gd.xlarge`\.  If possible, choose a DB instance class large enough that a typical query working set can be held in memory\. When working sets are held in memory, the system can avoid writing to disk, which improves performance\. Currently, Multi\-AZ DB clusters only support db\.m6gd and db\.r6gd DB instance classes\. For more information about DB instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\.  |  **CLI option:** `--db-cluster-instance-class` **RDS API parameter:** `DBClusterInstanceClass`  |  If you choose to apply the change immediately, it occurs immediately\. If you don't choose to apply the change immediately, it occurs during the next maintenance window\.  |  Downtime occurs during this change\.  | 
|  **DB cluster parameter group**  |  The DB cluster parameter group that you want associated with the DB cluster\.  For more information, see [Working with parameter groups for Multi\-AZ DB clusters](multi-az-db-clusters-concepts.md#multi-az-db-clusters-concepts-parameter-groups)\.   |  **CLI option:** `--db-cluster-parameter-group-name` **RDS API parameter:** `DBClusterParameterGroupName`  |  The parameter group change occurs immediately\.  |  An outage doesn't occur during this change\. When you change the parameter group, changes to some parameters are applied to the DB instances in the Multi\-AZ DB cluster immediately without a reboot\. Changes to other parameters are applied only after the DB instances are rebooted\.  | 
|  DB engine version  |  The version of database engine that you want to use\.  |  **CLI option:** `--engine-version` **RDS API parameter:** `EngineVersion`  |  If you choose to apply the change immediately, it occurs immediately\. If you don't choose to apply the change immediately, it occurs during the next maintenance window\.  |  An outage occurs during this change\.  | 
| Deletion protection |  **Enable deletion protection** to prevent your DB cluster from being deleted\. For more information, see [Deleting a DB instance](USER_DeleteInstance.md)\.  |  **CLI option:** `--deletion-protection` `--no-deletion-protection` **RDS API parameter:** `DeletionProtection`  |  The change occurs immediately\. This setting ignores the apply immediately setting\.   |  An outage doesn't occur during this change\.  | 
|  Maintenance window  |  The 30\-minute window in which pending modifications to your DB cluster are applied\. If the time period doesn't matter, choose **No preference**\. For more information, see [The Amazon RDS maintenance window](USER_UpgradeDBInstance.Maintenance.md#Concepts.DBMaintenance)\.  |  **CLI option:** `--preferred-maintenance-window` **RDS API parameter:** `PreferredMaintenanceWindow`  |  The change occurs immediately\. This setting ignores the apply immediately setting\.   |  If there are one or more pending actions that cause downtime, and the maintenance window is changed to include the current time, those pending actions are applied immediately and downtime occurs\.  | 
|  Manage master credentials in AWS Secrets Manager  |  Select **Manage master credentials in AWS Secrets Manager** to manage the master user password in a secret in Secrets Manager\. Optionally, choose a KMS key to use to protect the secret\. Choose from the KMS keys in your account, or enter the key from a different account\. If RDS is already managing the master user password for the DB cluster, you can rotate the master user password by choosing **Rotate secret immediately**\. For more information, see [Password management with Amazon RDS and AWS Secrets Manager](rds-secrets-manager.md)\.  |  **CLI option:** `--manage-master-user-password \| --no-manage-master-user-password` `--master-user-secret-kms-key-id` `--rotate-master-user-password \| --no-rotate-master-user-password` **RDS API parameter:** `ManageMasterUserPassword` `MasterUserSecretKmsKeyId` `RotateMasterUserPassword`  |  If you are turning on or turning off automatic master user password management, the change occurs immediately\. This change ignores the apply immediately setting\. If you are rotating the master user password, you must specify that the change is applied immediately\.  |  Downtime doesn't occur during this change\.  | 
|  New master password  |  The password for your master user account\.  |  **CLI option:** `--master-user-password` **RDS API parameter:** `MasterUserPassword`  |  The change is applied asynchronously, as soon as possible\. This setting ignores the apply immediately setting\.   |  Downtime doesn't occur during this change\.  | 
|  Provisioned IOPS  |  The amount of Provisioned IOPS \(input/output operations per second\) to be initially allocated for the DB cluster\. This setting is available only if Provisioned IOPS \(`io1`\) is selected as the storage type\. For more information, see [Provisioned IOPS SSD storage](CHAP_Storage.md#USER_PIOPS)\.   |  **CLI option:** `--iops` **RDS API parameter:** `Iops`  |  If you choose to apply the change immediately, it occurs immediately\. If you don't choose to apply the change immediately, it occurs during the next maintenance window\.  |  Downtime doesn't occur during this change\.  | 
|  Public access  |  **Publicly accessible** to give the DB cluster a public IP address, meaning that it's accessible outside its virtual private cloud \(VPC\)\. To be publicly accessible, the DB cluster also has to be in a public subnet in the VPC\. **Not publicly accessible** to make the DB cluster accessible only from inside the VPC\. For more information, see [Hiding a DB instance in a VPC from the internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\. To connect to a DB cluster from outside of its VPC, the DB cluster must be publicly accessible\. Also, access must be granted using the inbound rules of the DB cluster's security group, and other requirements must be met\. For more information, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.   If your DB cluster isn't publicly accessible, you can use an AWS Site\-to\-Site VPN connection or an AWS Direct Connect connection to access it from a private network\. For more information, see [Internetwork traffic privacy](inter-network-traffic-privacy.md)\.  |  **CLI option:** `--publicly-accessible` `--no-publicly-accessible` **RDS API parameter:** `PubliclyAccessible`  |  The change occurs immediately\. This setting ignores the apply immediately setting\.  |  An outage doesn't occur during this change\.  | 
|  VPC security group  |  The security groups to associate with the DB cluster\. For more information, see [Overview of VPC security groups](Overview.RDSSecurityGroups.md#Overview.RDSSecurityGroups.VPCSec)\.  |  **CLI option:** `--vpc-security-group-ids` **RDS API parameter:** `VpcSecurityGroupIds`  |  The change is applied asynchronously, as soon as possible\. This setting ignores the apply immediately setting\.   |  An outage doesn't occur during this change\.  | 

## Settings that don't apply when modifying Multi\-AZ DB clusters<a name="modify-multi-az-db-cluster-settings-not-applicable"></a>

The following settings in the AWS CLI command [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and the RDS API operation [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) don't apply to Multi\-AZ DB clusters\.

You also can't modify these settings for Multi\-AZ DB clusters in the console\.


****  

| AWS CLI setting | RDS API setting | 
| --- | --- | 
|  `--allow-major-version-upgrade\|--no-allow-major-version-upgrade`  |  `AllowMajorVersionUpgrade`  | 
|  `--backtrack-window`  |  `BacktrackWindow`  | 
|  `--cloudwatch-logs-export-configuration`  |  `CloudwatchLogsExportConfiguration`  | 
|  `--copy-tags-to-snapshot \| --no-copy-tags-to-snapshot`  |  `CopyTagsToSnapshot`  | 
|  `--db-instance-parameter-group-name`  |  `DBInstanceParameterGroupName`  | 
|  `--domain`  |  `Domain`  | 
|  `--domain-iam-role-name`  |  `DomainIAMRoleName`  | 
|  `--enable-global-write-forwarding \| --no-enable-global-write-forwarding`  |  `EnableGlobalWriteForwarding`  | 
|  `--enable-http-endpoint \| --no-enable-http-endpoint`  |  `EnableHttpEndpoint`  | 
|  `--enable-iam-database-authentication \| --no-enable-iam-database-authentication`  |  `EnableIAMDatabaseAuthentication`  | 
|  `--option-group-name`  |  `OptionGroupName`  | 
|  `--port`  |  `Port`  | 
|  `--scaling-configuration`  |  `ScalingConfiguration`  | 
|  `--storage-type`  |  `StorageType`  | 