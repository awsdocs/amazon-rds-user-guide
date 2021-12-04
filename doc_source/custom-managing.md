# Managing an Amazon RDS Custom DB instance<a name="custom-managing"></a>

Amazon RDS Custom supports a subset of the usual management tasks for Amazon RDS DB instances\. Following, you can find instructions for the supported RDS Custom management tasks using the AWS Management Console and the AWS CLI\.

**Topics**
+ [Working with high availability features for RDS Custom for Oracle](#custom-managing.ha)
+ [Working with high availability features for RDS Custom for SQL Server](#custom-managing.AO)
+ [Pausing and resuming RDS Custom automation](#custom-managing.pausing)
+ [Modifying an RDS Custom for SQL Server DB instance](#custom-managing.modify-sqlserver)
+ [Modifying the storage for an RDS Custom for Oracle DB instance](#custom-managing.storage-modify)
+ [Changing the time zone of an RDS Custom for Oracle DB instance](#custom-managing.timezone)
+ [Support for Transparent Data Encryption](#custom-managing.tde)
+ [Tagging RDS Custom resources](#custom-managing.tagging)
+ [Deleting an RDS Custom DB instance](#custom-managing.deleting)

## Working with high availability features for RDS Custom for Oracle<a name="custom-managing.ha"></a>

To support replication between RDS Custom for Oracle instances, you can configure high availability \(HA\) with Oracle Data Guard\. The primary DB instance automatically synchronizes data to the standby instances\.

You can configure your high availability environment in the following ways:
+ Configure standby instances in different Availability Zones \(AZs\) to be resilient to AZ failures\.
+ Place your standby databases in mounted or read\-only mode\.
+ Fail over or switch over from the primary database to a standby database with no data loss\.
+ Migrate data by configuring high availability for your on\-premises instance, and then failing over or switching over to the RDS Custom standby database\.

To learn how to configure high availability, see the whitepaper [Enabling high availability with Data Guard on Amazon RDS Custom for Oracle](https://d1.awsstatic.com/whitepapers/enabling-high-availability-with-data-guard-on-amazon-rds-custom-for-oracle.pdf)\. You can perform the following tasks:
+ Use a virtual private network \(VPN\) tunnel to encrypt data in transit for your high availability instances\. Encryption in transit isn't configured automatically by RDS Custom\.
+ Configure Oracle Fast\-Failover Observer \(FSFO\) to monitor your high availability instances\.
+ Allow the observer to perform automatic failover when necessary conditions are met\.

## Working with high availability features for RDS Custom for SQL Server<a name="custom-managing.AO"></a>

To support replication between RDS Custom for SQL Server instances, you can configure high availability \(HA\) with Always On Availability Groups \(AGs\)\. The primary DB instance automatically synchronizes data to the standby instances\.

You can configure your high availability environment in the following ways:
+ Configure standby instances in different Availability Zones \(AZs\) to be resilient to AZ failures\.
+ Place your standby databases in mounted or read\-only mode\.
+ Fail over or switch over from the primary database to a standby database with no data loss\.
+ Migrate data by configuring high availability for your on\-premises instance, and then failing over or switching over to the RDS Custom standby database\.

To learn how to configure high availability, see the blog post *Configuring high availability with Always On Availability Groups on Amazon RDS Custom for SQL Server*\. You can perform the following tasks:
+ Use a virtual private network \(VPN\) tunnel to encrypt data in transit for your high availability instances\. Encryption in transit isn't configured automatically by RDS Custom\.
+ Configure Always On AGs to monitor your high availability instances\.
+ Allow the observer to perform automatic failover when necessary conditions are met\.

You can also use other encryption technology, such as Secure Sockets Layer \(SSL\), to encrypt data in transit\.

You're responsible for configuring VPC flow logs and CloudWatch alarms to monitor traffic mirroring to prevent data leakage\.

## Pausing and resuming RDS Custom automation<a name="custom-managing.pausing"></a>

RDS Custom automatically provides monitoring and instance recovery for an RDS Custom DB instance\. If you need to customize the instance, do the following:

1. Pause RDS Custom automation for a specified period\. The pause ensures that your customizations don't interfere with RDS Custom automation\.

1. Customize the RDS Custom DB instance as needed\.

1. Do either of the following:
   + Resume automation manually\.
   + Wait for the pause period to end\. In this case, RDS Custom resumes monitoring and instance recovery automatically\.

**Important**  
Pausing and resuming automation are the only supported automation tasks when modifying a DB instance\.

### Console<a name="custom-managing.pausing.console"></a>

**To pause or resume RDS Custom automation**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the RDS Custom DB instance that you want to modify\.

1. Choose **Modify**\. The **Modify DB instance** page appears\.

1. For **RDS Custom automation mode**, choose one of the following options:
   + **Paused** pauses the monitoring and instance recovery for the RDS Custom DB instance\. Enter the pause duration that you want \(in minutes\) for **Automation mode duration**\. The minimum value is 60 minutes \(default\)\. The maximum value is 1,440 minutes\.
   + **Full automation** resumes automation\.

1. Choose **Continue** to check the summary of modifications\.

   A message indicates that RDS Custom will apply the changes immediately\.

1. If your changes are correct, choose **Modify DB instance**\. Or choose **Back** to edit your changes or **Cancel** to cancel your changes\.

   On the RDS console, the details for the modification appear\. If you paused automation, the **Status** of your RDS Custom DB instance indicates **Automation paused**\.

1. \(Optional\) In the navigation pane, choose **Databases**, and then your RDS Custom DB instance\.

   In the **Summary** pane, **RDS Custom automation mode** indicates the automation status\. If automation is paused, the value is **Paused\. Automation resumes in *num* minutes**\.

### AWS CLI<a name="custom-managing.pausing.CLI"></a>

To pause or resume RDS Custom automation, use the [modify\-db\-instance]() AWS CLI command\. Identify the DB instance using the required parameter `--db-instance-identifier`\. Control the automation mode with the following parameters:
+ `--automation-mode` specifies the pause state of the DB instance\. Valid values are `all-paused`, which pauses automation, and `full`, which resumes it\.
+ `--resume-full-automation-mode-minutes` specifies the duration of the pause\. The default value is 60 minutes\.

**Note**  
Regardless of whether you specify `--no-apply-immediately` or `--apply-immediately`, RDS Custom applies modifications asynchronously as soon as possible\.

In the command response, `ResumeFullAutomationModeTime` indicates the resume time as a UTC timestamp\. When the automation mode is `all-paused`, you can use `modify-db-instance` to resume automation mode or extend the pause period\. No other `modify-db-instance` options are supported\.

The following example pauses automation for `my-custom-instance` for 90 minutes\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier my-custom-instance \
    --automation-mode all-paused \
    --resume-full-automation-mode-minutes 90
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier my-custom-instance ^
    --automation-mode all-paused ^
    --resume-full-automation-mode-minutes 90
```

The following example extends the pause duration for an extra 30 minutes\. The 30 minutes is added to the original time shown in `ResumeFullAutomationModeTime`\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier my-custom-instance \
    --automation-mode all-paused \
    --resume-full-automation-mode-minutes 30
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier my-custom-instance ^
    --automation-mode all-paused ^
    --resume-full-automation-mode-minutes 30
```

The following example resumes full automation for `my-custom-instance`\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier my-custom-instance \
    --automation-mode full \
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier my-custom-instance ^
    --automation-mode full
```
In the following partial sample output, the pending `AutomationMode` value is `full`\.  

```
{
    "DBInstance": {
        "PubliclyAccessible": true,
        "MasterUsername": "admin",
        "MonitoringInterval": 0,
        "LicenseModel": "bring-your-own-license",
        "VpcSecurityGroups": [
            {
                "Status": "active",
                "VpcSecurityGroupId": "0123456789abcdefg"
            }
        ],
        "InstanceCreateTime": "2020-11-07T19:50:06.193Z",
        "CopyTagsToSnapshot": false,
        "OptionGroupMemberships": [
            {
                "Status": "in-sync",
                "OptionGroupName": "default:custom-oracle-ee-19"
            }
        ],
        "PendingModifiedValues": {
            "AutomationMode": "full"
        },
        "Engine": "custom-oracle-ee",
        "MultiAZ": false,
        "DBSecurityGroups": [],
        "DBParameterGroups": [
            {
                "DBParameterGroupName": "default.custom-oracle-ee-19",
                "ParameterApplyStatus": "in-sync"
            }
        ],
        ...
        "ReadReplicaDBInstanceIdentifiers": [],
        "AllocatedStorage": 250,
        "DBInstanceArn": "arn:aws:rds:us-west-2:012345678912:db:my-custom-instance",
        "BackupRetentionPeriod": 3,
        "DBName": "ORCL",
        "PreferredMaintenanceWindow": "fri:10:56-fri:11:26",
        "Endpoint": {
            "HostedZoneId": "ABCDEFGHIJKLMNO",
            "Port": 8200,
            "Address": "my-custom-instance.abcdefghijk.us-west-2.rds.amazonaws.com"
        },
        "DBInstanceStatus": "automation-paused",
        "IAMDatabaseAuthenticationEnabled": false,
        "AutomationMode": "all-paused",
        "EngineVersion": "19.my_cev1",
        "DeletionProtection": false,
        "AvailabilityZone": "us-west-2a",
        "DomainMemberships": [],
        "StorageType": "gp2",
        "DbiResourceId": "db-ABCDEFGHIJKLMNOPQRSTUVW",
        "ResumeFullAutomationModeTime": "2020-11-07T20:56:50.565Z",
        "KmsKeyId": "arn:aws:kms:us-west-2:012345678912:key/aa111a11-111a-11a1-1a11-1111a11a1a1a",
        "StorageEncrypted": false,
        "AssociatedRoles": [],
        "DBInstanceClass": "db.m5.xlarge",
        "DbInstancePort": 0,
        "DBInstanceIdentifier": "my-custom-instance",
        "TagList": []
    }
```

## Modifying an RDS Custom for SQL Server DB instance<a name="custom-managing.modify-sqlserver"></a>

Modifying an RDS Custom for SQL Server DB instance is similar to doing this for Amazon RDS, but the changes that you can make are limited to the following:
+ Changing the DB instance class
+ Changing the backup retention period and backup window
+ Changing the maintenance window
+ Upgrading the DB engine version when a new version becomes available

The following limitations apply to modifying an RDS Custom for SQL Server DB instance:
+ Multi\-AZ deployments aren't supported\.
+ Custom DB option and parameter groups aren't supported\.
+ You can't modify the allocated storage\.
+ Any storage volumes that you attach manually to your RDS Custom DB instance are outside the support perimeter\.

  For more information, see [Responding to an unsupported configuration](custom-troubleshooting.md#custom-troubleshooting.support-perimeter)\.

### Console<a name="custom-managing.modify-sqlserver.CON"></a>

**To modify an RDS Custom for SQL Server DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to modify\.

1. Choose **Modify**\.

1. Make the following changes as needed:

   1. For **DB engine version**, choose the new version\.

   1. Change the value for **DB instance class**\. For supported classes, see [DB instance class support for RDS Custom](custom-reqs-limits.md#custom-reqs-limits.instances)\.

   1. Change the value for **Backup retention period**\.

   1. For **Backup window**, set values for the **Start time** and **Duration**\.

   1. For **DB instance maintenance window**, set values for the **Start day**, **Start time**, and **Duration**\.

1. Choose **Continue**\.

1. Choose **Apply immediately** or **Apply during the next scheduled maintenance window**\.

1. Choose **Modify DB instance**\.

### AWS CLI<a name="custom-managing.modify-sqlserver.CLI"></a>

To modify an RDS Custom for SQL Server DB instance, use the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) AWS CLI command\. Set the following parameters as needed:
+ `--db-instance-class` – For supported classes, see [DB instance class support for RDS Custom](custom-reqs-limits.md#custom-reqs-limits.instances)\.
+ `--engine-version` – The version number of the database engine to which you're upgrading\.
+ `--backup-retention-period` – How long to retain automated backups, from 0–35 days\.
+ `--preferred-backup-window` – The daily time range during which automated backups are created\.
+ `--preferred-maintenance-window` – The weekly time range \(in UTC\) during which system maintenance can occur\.
+ `--apply-immediately` – Use `--apply-immediately` to apply the storage changes immediately\.

  Or use `--no-apply-immediately` \(the default\) to apply the changes during the next maintenance window\.

## Modifying the storage for an RDS Custom for Oracle DB instance<a name="custom-managing.storage-modify"></a>

Modifying the storage for an RDS Custom for Oracle DB instance is similar to doing this for Amazon RDS, but the changes that you can make are limited to the following:
+ Increasing the allocated storage
+ Changing the storage type from io1 to gp2, or gp2 to io1
+ Changing the Provisioned IOPS, if you're using the io1 storage type

The following limitations apply to modifying the storage for an RDS Custom for Oracle DB instance:
+ The minimum allocated storage for RDS Custom for Oracle is 40 GiB, and the maximum is 64 TiB\.
+ As with Amazon RDS, you can't decrease the allocated storage\. This is a limitation of Amazon EBS volumes\.
+ Storage autoscaling isn't supported for RDS Custom DB instances\.
+ Any storage volumes that you attach manually to your RDS Custom DB instance are outside the support perimeter\.

  For more information, see [Responding to an unsupported configuration](custom-troubleshooting.md#custom-troubleshooting.support-perimeter)\.
+ Magnetic \(standard\) storage isn't supported for RDS Custom\.

For more information about storage, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.

For general information on storage modification, see [Working with storage for Amazon RDS DB instances](USER_PIOPS.StorageTypes.md)\. The following procedures are specific to RDS Custom\.

### Console<a name="custom-managing.storage-modify.CON"></a>

**To modify the storage for an RDS Custom for Oracle DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to modify\.

1. Choose **Modify**\.

1. Make the following changes as needed:

   1. Enter a new value for **Allocated storage**\. It must be greater than the current value, and from 40 GiB–64 TiB\.

   1. Change the value for **Storage type**\. You can use General Purpose \(gp2\) or Provisioned IOPS \(io1\) storage\.

   1. If using io1 storage, you can change the **Provisioned IOPS** value\.

1. Choose **Continue**\.

1. Choose **Apply immediately** or **Apply during the next scheduled maintenance window**\.

1. Choose **Modify DB instance**\.

### AWS CLI<a name="custom-managing.storage-modify.CLI"></a>

To modify the storage for an RDS Custom for Oracle DB instance, use the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) AWS CLI command\. Set the following parameters as needed:
+ `--allocated-storage` – Amount of storage to be allocated for the DB instance, in gibibytes\. It must be greater than the current value, and from 40–65,536 GiB\.
+ `--storage-type` – The storage type, gp2 or io1\.
+ `--iops` – Provisioned IOPS for the DB instance, if using the io1 storage type\.
+ `--apply-immediately` – Use `--apply-immediately` to apply the storage changes immediately\.

  Or use `--no-apply-immediately` \(the default\) to apply the changes during the next maintenance window\.

## Changing the time zone of an RDS Custom for Oracle DB instance<a name="custom-managing.timezone"></a>

You change the time zone of an RDS Custom for Oracle DB instance manually, unlike RDS for Oracle where you use the `TIME_ZONE` option in a custom DB option group\.

You can change time zones for RDS Custom for Oracle DB instances multiple times, but we recommend not changing them more than once every 48 hours\. We also recommend changing them only when the latest restorable time is within the last 30 minutes\.

If you don't follow these recommendations, cleaning up redo logs might remove more logs than intended\. Redo log timestamps might also be converted incorrectly to UTC, which can prevent the redo logs from being downloaded and replayed correctly\. This in turn can prevent point\-in\-time recovery \(PITR\) from performing correctly\.

Changing the time zone of an RDS Custom for Oracle DB instance has the following limitations:
+ PITR is supported for recovery times before RDS Custom automation is paused, and after automation is resumed\.

  For more information on PITR, see [Restoring an RDS Custom instance to a point in time](custom-backup.md#custom-backup.pitr)\.
+ Changing the time zone of an existing read replica causes downtime\. We recommend changing the time zone of the DB instance before creating read replicas\.

  You can create a read replica from a DB instance with a modified time zone\. For more information on read replicas, see [Working with read replicas for RDS Custom for Oracle](custom-rr.md)\.

Use the following procedures to change the time zone of an RDS Custom for Oracle DB instance\.

Make sure to follow these procedures\. If they aren't followed, it can result in these issues:
+ Disruption of redo log download and replay
+ Incorrect redo log timestamps 
+ Cleanup of redo logs on the host that doesn't use the redo log retention period

**To change the time zone for a primary DB instance**

1. Pause RDS Custom automation\. For more information, see [Pausing and resuming RDS Custom automation](#custom-managing.pausing)\.

1. \(Optional\) Change the time zone of the DB instance, for example by using the following command\.

   ```
   ALTER DATABASE SET TIME_ZONE = 'US/Pacific';
   ```

1. Shut down the DB instance\.

1. Log in to the host and change the system time zone\.

1. Start the DB instance\.

1. Resume RDS Custom automation\.

**To change the time zone for a primary DB instance and its read replicas**

1. Pause RDS Custom automation on the primary DB instance\.

1. Pause RDS Custom automation on the read replicas\.

1. \(Optional\) Change the time zone of the primary and read replicas, for example by using the following command\.

   ```
   ALTER DATABASE SET TIME_ZONE = 'US/Pacific';
   ```

1. Shut down the primary DB instance\.

1. Shut down the read replicas\.

1. Log in to the host and change the system time zone for the read replicas\.

1. Change the system time zone for the primary DB instance\.

1. Mount the read replicas\.

1. Start the primary DB instance\.

1. Resume RDS Custom automation on the primary DB instance and then on the read replicas\.

## Support for Transparent Data Encryption<a name="custom-managing.tde"></a>

RDS Custom supports Transparent Data Encryption \(TDE\) for RDS Custom for Oracle and RDS Custom for SQL Server DB instances\.

However, you can't enable TDE using an option in a custom option group as you can in RDS for Oracle or RDS for SQL Server\. You turn on TDE manually\. For information about using Oracle Transparent Data Encryption, see [Securing stored data using Transparent Data Encryption](http://docs.oracle.com/cd/E11882_01/network.112/e40393/asotrans.htm#BABFGJAG) in the Oracle documentation\. For information about Transparent Data Encryption for SQL Server, see [Transparent Data Encryption \(TDE\)](http://msdn.microsoft.com/en-us/library/bb934049.aspx) in the Microsoft documentation\.

## Tagging RDS Custom resources<a name="custom-managing.tagging"></a>

You can tag RDS Custom resources as with Amazon RDS resources, but with some important differences:
+ Don't create or modify the `AWSRDSCustom` tag that's required for RDS Custom automation\. If you do, you might break the automation\.
+ Tags added to RDS Custom DB instances during creation are propagated to all other related RDS Custom resources\.
+ Tags aren't propagated when you add them to RDS Custom resources after DB instance creation\.

For general information on resource tagging, see [Tagging Amazon RDS resources](USER_Tagging.md)\.

## Deleting an RDS Custom DB instance<a name="custom-managing.deleting"></a>

To delete an RDS Custom DB instance, do the following:
+ Provide the name of the DB instance\.
+ Clear the option to take a final DB snapshot of the DB instance\.
+ Choose or clear the option to retain automated backups\.

You can delete an RDS Custom DB instance using the console or the CLI\. The time required to delete the DB instance can vary depending on the backup retention period \(that is, how many backups to delete\) and how much data is deleted\.

### Console<a name="custom-managing.deleting.console"></a>

**To delete an RDS Custom DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the RDS Custom DB instance that you want to delete\. RDS Custom DB instances show the role **Instance \(RDS Custom\)**\.

1. For **Actions**, choose **Delete**\.

1. To retain automated backups, choose **Retain automated backups**\.

1. Enter **delete me** in the box\.

1. Choose **Delete**\.

### AWS CLI<a name="custom-managing.deleting.CLI"></a>

You delete an RDS Custom DB instance by using the [delete\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-instance.html) AWS CLI command\. Identify the DB instance using the required parameter `--db-instance-identifier`\. The remaining parameters are the same as for an Amazon RDS DB instance, with the following exceptions:
+ `--skip-final-snapshot` is required\.
+ `--no-skip-final-snapshot` isn't supported\.
+ `--final-db-snapshot-identifier` isn't supported\.

The following example deletes the RDS Custom DB instance named `my-custom-instance`, and retains automated backups\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds delete-db-instance \
    --db-instance-identifier my-custom-instance \
    --skip-final-snapshot \
    --no-delete-automated-backups
```
For Windows:  

```
aws rds delete-db-instance ^
    --db-instance-identifier my-custom-instance ^
    --skip-final-snapshot ^
    --no-delete-automated-backups
```