# Working with Amazon RDS on AWS Outposts<a name="rds-on-outposts"></a>

Amazon RDS on AWS Outposts extends Amazon RDS for MySQL and PostgreSQL databases to AWS Outposts environments\. AWS Outposts uses the same hardware as in public AWS Regions to bring AWS services, infrastructure, and operation models on\-premises\. With RDS on Outposts, you can provision managed DB instances close to the business applications that must run on\-premises\. For more information about AWS Outposts, see [AWS Outposts](http://aws.amazon.com/outposts/)\.

You use the same AWS Management Console, AWS CLI, and RDS API to provision and manage on\-premises RDS on Outposts DB instances as you do for RDS DB instances running in the AWS Cloud\. RDS on Outposts automates tasks, such as database provisioning, operating system and database patching, backup, and long\-term archival in Amazon S3\.

RDS on Outposts supports automated backups of DB instances\. Network connectivity between your Outpost and your AWS Region is required to back up and restore DB instances\. All DB snapshots and transaction logs from an Outpost are stored in your AWS Region\. From your AWS Region, you can restore a DB instance from a DB snapshot to a different Outpost\. For more information, see [Working with backups](USER_WorkingWithAutomatedBackups.md)\.

RDS on Outposts supports automated maintenance and upgrades of DB instances\. For more information, see [Maintaining a DB instance](USER_UpgradeDBInstance.Maintenance.md)\.

RDS on Outposts uses encryption at rest for DB instances and DB snapshots using your AWS Key Management Service \(AWS KMS\) key\. For more information about encryption at rest, see [Encrypting Amazon RDS resources](Overview.Encryption.md)\.

When network connectivity to the AWS Region isn't available, your DB instance continues to run locally\. You can't create new DB instances or take new actions on existing DB instances\. Automatic backups don't occur when there is no connectivity\. If there is a DB instance failure, the DB instance isn't automatically replaced until connectivity is restored\. We recommend restoring network connectivity as soon as possible\.

**Topics**
+ [Prerequisites for Amazon RDS on AWS Outposts](#rds-on-outposts.prerequisites)
+ [Amazon RDS on AWS Outposts support for Amazon RDS features](#rds-on-outposts.rds-feature-support)
+ [Supported DB instance classes for Amazon RDS on AWS Outposts](#rds-on-outposts.db-instance-classes)
+ [Customer\-owned IP addresses for RDS on Outposts](#rds-on-outposts.coip)
+ [Creating DB instances for Amazon RDS on AWS Outposts](#rds-on-outposts.creating)

## Prerequisites for Amazon RDS on AWS Outposts<a name="rds-on-outposts.prerequisites"></a>

The following are prerequisites for using Amazon RDS on AWS Outposts:
+ Install AWS Outposts in your on\-premises data center\. For more information about AWS Outposts, see [AWS Outposts](http://aws.amazon.com/outposts/)\.
+ Make sure that you have at least one subnet available for RDS on Outposts\. You can use the same subnet for other workloads\.
+ Make sure that you have a reliable network connection between your Outpost and an AWS Region\.

## Amazon RDS on AWS Outposts support for Amazon RDS features<a name="rds-on-outposts.rds-feature-support"></a>


****  

| Feature | Supported | Notes | More information | 
| --- | --- | --- | --- | 
|  DB instance provisioning  |  Yes  |  You can only create DB instances for RDS for MySQL and RDS for PostgreSQL DB engines\. The following versions are supported: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-on-outposts.html)  |  [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)  | 
|  Modifying the master user password  |  Yes  |  —  |  [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)  | 
|  Renaming a DB instance  |  Yes  |  —  |  [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)  | 
|  Rebooting a DB instance  |  Yes  |  —  |  [Rebooting a DB instance](USER_RebootInstance.md)  | 
|  Stopping a DB instance  |  Yes  |  —  |  [Stopping an Amazon RDS DB instance temporarily](USER_StopInstance.md)  | 
|  Starting a DB instance  |  Yes  |  —  |  [Starting an Amazon RDS DB instance that was previously stopped](USER_StartInstance.md)  | 
|  Multi\-AZ deployments  |  No  |  —  |  [High availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)  | 
|  DB parameter groups  |  Yes  |  —  |  [Working with DB parameter groups](USER_WorkingWithParamGroups.md)  | 
|  Read replicas  |  No  |  —  |  [Working with read replicas](USER_ReadRepl.md)  | 
|  Encryption at rest  |  Yes  |  RDS on Outposts doesn't support unencrypted DB instances\.  |  [Encrypting Amazon RDS resources](Overview.Encryption.md)  | 
|  AWS Identity and Access Management \(IAM\) database authentication  |  No  |  —  |  [IAM database authentication for MySQL and PostgreSQL](UsingWithRDS.IAMDBAuth.md)  | 
|  Associating an IAM role with a DB instance  |  No  |  —  |  [add\-role\-to\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/add-role-to-db-instance.html) CLI command and [AddRoleToDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_AddRoleToDBInstance.html) RDS API operation  | 
|  Kerberos authentication  |  No  |  —  |  [Kerberos authentication](database-authentication.md#kerberos-authentication)  | 
|  Tagging Amazon RDS resources  |  Yes  |  —  |  [Tagging Amazon RDS resources](USER_Tagging.md)  | 
|  Option groups  |  No  |  —  |  [Working with option groups](USER_WorkingWithOptionGroups.md)  | 
|  Modifying the maintenance window  |  Yes  |  —  |  [Maintaining a DB instance](USER_UpgradeDBInstance.Maintenance.md)  | 
|  Automatic minor version upgrade  |  Yes  |  —  |  [Automatically upgrading the minor engine version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.AutoMinorVersionUpgrades)  | 
|  Modifying the backup window  |  Yes  |  —  |  [Working with backups](USER_WorkingWithAutomatedBackups.md) and [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)  | 
|  DB instance scaling  |  Yes  |  To scale a DB instance, modify its on\-premises DB instance class\. Storage scaling isn't supported\.  |  [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)  | 
|  Manual and automatic DB instance snapshots  |  Yes  |  Manual and automatic DB instance snapshots are stored in your AWS Region\.  |  [Creating a DB snapshot](USER_CreateSnapshot.md)  | 
|  Restoring from a DB snapshot  |  Yes  |  —  |  [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)  | 
|  Restoring a DB instance from Amazon S3  |  No  |  —  |  [Restoring a backup into an Amazon RDS MySQL DB instance](MySQL.Procedural.Importing.md)  | 
|  Exporting snapshot data to Amazon S3  |  Yes  |  —  |  [Exporting DB snapshot data to Amazon S3](USER_ExportSnapshot.md)  | 
|  Point\-in\-time recovery  |  Yes  |  —  |  [Restoring a DB instance to a specified time](USER_PIT.md)  | 
|  Enhanced monitoring  |  No  |  —  |  [Enhanced Monitoring](USER_Monitoring.OS.md)  | 
|  Amazon CloudWatch monitoring  |  No  |  —  |  [Monitoring with Amazon CloudWatch](MonitoringOverview.md#monitoring-cloudwatch)  | 
|  Publishing database engine logs to CloudWatch Logs  |  No  |  —  |  [Publishing database logs to Amazon CloudWatch Logs](USER_LogAccess.md#USER_LogAccess.Procedural.UploadtoCloudWatch)  | 
|  Event notification  |  Yes  |  —  |  [Using Amazon RDS event notification](USER_Events.md)  | 
|  Amazon RDS Performance Insights  |  No  |  —  |  [Using Amazon RDS Performance Insights](USER_PerfInsights.md)  | 
|  Viewing or downloading database logs  |  No  |  RDS on Outposts doesn't support viewing database logs using the console or describing database logs using the CLI or RDS API\. RDS on Outposts doesn't support downloading database logs using the console or downloading database logs using the CLI or RDS API\.  |  [Amazon RDS database log files](USER_LogAccess.md)  | 
|  Amazon RDS Proxy  |  No  |  —  |  [Managing connections with Amazon RDS Proxy](rds-proxy.md)  | 
|  Stored procedures for Amazon RDS for MySQL  |  Yes  |  —  |  [MySQL on Amazon RDS SQL reference](Appendix.MySQL.SQLRef.md)  | 
|  Replication with external databases for Amazon RDS for MySQL  |  No  |  —  |  [Replication with a MySQL or MariaDB instance running external to Amazon RDS](MySQL.Procedural.Importing.External.Repl.md)  | 

**Note**  
RDS on Outposts doesn't support use cases that require all data to remain in your data center\.  
RDS on Outposts stores database backups and logs in your AWS Region\.

## Supported DB instance classes for Amazon RDS on AWS Outposts<a name="rds-on-outposts.db-instance-classes"></a>

Amazon RDS on AWS Outposts supports the following DB instance classes:
+ General Purpose DB instance classes
  + db\.m5\.24xlarge
  + db\.m5\.12xlarge
  + db\.m5\.4xlarge
  + db\.m5\.2xlarge
  + db\.m5\.xlarge
  + db\.m5\.large
+ Memory Optimized DB instance classes
  + db\.r5\.24xlarge
  + db\.r5\.12xlarge
  + db\.r5\.4xlarge
  + db\.r5\.2xlarge
  + db\.r5\.xlarge
  + db\.r5\.large

Only General Purpose SSD storage is supported for RDS on Outposts DB instances\. For more information about DB instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\.

## Customer\-owned IP addresses for RDS on Outposts<a name="rds-on-outposts.coip"></a>

AWS Outposts uses information that you provide about your on\-premises network to create an address pool, known as a *customer\-owned IP address pool* \(CoIP pool\)\. *Customer\-owned IP addresses* \(CoIPs\) provide local or external connectivity to resources in your Outpost subnets through your on\-premises network\. For more information about CoIPs, see [Customer\-owned IP addresses](https://docs.aws.amazon.com/outposts/latest/userguide/outposts-networking-components.html#ip-addressing) in the *AWS Outposts User Guide*\.

Each RDS on Outposts DB instance has a private IP address for traffic inside its virtual private cloud \(VPC\)\. This private IP address isn't publicly accessible\. You can use the **Public** option to designate whether the DB instance also has a public IP address in addition to the private IP address\. Using the public IP address for connections routes them through the internet and can result in high latencies in some cases\.

Instead of using these private and public IP addresses, RDS on Outposts supports enabling a CoIP for DB instances through their subnets\. When you enable a CoIP for an RDS on Outposts DB instance, you connect to the DB instance with the DB instance endpoint\. RDS on Outposts automatically uses the CoIP for all connections from both inside and outside of the VPC\.

CoIPs can provide the following benefits for RDS on Outposts DB instances:
+ Lower connection latency
+ Enhanced security

You can enable or disable a CoIP for an RDS on Outposts DB instance using the AWS Management Console, the AWS CLI, or the RDS API:
+ With the AWS Management Console, use the **Customer\-owned IP address \(CoIP\)** setting in **Access type**\.  
![\[The Customer-owned IP address (CoIPs) setting in the AWS Management Console.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/outpost-coip.png)
+ With the AWS CLI, use the `enable-customer-owned-ip | no-enable-customer-owned-ip` option\.
+ With the RDS API, use the `EnableCustomerOwnedIp` parameter\.

You can enable or disable a CoIP when you perform any of the following actions:
+ Create a DB instance

  For more information, see [Creating DB instances for Amazon RDS on AWS Outposts](#rds-on-outposts.creating)\.
+ Modify a DB instance

  For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.
+ Restore a DB instance from a snapshot

  For more information, see [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)\.
+ Restore a DB instance to a specified time

  For more information, see [Restoring a DB instance to a specified time](USER_PIT.md)\.

**Note**  
If you enable a CoIP for a DB instance, but Amazon RDS is unable to allocate a CoIP for the DB instance, the DB instance status is changed to **incompatible\-network**\. For more information about the DB instance status, see [DB instance status](Overview.DBInstance.Status.md)\.

The following limitations apply to CoIP support for RDS on Outposts DB instances:
+ When a CoIP is enabled for a DB instance, make sure that public accessibility is disabled for the DB instance\.
+ You can't assign a CoIP from a CoIP pool to a DB instance\. When you enable a CoIP for a DB instance, Amazon RDS automatically assigns a CoIP from a CoIP pool to the DB instance\.

## Creating DB instances for Amazon RDS on AWS Outposts<a name="rds-on-outposts.creating"></a>

Creating an Amazon RDS on AWS Outposts DB instance is similar to creating an Amazon RDS DB instance in the AWS Cloud\. However, you must specify a DB subnet group that is associated with your Outpost\.

An Amazon VPC can span all of the Availability Zones in an AWS Region\. You can extend any VPC in the AWS Region to your Outpost by adding an Outpost subnet\. To add an Outpost subnet to a VPC, specify the Amazon Resource Name \(ARN\) of the Outpost when you create the subnet\.

Before you create an RDS on Outposts DB instance, you can create a DB subnet group that includes one subnet that is associated with your Outpost\. When you create an RDS on Outposts DB instance, specify this DB subnet group\. You can also choose to create a new DB subnet group when you create your DB instance\.

For information about configuring AWS Outposts, see the [AWS Outposts User Guide](https://docs.aws.amazon.com/outposts/latest/userguide/)\.

### Console<a name="rds-on-outposts.creating.console"></a>

**To create an RDS on Outposts DB instance using the console**

1. Create a DB subnet group with one subnet that is associated with your Outpost\.

   To create a new DB subnet group for the Outpost when you create your DB instance, skip this step\. 
**Note**  
To create a DB subnet group for the AWS Cloud, you specify at least two subnets\. However, for an Outpost DB subnet group, you can specify only one subnet\.

   1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

   1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region where you want to create the DB subnet group\.

   1. Choose **Subnet groups**, and then choose **Create DB Subnet Group**\.

      The **Create DB subnet group** page appears\.  
![\[Create DB subnet group page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/create-db-subnet-group.png)

   1. Set the following values for your new DB subnet group:
      + ****Name** –** The name of the DB subnet group
      + ****Description** –** A description for the DB subnet group
      + ****VPC** –** The VPC for which you're creating the DB subnet group

   1. For **Availability Zones**, choose the Availability Zone for your Outpost\.

   1. For **Subnets**, choose the subnet for use by RDS on Outposts\.

      Your DB subnet group must have only one subnet\.

   1. Choose **Create** to create the DB subnet group\.

1. Create the DB instance, and choose the Outpost for your DB instance\. 

   1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

   1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region where you want to create the DB instance\.

   1. In the navigation pane, choose **Databases**\.

   1. Choose **Create database**\.

      The AWS Management Console detects available Outposts that you have configured and presents the **On\-premises** option in the **Database location** section\.  
![\[Creating an RDS on Outposts DB instance page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/create-outpost-db-instance.png)
**Note**  
If you haven't configured any Outposts, either the **Database location** section doesn't appear or the **RDS on Outposts** option isn't available in the **Choose an on\-premises creation method** section\.

   1. Choose the following settings:
      + ****Database location** –** **On\-premises**
      + ****On\-premises creation method** –** **RDS on Outposts**
      + ****Outpost** –** The Outpost that uses the virtual private cloud \(VPC\) that has the DB subnet group for your DB instance\. Your VPC here must be based on the Amazon VPC service\.
      + ****Virtual Private Cloud \(VPC\)** –** The VPC that contains the DB subnet group for your DB instance\.
      + ****VPC security group** –** The Amazon VPC security group for your DB instance\.
      + ****Subnet group** –** The DB subnet group for your DB instance\.

        You can choose an existing DB subnet group that is associated with the Outpost\. If you didn't create a DB subnet group, you can create a new DB subnet group for the Outpost\. Only one subnet is allowed in this DB subnet group\.

   1. For the remaining sections, specify your DB instance settings\.

      For information about each setting when creating a DB instance, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\.

   1. Choose **Create database**\. 

      If you chose to use an automatically generated password, the **View credential details** button appears on the **Databases** page\.

      To view the master user name and password for the DB instance, choose **View credential details**\.  
![\[Master user credentials after automatic password generation.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-credentials.png)

      To connect to the DB instance as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\. To change the master user password after the DB instance is available, modify the DB instance\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

   1. For **Databases**, choose the name of the new DB instance\.

      On the RDS console, the details for the new DB instance appear\. The DB instance has a status of **Creating** until the DB instance is created and ready for use\. When the state changes to **Available**, you can connect to the DB instance\. Depending on the DB instance class and storage allocated, it can take several minutes for the new DB instance to be available\.   
![\[My DB instances details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/create-outpost-launch.png)

      After the DB instance is available, you can manage it the same way that you manage RDS DB instances in the cloud\.

### AWS CLI<a name="rds-on-outposts.creating.cli"></a>

To create a new DB instance in an Outpost with the AWS CLI, first create a DB subnet group for use by RDS on Outposts by calling the [create\-db\-subnet\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-subnet-group.html) command\. For `--subnet-ids`, specify the subnet group in the Outpost for use by RDS on Outposts\. 

For Linux, macOS, or Unix:

```
1. aws rds create-db-subnet-group \
2.     --db-subnet-group-name myoutpostdbsubnetgr \
3.     --db-subnet-group-description "DB subnet group for RDS on Outposts" \
4.     --subnet-ids subnet-abc123
```

For Windows:

```
1. aws rds create-db-subnet-group ^
2.     --db-subnet-group-name myoutpostdbsubnetgr ^
3.     --db-subnet-group-description "DB subnet group for RDS on Outposts" ^
4.     --subnet-ids subnet-abc123
```

Next, call the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command with the parameters below\. Specify an Availability Zone for the Outpost, an Amazon VPC security group associated with the Outpost, and the DB subnet group you created for the Outpost\. You can include the following options: 
+ `--db-instance-identifier`
+ `--db-instance-class`
+ `--engine`
+ `--availability-zone`
+ `--db-security-groups`
+ `--db-subnet-group-name`
+ `--allocated-storage`
+ `--master-user-name`
+ `--master-user-password`
+ `--backup-retention-period`
+ `-storage-encrypted`
+ `--kms-key-id`

**Example**  
The following example creates a MySQL DB instance named `myoutpostdbinstance`\.  
For Linux, macOS, or Unix:  

```
 1. aws rds create-db-instance \
 2.     --db-instance-identifier myoutpostdbinstance \
 3.     --db-instance-class db.m5.large \
 4.     --engine mysql \
 5.     --availability-zone us-east-1d \
 6.     --db-security-groups outpost-sg \
 7.     --db-subnet-group-name myoutpostdbsubnetgr \
 8.     --allocated-storage 100 \
 9.     --master-username masterawsuser \
10.     --master-user-password masteruserpassword \
11.     --backup-retention-period 3 \
12.     --storage-encrypted \
13.     --kms-key-id mykey
```
For Windows:  

```
 1. aws rds create-db-instance ^
 2.     --db-instance-identifier myoutpostdbinstance ^
 3.     --db-instance-class db.m5.large ^
 4.     --engine mysql ^
 5.     --availability-zone us-east-1d ^
 6.     --db-security-groups outpost-sg ^
 7.     --db-subnet-group-name myoutpostdbsubnetgr ^
 8.     --allocated-storage 100 ^
 9.     --master-username masterawsuser ^
10.     --master-user-password masteruserpassword ^
11.     --backup-retention-period 3 ^
12.     --storage-encrypted ^
13.     --kms-key-id mykey
```

To create a PostgreSQL DB instance, specify `postgres` for the `--engine` option\.

For information about each setting when creating a DB instance, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\.

### RDS API<a name="rds-on-outposts.creating.api"></a>

To create a new DB instance in an Outpost with the RDS API, first create a DB subnet group for use by RDS on Outposts by calling the [CreateDBSubnetGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBSubnetGroup.html) operation\. For `SubnetIds`, specify the subnet group in the Outpost for use by RDS on Outposts\. 

Next, call the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) operation with the parameters below\. Specify an Availability Zone for the Outpost, an Amazon VPC security group associated with the Outpost, and the DB subnet group you created for the Outpost\. 
+ `AllocatedStorage`
+ `AvailabilityZone`
+ `BackupRetentionPeriod`
+ `DBInstanceClass`
+ `DBInstanceIdentifier`
+ `DBSecurityGroups`
+ `DBSubnetGroupName`
+ `Engine`
+ `MasterUsername`
+ `MasterUserPassword`
+ `StorageEncrypted`
+ `KmsKeyID`

For information about each setting when creating a DB instance, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\.