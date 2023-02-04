# Managing an Amazon RDS Custom for Oracle DB instance<a name="custom-managing"></a>

Amazon RDS Custom supports a subset of the usual management tasks for Amazon RDS DB instances\. Following, you can find instructions for the supported RDS Custom for Oracle management tasks using the AWS Management Console and the AWS CLI\.

**Topics**
+ [Working with container databases \(CDBs\) in RDS Custom for Oracle](#custom-managing.multitenant)
+ [Working with high availability features for RDS Custom for Oracle](#custom-managing.ha)
+ [Pausing and resuming RDS Custom automation](#custom-managing.pausing)
+ [Modifying your RDS Custom for Oracle DB instance](#custom-managing.modifying)
+ [Changing the time zone of an RDS Custom for Oracle DB instance](#custom-managing.timezone)
+ [Changing the character set of an RDS Custom for Oracle DB instance](#custom-managing.character-set)
+ [Support for Transparent Data Encryption](#custom-managing.tde)
+ [Tagging RDS Custom for Oracle resources](#custom-managing.tagging)
+ [Deleting an RDS Custom for Oracle DB instance](#custom-managing.deleting)

## Working with container databases \(CDBs\) in RDS Custom for Oracle<a name="custom-managing.multitenant"></a>

You can either create your RDS Custom for Oracle DB instance with the Oracle Multitenant architecture \(`custom-oracle-ee-cdb` engine type\) or with the traditional non\-CDB architecture \(`custom-oracle-ee` engine type\)\. When you create a container database \(CDB\), it contains one pluggable database \(PDB\) and one PDB seed\. You can create additional PDBs manually using Oracle SQL\.

In the RDS Custom for Oracle shared responsibility model, you are responsible for managing PDBs and creating any additional PDBs\. RDS Custom doesn't restrict the number of PDBs\. You can manually create, modify, and delete PDBs by connecting to the CDB root and running a SQL command\. Create PDBs on an Amazon EBS data volume to prevent the DB instance from going outside the support perimeter\.

You can't rename existing PDBs using Amazon RDS APIs\. You also can't rename the CDB using the `modify-db-instance` command\.

To modify your CDBs or PDBs, complete the following steps:

1. Pause automation to prevent interference with RDS Custom actions\.

1. Modify your CDB or PDBs\.

1. Back up any modified PDBs\.

1. Resume automation\.

RDS Custom keeps the CDB root open in the same way as it keeps a non\-CDB open\. If the state of the CDB root changes, the monitoring and recovery automation attempts to recover the CDB root to the desired state\. You receive RDS event notifications when the root CDB is shut down \(`RDS-EVENT-0004`\) or restarted \(`RDS-EVENT-0006`\), similar to the non\-CDB architecture\. RDS Custom attempts to open all PDBs in `READ WRITE` mode at DB instance startup\. If some PDBs can't be opened, RDS Custom publishes the following event: `tenant database shutdown`\. 

## Working with high availability features for RDS Custom for Oracle<a name="custom-managing.ha"></a>

To support replication between RDS Custom for Oracle instances, you can configure high availability \(HA\) with Oracle Data Guard\. The primary DB instance automatically synchronizes data to the standby instances\.

You can configure your high availability environment in the following ways:
+ Configure standby instances in different Availability Zones \(AZs\) to be resilient to AZ failures\.
+ Place your standby databases in mounted or read\-only mode\.
+ Fail over or switch over from the primary database to a standby database with no data loss\.
+ Migrate data by configuring high availability for your on\-premises instance, and then failing over or switching over to the RDS Custom standby database\.

To learn how to configure high availability, see the whitepaper [Build high availability for Amazon RDS Custom for Oracle using read replicas](http://aws.amazon.com/blogs/database/build-high-availability-for-amazon-rds-custom-for-oracle-using-read-replicas/)\. You can perform the following tasks:
+ Use a virtual private network \(VPN\) tunnel to encrypt data in transit for your high availability instances\. Encryption in transit isn't configured automatically by RDS Custom\.
+ Configure Oracle Fast\-Failover Observer \(FSFO\) to monitor your high availability instances\.
+ Allow the observer to perform automatic failover when necessary conditions are met\.

## Pausing and resuming RDS Custom automation<a name="custom-managing.pausing"></a>

RDS Custom automatically provides monitoring and instance recovery for an RDS Custom for Oracle DB instance\. If you need to customize your DB instance, do the following:

1. Pause RDS Custom automation for a specified period\. The pause ensures that your customizations don't interfere with RDS Custom automation\.

1. Customize the RDS Custom for Oracle DB instance as needed\.

1. Do either of the following:
   + Resume automation manually\.
**Important**  
Pausing and resuming automation are the only supported automation tasks when modifying an RDS Custom for Oracle DB instance\.
   + Wait for the pause period to end\. In this case, RDS Custom resumes monitoring and instance recovery automatically\.

In an on\-premises Oracle CDB, you can preserve a specified open mode for PDBs using a built\-in command or after a startup trigger\. This mechanism brings PDBs to a specified state when the CDB restarts\. When opening your CDB, RDS Custom automation always discards any user\-specified preserved states and attempts to open all PDBs\. If RDS Custom can't open all PDBs, the following event is issued: `The following PDBs failed to open: list-of-PDBs`\.

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

## Modifying your RDS Custom for Oracle DB instance<a name="custom-managing.modifying"></a>

Modifying an RDS Custom for Oracle DB instance is similar to modifying an Amazon RDS instance, but you can only do the following:
+ Change the DB instance class\.
+ Increase the allocated storage for your DB instance\.
+ Change the storage type from io1 to gp2, or gp2 to io1\.
+ Change the Provisioned IOPS, if you're using the io1 storage type\.

**Topics**
+ [Requirements and limitations when modifying your DB instance storage](#custom-managing.storage-modify)
+ [Requirements and limitations when modifying your DB instance class](#custom-managing.instance-class-reqs)
+ [How RDS Custom creates your DB instance when you modify the instance class](#custom-managing.instance-class-resources)
+ [Modifying the instance class or storage for your RDS Custom for Oracle DB instance](#custom-managing.modifying.procedure)

### Requirements and limitations when modifying your DB instance storage<a name="custom-managing.storage-modify"></a>

Consider the following requirements and limitations when you modify the storage for an RDS Custom for Oracle DB instance:
+ The minimum allocated storage for RDS Custom for Oracle is 40 GiB, and the maximum is 64 TiB\.
+ As with Amazon RDS, you can't decrease the allocated storage\. This is a limitation of Amazon EBS volumes\.
+ Storage autoscaling isn't supported for RDS Custom DB instances\.
+ Any storage volumes that you attach manually to your RDS Custom DB instance are outside the support perimeter\.

  For more information, see [RDS Custom support perimeter and unsupported configurations](custom-troubleshooting.md#custom-troubleshooting.support-perimeter)\.
+ Magnetic \(standard\) Amazon EBS storage isn't supported for RDS Custom\.

For more information about Amazon EBS storage, see [Amazon RDS DB instance storage](CHAP_Storage.md)\. For general information about storage modification, see [Working with storage for Amazon RDS DB instances](USER_PIOPS.StorageTypes.md)\.

### Requirements and limitations when modifying your DB instance class<a name="custom-managing.instance-class-reqs"></a>

Consider the following requirements and limitations when you modify the instance class for an RDS Custom for Oracle DB instance:
+ Your DB instance must be in the `available` state\.
+ Your DB instance must have a minimum of 100 MiB of free space on the root volume, data volume, and binary volume\.
+ You can assign only a single elastic IP \(EIP\) to your RDS Custom for Oracle DB instance when using the default elastic network interface \(ENI\)\. If you attach multiple ENIs to your DB instance, the modify operation fails\.
+ All RDS Custom for Oracle tags must be present\.
+ If you use RDS Custom for Oracle replication, note the following requirements and limitations:
  + For primary DB instances and read replicas, you can change the instance class for only one DB instance at a time\.
  + If your RDS Custom for Oracle DB instance has an on\-premises primary or replica database, make sure to manually update private IP addresses on the on\-premises DB instance after the modification completes\. This action is necessary to preserve Oracle DataGuard functionality\. RDS Custom for Oracle publishes an event when the modification succeeds\.
  + You can't modify your RDS Custom for Oracle DB instance class when the primary or read replica DB instances have FSFO \(Fast\-Start Failover\) configured\.

### How RDS Custom creates your DB instance when you modify the instance class<a name="custom-managing.instance-class-resources"></a>

When you modify your instance class, RDS Custom creates your DB instance as follows:
+ Creates the Amazon EC2 instance\.
+ Creates the root volume from the latest DB snapshot\. RDS Custom for Oracle doesn't retain information added to the root volume after the latest DB snapshot\.
+ Creates Amazon CloudWatch alarms\.
+ Creates an Amazon EC2 SSH key pair if you have deleted the original key pair\. Otherwise, RDS Custom for Oracle retains the original key pair\.
+ Creates new resources using the tags that are attached to your DB instance when you initiate the modification\. RDS Custom doesn't transfer tags to the new resources when they are attached directly to underlying resources\.
+ Transfers the binary and data volumes with the most recent modifications to the new DB instance\.
+ Transfers the elastic IP address \(EIP\)\. If the DB instance is publicly accessible, then RDS Custom temporarily attaches a public IP address to the new DB instance before transferring the EIP\. If the DB instance isn't publicly accessible, RDS Custom doesn't create public IP addresses\.

### Modifying the instance class or storage for your RDS Custom for Oracle DB instance<a name="custom-managing.modifying.procedure"></a>

You can modify the DB instance class or storage using the console, AWS CLI, or RDS API\.

#### Console<a name="custom-managing.modifying.procedure.CON"></a>

**To modify an RDS Custom for Oracle DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to modify\.

1. Choose **Modify**\.

1. Make the following changes as needed:

   1. Change the value for **DB instance class**\. For supported classes, see [DB instance class support for RDS Custom for Oracle](custom-reqs-limits.md#custom-reqs-limits.instances)\.

   1. Enter a new value for **Allocated storage**\. It must be greater than the current value, and from 40 GiB–64 TiB\.

   1. Change the value for **Storage type**\. You can use General Purpose \(gp2\) or Provisioned IOPS \(io1\) storage\.

   1. If using io1 storage, you can change the **Provisioned IOPS** value\.

1. Choose **Continue**\.

1. Choose **Apply immediately** or **Apply during the next scheduled maintenance window**\.

1. Choose **Modify DB instance**\.

#### AWS CLI<a name="custom-managing.modifying.procedure.CLI"></a>

To modify the storage for an RDS Custom for Oracle DB instance, use the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) AWS CLI command\. Set the following parameters as needed:
+ `--db-instance-class` – A new instance class\. For supported classes, see [DB instance class support for RDS Custom for Oracle](custom-reqs-limits.md#custom-reqs-limits.instances)\.
+ `--allocated-storage` – Amount of storage to be allocated for the DB instance, in gibibytes\. It must be greater than the current value, and from 40–65,536 GiB\.
+ `--storage-type` – The storage type, gp2 or io1\.
+ `--iops` – Provisioned IOPS for the DB instance, if using the io1 storage type\.
+ `--apply-immediately` – Use `--apply-immediately` to apply the storage changes immediately\.

  Or use `--no-apply-immediately` \(the default\) to apply the changes during the next maintenance window\.

The following example changes the DB instance class of my\-custom\-instance to db\.m5\.16xlarge\. The command also changes the storage size to 1 TiB, storage type to io1, and Provisioned IOPS to 3000\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier my-custom-instance \
    --db-instance-class db.m5.16xlarge \
    --storage-type io1 \
    --iops 3000 \
    --allocated-storage 1024 \
    --apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier my-custom-instance ^
    --db-instance-class db.m5.16xlarge ^
    --storage-type io1 ^
    --iops 3000 ^
    --allocated-storage 1024 ^
    --apply-immediately
```

## Changing the time zone of an RDS Custom for Oracle DB instance<a name="custom-managing.timezone"></a>

You change the time zone of an RDS Custom for Oracle DB instance manually\. This approach contrasts with RDS for Oracle, where you use the `TIME_ZONE` option in a custom DB option group\.

You can change time zones for RDS Custom for Oracle DB instances multiple times\. However, we recommend not changing them more than once every 48 hours\. We also recommend changing them only when the latest restorable time is within the last 30 minutes\.

If you don't follow these recommendations, cleaning up redo logs might remove more logs than intended\. Redo log timestamps might also be converted incorrectly to UTC, which can prevent the redo log files from being downloaded and replayed correctly\. This in turn can prevent point\-in\-time recovery \(PITR\) from performing correctly\.

Changing the time zone of an RDS Custom for Oracle DB instance has the following limitations:
+ PITR is supported for recovery times before RDS Custom automation is paused, and after automation is resumed\.

  For more information about PITR, see [Restoring an RDS Custom for Oracle instance to a point in time](custom-backup.md#custom-backup.pitr)\.
+ Changing the time zone of an existing read replica causes downtime\. We recommend changing the time zone of the DB instance before creating read replicas\.

  You can create a read replica from a DB instance with a modified time zone\. For more information about read replicas, see [Working with Oracle replicas for RDS Custom for Oracle](custom-rr.md)\.

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

## Changing the character set of an RDS Custom for Oracle DB instance<a name="custom-managing.character-set"></a>

RDS Custom for Oracle defaults to the character set US7ASCII\. You might want to specify different character sets to meet language or multibyte character requirements\. When you use RDS Custom for Oracle, you can pause automation and then change the character set of your database manually\.

Changing the character set of an RDS Custom for Oracle DB instance has the following requirements:
+ You can only change the character on a newly provisioned RDS Custom instance that has an empty or starter database with no application data\. For all other scenarios, change the character set using DMU \(Database Migration Assistant for Unicode\)\.
+ You can only change to a character set supported by RDS for Oracle\. For more information, see [Supported DB character sets](Appendix.OracleCharacterSets.md#Appendix.OracleCharacterSets.db-character-set.supported)\.

**To change the character set of an RDS Custom for Oracle DB instance**

1. Pause RDS Custom automation\. For more information, see [Pausing and resuming RDS Custom automation](#custom-managing.pausing)\.

1. Log in to your database as a user with `SYSDBA` privileges\.

1. Restart the database in restricted mode, change the character set, and then restart the database in normal mode\.

   Run the following script in your SQL client:

   ```
   SHUTDOWN IMMEDIATE;
   STARTUP RESTRICT;
   ALTER DATABASE CHARACTER SET INTERNAL_CONVERT AL32UTF8;
   SHUTDOWN IMMEDIATE;
   STARTUP;
   SELECT VALUE FROM NLS_DATABASE_PARAMETERS WHERE PARAMETER = 'NLS_CHARACTERSET';
   ```

   Verify that the output shows the correct character set:

   ```
   VALUE
   --------
   AL32UTF8
   ```

1. Resume RDS Custom automation\. For more information, see [Pausing and resuming RDS Custom automation](#custom-managing.pausing)\.

## Support for Transparent Data Encryption<a name="custom-managing.tde"></a>

RDS Custom supports Transparent Data Encryption \(TDE\) for RDS Custom for Oracle DB instances\.

However, you can't enable TDE using an option in a custom option group as you can in RDS for Oracle\. You turn on TDE manually\. For information about using Oracle Transparent Data Encryption, see [Securing stored data using Transparent Data Encryption](http://docs.oracle.com/cd/E11882_01/network.112/e40393/asotrans.htm#BABFGJAG) in the Oracle documentation\.

## Tagging RDS Custom for Oracle resources<a name="custom-managing.tagging"></a>

You can tag RDS Custom resources as with Amazon RDS resources, but with some important differences:
+ Don't create or modify the `AWSRDSCustom` tag that's required for RDS Custom automation\. If you do, you might break the automation\.
+ Tags added to RDS Custom DB instances during creation are propagated to all other related RDS Custom resources\.
+ Tags aren't propagated when you add them to RDS Custom resources after DB instance creation\.

For general information about resource tagging, see [Tagging Amazon RDS resources](USER_Tagging.md)\.

## Deleting an RDS Custom for Oracle DB instance<a name="custom-managing.deleting"></a>

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