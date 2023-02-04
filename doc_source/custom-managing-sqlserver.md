# Managing an Amazon RDS Custom for SQL Server DB instance<a name="custom-managing-sqlserver"></a>

Amazon RDS Custom for SQL Server supports a subset of the usual management tasks for Amazon RDS DB instances\. Following, you can find instructions for the supported RDS Custom for SQL Server management tasks using the AWS Management Console and the AWS CLI\.

**Topics**
+ [Working with high availability features for RDS Custom for SQL Server](#custom-managing.AO)
+ [Pausing and resuming RDS Custom automation](#custom-managing-sqlserver.pausing)
+ [Modifying an RDS Custom for SQL Server DB instance](#custom-managing.modify-sqlserver)
+ [Modifying the storage for an RDS Custom for SQL Server DB instance](#custom-managing-sqlserver.storage-modify)
+ [Tagging RDS Custom for SQL Server resources](#custom-managing-sqlserver.tagging)
+ [Deleting an RDS Custom for SQL Server DB instance](#custom-managing-sqlserver.deleting)

## Working with high availability features for RDS Custom for SQL Server<a name="custom-managing.AO"></a>

To support replication between RDS Custom for SQL Server instances, you can configure high availability \(HA\) with Always On Availability Groups \(AGs\)\. The primary DB instance automatically synchronizes data to the standby instances\.

You can configure your high availability environment in the following ways:
+ Configure standby instances in different Availability Zones \(AZs\) to be resilient to AZ failures\.
+ Place your standby databases in mounted or read\-only mode\.
+ Fail over or switch over from the primary database to a standby database with no data loss\.
+ Migrate data by configuring high availability for your on\-premises instance, and then failing over or switching over to the RDS Custom standby database\.
+ Use a virtual private network \(VPN\) tunnel to encrypt data in transit for your high availability instances\. Encryption in transit isn't configured automatically by RDS Custom\.
+ Configure Always On AGs to monitor your high availability instances\.
+ Allow the observer to perform automatic failover when necessary conditions are met\.

You can also use other encryption technology, such as Secure Sockets Layer \(SSL\), to encrypt data in transit\.

You're responsible for configuring VPC flow logs and CloudWatch alarms to monitor traffic mirroring to prevent data leakage\.

## Pausing and resuming RDS Custom automation<a name="custom-managing-sqlserver.pausing"></a>

RDS Custom automatically provides monitoring and instance recovery for an RDS Custom for SQL Server DB instance\. If you need to customize the instance, do the following:

1. Pause RDS Custom automation for a specified period\. The pause ensures that your customizations don't interfere with RDS Custom automation\.

1. Customize the RDS Custom for SQL Server DB instance as needed\.

1. Do either of the following:
   + Resume automation manually\.
   + Wait for the pause period to end\. In this case, RDS Custom resumes monitoring and instance recovery automatically\.

**Important**  
Pausing and resuming automation are the only supported automation tasks when modifying an RDS Custom for SQL Server DB instance\.

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

### AWS CLI<a name="custom-managing-sqlserver.pausing.CLI"></a>

To pause or resume RDS Custom automation, use the `modify-db-instance` AWS CLI command\. Identify the DB instance using the required parameter `--db-instance-identifier`\. Control the automation mode with the following parameters:
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
+ Any storage volumes that you attach manually to your RDS Custom DB instance are outside the support perimeter\.

  For more information, see [RDS Custom support perimeter and unsupported configurations](custom-troubleshooting.md#custom-troubleshooting.support-perimeter)\.

### Console<a name="custom-managing.modify-sqlserver.CON"></a>

**To modify an RDS Custom for SQL Server DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to modify\.

1. Choose **Modify**\.

1. Make the following changes as needed:

   1. For **DB engine version**, choose the new version\.

   1. Change the value for **DB instance class**\. For supported classes, see  [DB instance class support for RDS Custom for SQL Server](custom-reqs-limits-MS.md#custom-reqs-limits.instancesMS)

   1. Change the value for **Backup retention period**\.

   1. For **Backup window**, set values for the **Start time** and **Duration**\.

   1. For **DB instance maintenance window**, set values for the **Start day**, **Start time**, and **Duration**\.

1. Choose **Continue**\.

1. Choose **Apply immediately** or **Apply during the next scheduled maintenance window**\.

1. Choose **Modify DB instance**\.

### AWS CLI<a name="custom-managing.modify-sqlserver.CLI"></a>

To modify an RDS Custom for SQL Server DB instance, use the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) AWS CLI command\. Set the following parameters as needed:
+ `--db-instance-class` – For supported classes, see  [DB instance class support for RDS Custom for SQL Server](custom-reqs-limits-MS.md#custom-reqs-limits.instancesMS)
+ `--engine-version` – The version number of the database engine to which you're upgrading\.
+ `--backup-retention-period` – How long to retain automated backups, from 0–35 days\.
+ `--preferred-backup-window` – The daily time range during which automated backups are created\.
+ `--preferred-maintenance-window` – The weekly time range \(in UTC\) during which system maintenance can occur\.
+ `--apply-immediately` – Use `--apply-immediately` to apply the storage changes immediately\.

  Or use `--no-apply-immediately` \(the default\) to apply the changes during the next maintenance window\.

## Modifying the storage for an RDS Custom for SQL Server DB instance<a name="custom-managing-sqlserver.storage-modify"></a>

Modifying storage for an RDS Custom for SQL Server DB instance is similar to modifying storage for an Amazon RDS DB instance, but you can only do the following:
+ Increase the allocated storage size\.
+ Change the storage type\. For example, you can modify the the storage type from io1 to gp2, or gp2 to io1\.
+ Change the provisioned IOPS, if you're using the volume types that supports provisioned IOPS, such as io1\.

The following limitations apply to modifying the storage for an RDS Custom for SQL Server DB instance:
+ The minimum allocated storage size for RDS Custom for SQL Server is 20 GiB, and the maximum supported storage size is 16 TiB\.
+ As with Amazon RDS, you can't decrease the allocated storage\. This is a limitation of Amazon Elastic Block Store \(Amazon EBS\) volumes\. For more information, see [Working with storage for Amazon RDS DB instances](USER_PIOPS.StorageTypes.md)
+ Storage autoscaling isn't supported for RDS Custom for SQL Server DB instances\.
+ Any storage volumes that you manually attach to your RDS Custom DB instance are not considered for storage scaling\. Only the RDS\-provided default data volumes, i\.e\., the D drive, are considered for storage scaling\.

  For more information, see [RDS Custom support perimeter and unsupported configurations](custom-troubleshooting.md#custom-troubleshooting.support-perimeter)\.
+ Scaling storage usually doesn't cause any outage or performance degradation of the DB instance\. After you modify the storage size for a DB instance, the status of the DB instance is **storage\-optimization**\.
+ Storage optimization can take several hours\. You can't make further storage modifications for either six \(6\) hours or until storage optimization has completed on the instance, whichever is longer\. For more information, see [Working with storage for Amazon RDS DB instances](USER_PIOPS.StorageTypes.md)

For more information about storage, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.

For general information about storage modification, see [Working with storage for Amazon RDS DB instances](USER_PIOPS.StorageTypes.md)\.

### Console<a name="custom-managing.storage-modify.CON"></a>

**To modify the storage for an RDS Custom for SQL Server DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to modify\.

1. Choose **Modify**\.

1. Make the following changes as needed:

   1. Enter a new value for **Allocated storage**\. It must be greater than the current value, and from 20 GiB–16 TiB\.

   1. Change the value for **Storage type**\. You can use available storage types like General Purpose \(gp2\) or Provisioned IOPS \(io1\) storage\.

   1. If you are specifying volume types that support provisioned IOPS, you can define the **Provisioned IOPS** value\.

1. Choose **Continue**\.

1. Choose **Apply immediately** or **Apply during the next scheduled maintenance window**\.

1. Choose **Modify DB instance**\.

### AWS CLI<a name="custom-managing-sqlserver.storage-modify.CLI"></a>

To modify the storage for an RDS Custom for SQL Server DB instance, use the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) AWS CLI command\. Set the following parameters as needed:
+ `--allocated-storage` – Amount of storage to be allocated for the DB instance, in gibibytes\. It must be greater than the current value, and from 20–16,384 GiB\.
+ `--storage-type` – The storage type, for example, gp2 or io1\.
+ `--iops` – Provisioned IOPS for the DB instance\. You can specify this only for storage types that support provisioned IOPS, like io1\.
+ `--apply-immediately` – Use `--apply-immediately` to apply the storage changes immediately\.

  Or use `--no-apply-immediately` \(the default\) to apply the changes during the next maintenance window\.

The following example changes the storage size of my\-custom\-instance to 200 GiB, storage type to io1, and Provisioned IOPS to 3000\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier my-custom-instance \
    --storage-type io1 \
    --iops 3000 \
    --allocated-storage 200 \
    --apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier my-custom-instance ^
    --storage-type io1 ^
    --iops 3000 ^
    --allocated-storage 200 ^
    --apply-immediately
```

## Tagging RDS Custom for SQL Server resources<a name="custom-managing-sqlserver.tagging"></a>

You can tag RDS Custom resources as with Amazon RDS resources, but with some important differences:
+ Don't create or modify the `AWSRDSCustom` tag that's required for RDS Custom automation\. If you do, you might break the automation\.
+ Tags added to RDS Custom DB instances during creation are propagated to all other related RDS Custom resources\.
+ Tags aren't propagated when you add them to RDS Custom resources after DB instance creation\.

For general information about resource tagging, see [Tagging Amazon RDS resources](USER_Tagging.md)\.

## Deleting an RDS Custom for SQL Server DB instance<a name="custom-managing-sqlserver.deleting"></a>

To delete an RDS Custom for SQL Server DB instance, do the following:
+ Provide the name of the DB instance\.
+ Choose or clear the option to take a final DB snapshot of the DB instance\.
+ Choose or clear the option to retain automated backups\.

You can delete an RDS Custom for SQL Server DB instance using the console or the CLI\. The time required to delete the DB instance can vary depending on the backup retention period \(that is, how many backups to delete\), how much data is deleted, and whether a final snapshot is taken\.

**Note**  
You can't create a final DB snapshot of your DB instance if it has a status of `creating`, `failed`, `incompatible-create`, `incompatible-restore`, or `incompatible-network`\. For more information, see [Viewing Amazon RDS DB instance status](accessing-monitoring.md#Overview.DBInstance.Status)\.

**Important**  
When you choose to take a final snapshot, we recommend that you avoid writing data to your DB instance while the DB instance deletion is in progress\. Once the DB instance deletion is initiated, data changes are not guaranteed to be captured by the final snapshot\.

### Console<a name="custom-managing-sqs.deleting.console"></a>

**To delete an RDS Custom DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the RDS Custom for SQL Server DB instance that you want to delete\. RDS Custom for SQL Server DB instances show the role **Instance \(RDS Custom for SQL Server\)**\.

1. For **Actions**, choose **Delete**\.

1. To take a final snapshot, choose **Create final snapshot**, and provide a name for the **Final snapshot name**\.

1. To retain automated backups, choose **Retain automated backups**\.

1. Enter **delete me** in the box\.

1. Choose **Delete**\.

### AWS CLI<a name="custom-managing-sqs.deleting.CLI"></a>

You delete an RDS Custom for SQL Server DB instance by using the [delete\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-instance.html) AWS CLI command\. Identify the DB instance using the required parameter `--db-instance-identifier`\. The remaining parameters are the same as for an Amazon RDS DB instance\.

The following example deletes the RDS Custom for SQL Server DB instance named `my-custom-instance`, takes a final snapshot, and retains automated backups\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds delete-db-instance \
    --db-instance-identifier my-custom-instance \
    --no-skip-final-snapshot \
    --final-db-snapshot-identifier my-custom-instance-final-snapshot \
    --no-delete-automated-backups
```
For Windows:  

```
aws rds delete-db-instance ^
    --db-instance-identifier my-custom-instance ^
    --no-skip-final-snapshot ^
    --final-db-snapshot-identifier my-custom-instance-final-snapshot ^
    --no-delete-automated-backups
```

To take a final snapshot, the `--final-db-snapshot-identifier` option is required and must be specified\.

To skip the final snapshot, specify the `--skip-final-snapshot` option instead of the `--no-skip-final-snapshot` and `--final-db-snapshot-identifier` options in the command\.

To delete automated backups, specify the `--delete-automated-backups` option instead of the `--no-delete-automated-backups` option in the command\.