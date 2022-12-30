# Troubleshooting DB issues for Amazon RDS Custom<a name="custom-troubleshooting"></a>

The shared responsibility model of RDS Custom provides OS shell–level access and database administrator access\. RDS Custom runs resources in your account, unlike Amazon RDS, which runs resources in a system account\. With greater access comes greater responsibility\. In the following sections, you can learn how to troubleshoot issues with Amazon RDS Custom DB instances\.

**Topics**
+ [Viewing RDS Custom events](#custom-troubleshooting.support-perimeter.viewing-events)
+ [Subscribing to event notifications](#custom-troubleshooting.support-perimeter.subscribing)
+ [Troubleshooting custom engine version creation for RDS Custom for Oracle](#custom-troubleshooting.cev)
+ [Troubleshooting CEV errors for RDS Custom for SQL Server](#custom-troubleshooting-sqlserver.cev)
+ [RDS Custom support perimeter and unsupported configurations](#custom-troubleshooting.support-perimeter)
+ [Fixing unsupported configurations](#custom-troubleshooting.fix-unsupported)
+ [How Amazon RDS Custom replaces an impaired host](#custom-troubleshooting.host-problems)
+ [Troubleshooting upgrades for RDS Custom for Oracle](#custom-troubleshooting-upgrade)
+ [Troubleshooting replica promotion for RDS Custom for Oracle](#custom-troubleshooting-promote)

## Viewing RDS Custom events<a name="custom-troubleshooting.support-perimeter.viewing-events"></a>

The procedure for viewing events is the same for RDS Custom and Amazon RDS DB instances\. For more information, see [Viewing Amazon RDS events](USER_ListEvents.md)\.

To view RDS Custom event notification using the AWS CLI, use the `describe-events` command\. RDS Custom introduces several new events\. The event categories are the same as for Amazon RDS\. For the list of events, see [Amazon RDS event categories and event messages](USER_Events.Messages.md)\.

The following example retrieves details for the events that have occurred for the specified RDS Custom DB instance\.

```
1. aws rds describe-events \
2.     --source-identifier my-custom-instance \
3.     --source-type db-instance
```

## Subscribing to event notifications<a name="custom-troubleshooting.support-perimeter.subscribing"></a>

The procedure for subscribing to events is the same for RDS Custom and Amazon RDS DB instances\. For more information, see [Subscribing to Amazon RDS event notification](USER_Events.Subscribing.md)\.

To subscribe to RDS Custom event notification using the CLI, use `create-event-subscription` command\. Include the following required parameters:
+ `--subscription-name`
+ `--sns-topic-arn`

The following example creates a subscription for backup and recovery events for an RDS Custom DB instance in the current AWS account\. Notifications are sent to an Amazon Simple Notification Service \(Amazon SNS\) topic, specified by `--sns-topic-arn`\.

```
1. aws rds create-event-subscription \
2.     --subscription-name my-instance-events \
3.     --source-type db-instance \
4.     --event-categories '["backup","recovery"]' \
5.     --sns-topic-arn arn:aws:sns:us-east-1:123456789012:interesting-events
```

## Troubleshooting custom engine version creation for RDS Custom for Oracle<a name="custom-troubleshooting.cev"></a>

When CEV creation fails, RDS Custom issues `RDS-EVENT-0198` with the message `Creation failed for custom engine version major-engine-version.cev_name`, and includes details about the failure\. For example, the event prints missing files\.

CEV creation might fail because of the following issues:
+ The Amazon S3 bucket containing your installation files isn't in the same AWS Region as your CEV\.
+ When you request CEV creation in an AWS Region for the first time, RDS Custom creates an S3 bucket for storing RDS Custom resources \(such as CEV artifacts, AWS CloudTrail logs, and transaction logs\)\.

  CEV creation fails if RDS Custom can't create the S3 bucket\. Either the caller doesn't have S3 permissions as described in [Grant required permissions to your IAM user](custom-setup-orcl.md#custom-setup-orcl.iam-user), or the number of S3 buckets has reached the limit\.
+ The caller doesn't have permissions to get files from your S3 bucket that contains the installation media files\. These permissions are described in [Adding necessary IAM permissions](custom-cev.preparing.md#custom-cev.preparing.iam)\.
+ Your IAM policy has an `aws:SourceIp` condition\. Make sure to follow the recommendations in [AWS Denies access to AWS based on the source IP](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_aws_deny-ip.html) in the *AWS Identity and Access Management User Guide*\. Also make sure that the caller has the S3 permissions described in [Grant required permissions to your IAM user](custom-setup-orcl.md#custom-setup-orcl.iam-user)\.
+ Installation media files listed in the CEV manifest aren't in your S3 bucket\.
+ The SHA\-256 checksums of the installation files are unknown to RDS Custom\.

  Confirm that the SHA\-256 checksums of the provided files match the SHA\-256 checksum on the Oracle website\. If the checksums match, contact [AWS Support](http://aws.amazon.com/premiumsupport) and provide the failed CEV name, file name, and checksum\.
+ The OPatch version is incompatible with your patch files\. You might get the following message: `OPatch is lower than minimum required version. Check that the version meets the requirements for all patches, and try again`\. To apply an Oracle patch, you must use a compatible version of the OPatch utility\. You can find the required version of the Opatch utility in the readme file for the patch\. Download the most recent OPatch utility from My Oracle Support, and try creating your CEV again\.
+ The patches specified in the CEV manifest are in the wrong order\.

You can view RDS events either on the RDS console \(in the navigation pane, choose **Events**\) or by using the `describe-events` AWS CLI command\. The default duration is 60 minutes\. If no events are returned, specify a longer duration, as shown in the following example\.

```
aws rds describe-events --duration 360
```

Currently, the MediaImport service that imports files from Amazon S3 to create CEVs isn't integrated with AWS CloudTrail\. Therefore, if you turn on data logging for Amazon RDS in CloudTrail, calls to the MediaImport service such as the `CreateCustomDbEngineVersion` event aren't logged\.

However, you might see calls from the API gateway that accesses your Amazon S3 bucket\. These calls come from the MediaImport service for the `CreateCustomDbEngineVersion` event\.

## Troubleshooting CEV errors for RDS Custom for SQL Server<a name="custom-troubleshooting-sqlserver.cev"></a>

When you try to create a CEV, it might fail\. In this case, RDS Custom issues the `RDS-EVENT-0198` event message\. For more information on viewing RDS events, see [Amazon RDS event categories and event messages](USER_Events.Messages.md)\. 

Use the following information to help you address possible causes\.


****  

| Message | Troubleshooting suggestions | 
| --- | --- | 
| `Custom Engine Version creation expected a Sysprep’d AMI. Retry creation using a Sysprep’d AMI.` | Run Sysprep on the EC2 instance that you created from the AMI\. For more information about prepping an AMI using Sysprep, see [Create a standardized Amazon Machine Image \(AMI\) using Sysprep](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/Creating_EBSbacked_WinAMI.html#sysprep-using-ec2launchv2)\. | 
| `EC2 Image permissions for image (AMI_ID) weren't found for customer (Customer_ID). Verify customer (Customer_ID) has valid permissions on the EC2 Image.` | Verify that your account and profile used for creation has the required permissions on `create EC2 Instance` and `Describe Images` for the selected AMI\. | 
| `Image (AMI_ID) doesn't exist in your account (ACCOUNT_ID). Verify (ACCOUNT_ID) is the owner of the EC2 image.` | Ensure the AMI exists in the same customer account\. | 
| `Image id (AMI_ID) isn't valid. Specify a valid image id, and try again.` | The name of the AMI is incorrect\. Ensure the correct AMI ID is provided\. | 
| `Image (AMI_ID) operating system platform isn't supported. Specify a valid image, and try again.` |  Choose a supported AMI that has Windows Server with SQL Server Enterprise, Standard, or Web edition\. Choose an AMI with one of the following usage operation codes from the EC2 Marketplace: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-troubleshooting.html)  | 
| `The custom engine version can't be the same as the OEV engine version. Specify a valid CEV, and try again.` | Classic RDS Custom for SQL Server engine versions aren't supported\. For example, version **15\.00\.4073\.23\.v1**\. Use a supported version number\. | 
| `The custom engine version isn't in an active state. Specify a valid CEV, and try again.` | The CEV must be in an `AVAILABLE` state to complete the operation\. Modify the CEV from `INACTIVE` to `AVAILABLE`\.  | 
| `The custom engine version isn't valid for an upgrade. Specify a valid CEV with an engine version greater or equal to (X), and try again.` | The target CEV is not valid\. Check the requirements for a valid upgrade path\. For more information, see \(link needed here\) | 
| `The custom engine version isn't valid. Names can include only lowercase letters (a-z), dashes (-), underscores (_), and periods (.). Specify a valid CEV, and try again.` | Follow the required CEV naming convention\. For more information, see [Requirements](custom-cev-sqlserver.preparing.md#custom-cev-sqlserver.preparing.Requirements)\. | 
| `The custom engine version isn't valid. Specify valid database engine version, and try again. Example: 15.00.4073.23-cev123.` | An unsupported DB engine version was provided\. Use a supported DB engine version\. | 
| `The expected architecture is (X) for image (AMI_ID), but architecture (Y) was found.` | Use an AMI built on the **x86\_64** architecture\. | 
| `The expected owner of image (AMI_ID) is customer account ID (ACCOUNT_ID), but owner (ACCOUNT_ID) was found.` | Create the EC2 instance from the AMI that you have permission for\. Run Sysprep on the EC2 instance to create and save a base image\.  | 
| `The expected platform is (X) for image (AMI_ID), but platform (Y) was found.` | Use an AMI built with the Windows platform\. | 
| `The expected root device type is (X) for image %s, but root device type (Y) was found.` | Create the AMI with the EBS device type\. | 
| `The expected SQL Server edition is (X), but (Y) was found.` |  Choose a supported AMI that has Windows Server with SQL Server Enterprise, Standard, or Web edition\. Choose an AMI with one of the following usage operation codes from the EC2 Marketplace: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-troubleshooting.html)  | 
| `The expected state is (X) for image (AMI_ID), but the following state was found: (Y).` | Ensure the AMI is in a state of `AVAILABLE`\. | 
| `The provided Windows OS name (X) isn’t valid. Make sure the OS is one of the following: (Y).` | Use a supported Windows OS\. | 
| `RDS expected a Windows build version greater than or equal to (X), but found version (Y).` | Use an AMI with a minimum OS build version of **14393**\.  | 
| `RDS expected a Windows major version greater than or equal to (X).1f, but found version (Y).1f.` | Use an AMI with a minimum OS major version of **10\.0** or higher\.  | 

## RDS Custom support perimeter and unsupported configurations<a name="custom-troubleshooting.support-perimeter"></a>

RDS Custom provides monitoring capability called the *support perimeter*\. The support perimeter ensures that your RDS Custom instance uses a supported AWS infrastructure, operating system, and database\.

The support perimeter checks that the requirements listed in [Fixing unsupported configurations](#custom-troubleshooting.fix-unsupported) are met\. If any of these requirements aren't met, RDS Custom considers the DB instance to be outside of the support perimeter\. RDS Custom then changes the DB instance status to `unsupported-configuration`, and sends event notifications\. After you fix the configuration problems, RDS Custom changes the DB instance status to `available`\.

### What happens in the `unsupported-configuration` state<a name="custom-troubleshooting.unsupported"></a>

While the DB instance is in the `unsupported-configuration` state, the following is the case:
+ You can't modify the DB instance\.
+ You can't take DB snapshots\.
+ Automated backups aren't created\.
+ RDS Custom automation doesn't replace the underlying Amazon EC2 instance if it becomes impaired\.

However, the following RDS Custom automation continues to run:
+ The RDS Custom agent monitors the DB instance and sends notifications to the support perimeter of further changes\.  
+ The support perimeter continues to run and captures change events for the DB instance\. This has two purposes:
  + When you fix the configuration that caused the DB instance to be in the `unsupported-configuration` state, the support perimeter can return the DB instance to the `available` state\.
  + If you make other unsupported configurations, the support perimeter notifies you about them so that you can correct them\.
+ Redo logs are still archived and uploaded to Amazon S3 to facilitate point\-in\-time recovery \(PITR\)\. However, be aware of the following:
  + PITR can take a long time to completely restore to a new DB instance\. This is because you can't take either automated or manual snapshots while the DB instance is in the `unsupported-configuration` state\.

    PITR has to replay more redo logs starting from the most recent snapshot taken before the instance entered the `unsupported-configuration` state\.
  + In some cases, the DB instance is in the `unsupported-configuration` state because of changes you made that impacted redo log upload functionality\. In these cases, PITR can't restore the DB instance to the latest restorable time\.

## Fixing unsupported configurations<a name="custom-troubleshooting.fix-unsupported"></a>

It's your responsibility to fix configuration issues that put your RDS Custom DB instance into the `unsupported-configuration` state\. If the issue is with the AWS infrastructure, you can use the console or the AWS CLI to fix it\. If the issue is with the operating system or the database configuration, you can log in to the host to fix it\.

In the following table, you can find descriptions of the notifications that the support perimeter sends and how to fix them\. These notifications and the support perimeter are subject to change\.


| Notification | Description | Action | 
| --- | --- | --- | 
|  Database health: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-troubleshooting.html)  |  The support perimeter monitors the DB instance state\. It also monitors how many restarts occurred during the previous hour and day\. You're notified when the instance is in a state where it still exists, but you can't interact with it\.  |  Log in to the host and examine the database state\. <pre>ps -eo pid,state,command | grep smon</pre> Restart the DB instance if necessary to get it running again\. Sometimes it's necessary to reboot the host\. After the restart, the RDS Custom agent detects that the DB instance is no longer in an unresponsive state\. It then notifies the support perimeter to reevaluate the DB instance state\.   | 
|  Database archive lag target \(RDS Custom for Oracle only\): [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-troubleshooting.html)  |  The support perimeter monitors the `ARCHIVE_LAG_TARGET` database parameter to verify that the DB instance's latest restorable time is within reasonable bounds\.  |  Log in to the host, connect to the DB instance, and change the `ARCHIVE_LAG_TARGET` parameter to a value from 60–7200\. For example, use the following SQL command\. <pre>alter system set archive_lag_target=300 scope=both;</pre> The DB instance becomes available within 30 minutes\.  | 
|  Database log mode \(RDS Custom for Oracle only\): The monitored log mode of the database on Amazon EC2 instance \[i\-*xxxxxxxxxxxxxxxxx*\] has changed from \[ARCHIVELOG\] to \[NOARCHIVELOG\]\.  | The DB instance log mode must be set to ARCHIVELOG\. |  Log in to the host and shut down the DB instance\. You can use the following SQL command\. <pre>shutdown immediate;</pre> The RDS Custom agent restarts the DB instance and sets the log mode to `ARCHIVELOG`\. The DB instance becomes available within 30 minutes\.  | 
|  RDS Custom agent status \(RDS Custom for Oracle\): The monitored state of the RDS Custom agent on EC2 instance \[i\-*xxxxxxxxxxxxxxxxx*\] has changed from RUNNING to STOPPED\.  |  The RDS Custom agent must always be running\. The agent publishes the `IamAlive` metric to Amazon CloudWatch every 30 seconds\. An alarm is triggered if the metric hasn't been published for 30 seconds\. The support perimeter also monitors the RDS Custom agent process state on the host every 30 minutes\. On RDS Custom for Oracle, the DB instance goes outside the support perimeter if the RDS Custom agent stops\.  |  Log in to the host and make sure that the RDS Custom agent is running\. You can use the following commands to find the agent's status\. <pre>ps -ef | grep RDSCustomAgent</pre> <pre>pgrep java</pre> When the RDS Custom agent is running again, the `IamAlive` metric is published to Amazon CloudWatch, and the alarm switches to the `OK` state\. This switch notifies the support perimeter that the agent is running\.  | 
|  RDS Custom agent status \(RDS Custom for SQL Server\): The RDS Custom instance is going out of perimeter because an unsupported configuration was used for RDS Custom agent\.  |  The RDS Custom agent must always be running\. The support perimeter monitors the RDS Custom agent process state on the host every 1 minute\. On RDS Custom for SQL Server, a stopped agent is recovered by the monitoring service\. The DB instance goes outside the support perimeter if the RDS Custom agent is uninstalled\.  |  Log in to the host and make sure that the RDS Custom agent is running\. You can use the following commands to find the agent's status\. <pre>$name = "RDSCustomAgent"<br />$service = Get-Service $name<br />Write-Host $service.Status</pre> If the status isn't `Running`, you can start the service with the following command: <pre>Start-Service $name</pre>  | 
|  AWS Systems Manager agent \(SSM agent\) status \(RDS Custom for Oracle\): The AWS Systems Manager agent on EC2 instance \[i\-*xxxxxxxxxxxxxxxxx*\] is currently unreachable\. Make sure you have correctly configured the network, agent, and IAM permissions\.  |  The SSM agent must always be running\. The RDS Custom agent is responsible for making sure that the Systems Manager agent is running\. If the SSM agent was down and restarted, the RDS Custom agent publishes a metric to CloudWatch\. The RDS Custom agent has an alarm on the metric set to trigger when there has been a restart in each of the previous three minutes\. The support perimeter also monitors the SSM agent process state on the host every 30 minutes\.  |  For more information, see [Troubleshooting SSM Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/troubleshooting-ssm-agent.html)\. When the SSM agent is running again, the alarm switches to the `OK` state\. This switch notifies the support perimeter that the agent is running\.  | 
|  SSM agent status \(RDS Custom for SQL Server\): The RDS Custom instance is going out of perimeter because an unsupported configuration was used for SSM agent\.  |  The SSM agent must always be running\. The RDS Custom agent is responsible for making sure that the Systems Manager agent is running\. The support perimeter monitors the SSM agent process state on the host every 1 minute\.  |  For more information, see [Troubleshooting SSM Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/troubleshooting-ssm-agent.html)\.  | 
|  `sudo` configurations \(RDS Custom for Oracle only\): The sudo configurations on EC2 instance \[i\-*xxxxxxxxxxxxxxxxx*\] have changed\.  |  The support perimeter monitors that certain OS users are allowed to run certain commands on the box\. It monitors `sudo` configurations against the supported state\. When the `sudo` configurations aren't supported, the RDS Custom tries to overwrite them back to the previous supported state\. If that is successful, the following notification is sent: RDS Custom successfully overwrote your configuration\.  |  If the overwrite is unsuccessful, you can log in to the host and investigate why recent changes to the `sudo` configurations aren't supported\. You can use the following command\. <pre>visudo -c -f /etc/sudoers.d/individual_sudo_files</pre> After the support perimeter determines that the `sudo` configurations are supported, the DB instance becomes available within 30 minutes\.  | 
|  Amazon Elastic Block Store \(Amazon EBS\) volumes: RDS Custom for Oracle: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-troubleshooting.html) RDS Custom for SQL Server:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-troubleshooting.html)  |  RDS Custom creates two types of EBS volume, besides the root volume created from the Amazon Machine Image \(AMI\), and associates them with the EC2 instance\. The binary volume is where the database software binaries are located\. The data volumes are where database files are located\. The storage configurations that you set when creating the DB instance are used to configure the data volumes\. The support perimeter monitors the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-troubleshooting.html)  |  If you detached any initial EBS volumes, contact AWS Support\. If you modified the storage type, Provisioned IOPS, or storage throughput of an EBS volume, revert the modification to the original value\. If you modified the storage size of an EBS volume, contact AWS Support\. \(RDS Custom for Oracle only\) If you attached any additional EBS volumes, do either of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-troubleshooting.html)  | 
|  EBS\-optimized instances \(RDS Custom for Oracle only\): The EBS\-optimized attribute of Amazon EC2 instance \[i\-*xxxxxxxxxxxxxxxxx*\] has changed from \[enabled\] to \[disabled\]\.  |  Amazon EC2 instances should be EBS optimized\. If the `EBS-optimized` attribute is turned off \(`disabled`\), the support perimeter doesn't put the DB instance into the `unsupported-configuration` state\.  |  To turn on the `EBS-optimized` attribute: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-troubleshooting.html)  | 
|  Amazon EC2 instance state: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-troubleshooting.html)  |  The support perimeter monitors EC2 instance state\-change notifications\. The EC2 instance must always be running\.  |  If the EC2 instance is stopped, start it and remount the binary and data volumes\. On RDS Custom for Oracle, if the EC2 instance is terminated, delete the RDS Custom DB instance\. On RDS Custom for SQL Server, if the EC2 instance is terminated, RDS Custom performs an automated recovery to provision a new EC2 instance\.   | 
|  Amazon EC2 instance attributes: RDS Custom for Oracle: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-troubleshooting.html) RDS Custom for SQL Server: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-troubleshooting.html)  |  The support perimeter monitors the instance type of the EC2 instance where the RDS Custom DB instance is running\. The EC2 instance type must stay the same as when you set it up during RDS Custom DB instance creation\.  |  Change the EC2 instance type back to the original type using the EC2 console or CLI\. To change the instance type because of scaling requirements, do a PITR and specify the new instance type and class\. However, doing this results in a new RDS Custom DB instance with a new host and Domain Name System \(DNS\) name\.  | 
|  Oracle Data Guard role \(RDS Custom for Oracle only\): [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-troubleshooting.html)  |  The support perimeter monitors the current database role every 15 seconds and sends a CloudWatch notification if the database role has changed\. The Oracle Data Guard `DATABASE_ROLE` parameter must be either `PRIMARY` or `PHYSICAL STANDBY`\.  |  Restore the Oracle Data Guard database role to a supported value\. After the support perimeter determines that the database role is supported, the RDS Custom for Oracle DB instance becomes available within 15 seconds\.  | 
|  Shared memory connections \(RDS Custom for SQL Server only\): The RDS Custom instance is going out of perimeter because an unsupported configuration was used for shared memory protocol\.  |  The RDS Custom agent on the EC2 host connects to SQL Server using the shared memory protocol\. If this protocol is turned off \(`Enabled` is set to `No`\), then RDS Custom can't perform its management actions and places the DB instance outside the support perimeter\.  |  To bring the RDS Custom for SQL Server DB instance back within the support perimeter, turn on the shared memory protocol on the **Protocol** page of the **Shared Memory Properties** window by setting `Enabled` to `Yes`\. Restart SQL Server after enabling the protocol\.  | 
|  Database file locations \(RDS Custom for SQL Server only\): The RDS Custom instance is going out of perimeter because an unsupported configuration was used for database files location\.  |  All SQL Server database files are stored on the D: drive by default, in the `D:\rdsdbdata\DATA` directory\. If you create or alter the database file location to be anywhere other than the D: drive, then RDS Custom places the DB instance outside the support perimeter\. We strongly recommend that you don't save any database files on the C: drive\. You can lose data on the C: drive during certain operations, such as hardware failure\. Storage on the C: drive doesn't offer the same durability as on the D: drive, which is an EBS volume\. Also, if database files can't be found, RDS Custom places the DB instance outside the support perimeter\.  | Store all RDS Custom for SQL Server database files on the D: drive\. | 

## How Amazon RDS Custom replaces an impaired host<a name="custom-troubleshooting.host-problems"></a>

If the Amazon EC2 host becomes impaired, RDS Custom attempts to reboot it\. If this effort fails, RDS Custom uses the same stop and start feature included in Amazon EC2\.

**Topics**
+ [Stopping and starting the host](#custom-troubleshooting.host-problems.replacement.stop-start)
+ [Effects of host replacement](#custom-troubleshooting.host-problems.replacement.host-state)
+ [Best practices for Amazon EC2 hosts](#custom-troubleshooting.host-problems.best-practices)

### Stopping and starting the host<a name="custom-troubleshooting.host-problems.replacement.stop-start"></a>

RDS Custom automatically takes the following steps, with no user intervention required:

1. Stops the Amazon EC2 host\.

   The EC2 instance performs a normal shutdown and stops running\. Any Amazon EBS volumes remain attached to the instance, and their data persists\. Any data stored in the instance store volumes \(not supported on RDS Custom\) or RAM of the host computer is gone\.

   For more information, see [Stop and start your instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Stop_Start.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Starts the Amazon EC2 host\.

   The EC2 instance migrates to a new underlying host hardware\. In some cases, the RDS Custom DB instance remains on the original host\.

### Effects of host replacement<a name="custom-troubleshooting.host-problems.replacement.host-state"></a>

In RDS Custom, you have full control over the root device volume and Amazon EBS storage volumes\. The root volume can contain important data and configurations that you don't want to lose\.

RDS Custom for Oracle retains all database and customer data after the operation, including root volume data\. No user intervention is required\. On RDS Custom for SQL Server, database data is retained, but any data on the C: drive, including operating system and customer data, is lost\.

After the replacement process, the Amazon EC2 host has a new public IP address\. The host retains the following:
+ Instance ID
+ Private IP addresses
+ Elastic IP addresses
+ Instance metadata
+ Data storage volume data
+ Root volume data \(on RDS Custom for Oracle\)

### Best practices for Amazon EC2 hosts<a name="custom-troubleshooting.host-problems.best-practices"></a>

The Amazon EC2 host replacement feature covers the majority of Amazon EC2 impairment scenarios\. We recommend that you adhere to the following best practices:
+ Before you change your configuration or the operating system, back up your data\. If the root volume or operating system becomes corrupt, host replacement can't repair it\. Your only options are restoring from a DB snapshot or point\-in\-time recovery\.
+ Don't manually stop or terminate the physical Amazon EC2 host\. Both actions result in the instance being put outside the RDS Custom support perimeter\.
+ \(RDS Custom for SQL Server\) If you attach additional volumes to the Amazon EC2 host, configure them to remount upon restart\. If the host is impaired, RDS Custom might stop and start the host automatically\.

## Troubleshooting upgrades for RDS Custom for Oracle<a name="custom-troubleshooting-upgrade"></a>

Your upgrade of an RDS Custom for Oracle instance might fail\. Following, you can find techniques that you can use during upgrades of RDS Custom DB for Oracle DB instances:
+ Examine the following log files:
  + All upgrade output log files reside in the `/tmp` directory on the DB instance\.
  + These log files are named `catupg*.log`\.
  + A master output file named `/tmp/catupgrd0.log` reports all errors, and the steps performed\.
  + The `alert.log` file for the DB instance is located in the `/rdsdbdata/log/trace` directory\. Examine this file regularly as a best practice\.
+ Run the following `grep` command in the `root` directory to track the upgrade OS process\. This command shows where the log files are being written and determine the state of the upgrade process\.

  ```
  ps -aux | grep upg
  ```

  The output resembles the following\.

  ```
  root     18884  0.0  0.0 235428  8172 ?        S<   17:03   0:00 /usr/bin/sudo -u rdsdb /rdsdbbin/scripts/oracle-control ORCL op_apply_upgrade_sh RDS-UPGRADE/2.upgrade.sh
  rdsdb    18886  0.0  0.0 153968 12164 ?        S<   17:03   0:00 /usr/bin/perl -T -w /rdsdbbin/scripts/oracle-control ORCL op_apply_upgrade_sh RDS-UPGRADE/2.upgrade.sh
  rdsdb    18887  0.0  0.0 113196  3032 ?        S<   17:03   0:00 /bin/sh /rdsdbbin/oracle/rdbms/admin/RDS-UPGRADE/2.upgrade.sh
  rdsdb    18900  0.0  0.0 113196  1812 ?        S<   17:03   0:00 /bin/sh /rdsdbbin/oracle/rdbms/admin/RDS-UPGRADE/2.upgrade.sh
  rdsdb    18901  0.1  0.0 167652 20620 ?        S<   17:03   0:07 /rdsdbbin/oracle/perl/bin/perl catctl.pl -n 4 -d /rdsdbbin/oracle/rdbms/admin -l /tmp catupgrd.sql
  root     29944  0.0  0.0 112724  2316 pts/0    S+   18:43   0:00 grep --color=auto upg
  ```
+ Run the following SQL query to verify the current state of the components to find the database version and the options installed on the DB instance\.

  ```
  set linesize 180
  column comp_id format a15
  column comp_name format a40 trunc
  column status format a15 trunc
  select comp_id, comp_name, version, status from dba_registry order by 1;
  ```

  The output resembles the following\.

  ```
  COMP_NAME                                STATUS               PROCEDURE
  ---------------------------------------- -------------------- --------------------------------------------------
  Oracle Database Catalog Views            VALID                DBMS_REGISTRY_SYS.VALIDATE_CATALOG
  Oracle Database Packages and Types       VALID                DBMS_REGISTRY_SYS.VALIDATE_CATPROC
  Oracle Text                              VALID                VALIDATE_CONTEXT
  Oracle XML Database                      VALID                DBMS_REGXDB.VALIDATEXDB
  
  4 rows selected.
  ```
+ Run the following SQL query to check for invalid objects that might interfere with the upgrade process\.

  ```
  set pages 1000 lines 2000
  col OBJECT for a40
  Select substr(owner,1,12) owner,
         substr(object_name,1,30) object,
         substr(object_type,1,30) type, status,
         created
  from
         dba_objects where status <>'VALID' and owner in ('SYS','SYSTEM','RDSADMIN','XDB');
  ```

## Troubleshooting replica promotion for RDS Custom for Oracle<a name="custom-troubleshooting-promote"></a>

You can promote managed Oracle replicas in RDS Custom for Oracle using the console, `promote-read-replica` AWS CLI command, or `PromoteReadReplica` API\. If you delete your primary DB instance, and all replicas are healthy, RDS Custom for Oracle promotes your managed replicas to standalone instances automatically\. If a replica has paused automation or is outside the support perimeter, you must fix the replica before RDS Custom can promote it automatically\. For more information, see [Replica promotion requirements and limitations](custom-rr.md#custom-rr.promotion-reqs)\.

The replica promotion workflow might become stuck in the following situation:
+ The primary DB instance is in the state `STORAGE_FULL`\.
+ The primary DB can't archive all of its online redo logs\.
+ A gap exists between the archived redo log files on your Oracle replica and the primary database\.

To respond to the stuck workflow, complete the following steps:

1. Synchronize the redo log gap on your Oracle replica DB instance\.

1. Force the promotion of your read replica to the latest applied redo log\. Run the following commands in SQL\*Plus:

   ```
   ALTER DATABASE ACTIVATE STANDBY DATABASE;
   SHUTDOWN IMMEDIATE
   STARTUP
   ```

1. Contact AWS Support and request it to move your DB instance to available status\.