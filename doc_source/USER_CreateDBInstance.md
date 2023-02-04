# Creating an Amazon RDS DB instance<a name="USER_CreateDBInstance"></a>

The basic building block of Amazon RDS is the DB instance, where you create your databases\. You choose the engine\-specific characteristics of the DB instance when you create it\. You also choose the storage capacity, CPU, memory, and so on, of the AWS instance on which the database server runs\.

**Topics**
+ [DB instance prerequisites](#USER_CreateDBInstance.Prerequisites)
+ [Creating a DB instance](#USER_CreateDBInstance.Creating)
+ [Settings for DB instances](#USER_CreateDBInstance.Settings)

## DB instance prerequisites<a name="USER_CreateDBInstance.Prerequisites"></a>

**Important**  
Before you can create an Amazon RDS DB instance, you must complete the tasks in [Setting up for Amazon RDS](CHAP_SettingUp.md)\.

The following are prerequisites to complete before creating a DB instance\.

**Topics**
+ [Configure the network for the DB instance](#USER_CreateDBInstance.Prerequisites.VPC)
+ [Additional prerequisites](#USER_CreateDBInstance.Prerequisites.Additional)

### Configure the network for the DB instance<a name="USER_CreateDBInstance.Prerequisites.VPC"></a>

You can create an Amazon RDS DB instance only in a virtual private cloud \(VPC\) based on the Amazon VPC service\. Also, it must be in an AWS Region that has at least two Availability Zones\. The DB subnet group that you choose for the DB instance must cover at least two Availability Zones\. This configuration ensures that you can configure a Multi\-AZ deployment when you create the DB instance or easily move to one in the future\.

To set up connectivity between your new DB instance and an Amazon EC2 instance in the same VPC, do so when you create the DB instance\. To connect to your DB instance from resources other than EC2 instances in the same VPC, configure the network connections manually\.

**Topics**
+ [Configure automatic network connectivity with an EC2 instance](#USER_CreateDBInstance.Prerequisites.VPC.Automatic)
+ [Configure the network manually](#USER_CreateDBInstance.Prerequisites.VPC.Manual)

#### Configure automatic network connectivity with an EC2 instance<a name="USER_CreateDBInstance.Prerequisites.VPC.Automatic"></a>

When you create an RDS DB instance, you can use the AWS Management Console to set up connectivity between an EC2 instance and the new DB instance\. When you do so, RDS configures your VPC and network settings automatically\. The DB instance is created in the same VPC as the EC2 instance so that the EC2 instance can access the DB instance\.

The following are requirements for connecting an EC2 instance with the DB instance:
+ The EC2 instance must exist in the AWS Region before you create the DB instance\.

  If no EC2 instances exist in the AWS Region, the console provides a link to create one\.
+ The user who is creating the DB instance must have permissions to perform the following operations:
  + `ec2:AssociateRouteTable` 
  + `ec2:AuthorizeSecurityGroupEgress` 
  + `ec2:AuthorizeSecurityGroupIngress` 
  + `ec2:CreateRouteTable` 
  + `ec2:CreateSubnet` 
  + `ec2:CreateSecurityGroup` 
  + `ec2:DescribeInstances` 
  + `ec2:DescribeNetworkInterfaces` 
  + `ec2:DescribeRouteTables` 
  + `ec2:DescribeSecurityGroups` 
  + `ec2:DescribeSubnets` 
  + `ec2:ModifyNetworkInterfaceAttribute` 
  + `ec2:RevokeSecurityGroupEgress` 

Using this option creates a private DB instance\. The DB instance uses a DB subnet group with only private subnets to restrict access to resources within the VPC\.

To connect an EC2 instance to the DB instance, choose **Connect to an EC2 compute resource** in the **Connectivity** section on the **Create database** page\.

![\[Connect an EC2 instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ec2-set-up-connection-create.png)

When you choose **Connect to an EC2 compute resource**, RDS sets the following options automatically\. You can't change these settings unless you choose not to set up connectivity with an EC2 instance by choosing **Don't connect to an EC2 compute resource**\.


****  

| Console option | Automatic setting | 
| --- | --- | 
|  **Network type**  |  RDS sets network type to **IPv4**\. Currently, dual\-stack mode isn't supported when you set up a connection between an EC2 instance and the DB instance\.  | 
|  **Virtual Private Cloud \(VPC\)**  |  RDS sets the VPC to the one associated with the EC2 instance\.  | 
|  **DB subnet group**  | RDS requires a DB subnet group with a private subnet in the same Availability Zone as the EC2 instance\. If a DB subnet group that meets this requirement exists, then RDS uses the existing DB subnet group\. By default, this option is set to Automatic setup\. When you choose **Automatic setup** and there is no DB subnet group that meets this requirement, the following action happens\. RDS uses three available private subnets in three Availability Zones where one of the Availability Zones is the same as the EC2 instance\. If a private subnet isn’t available in an Availability Zone, RDS creates a private subnet in the Availability Zone\. Then RDS creates the DB subnet group\.When a private subnet is available, RDS uses the route table associated with the subnet and adds any subnets it creates to this route table\. When no private subnet is available, RDS creates a route table without internet gateway access and adds the subnets it creates to the route table\.RDS also allows you to use existing DB subnet groups\. Select **Choose existing** if you want to use an existing DB subnet group of your choice\.  | 
|  **Public access**  |  RDS chooses **No** so that the DB instance isn't publicly accessible\. For security, it is a best practice to keep the database private and make sure it isn't accessible from the internet\.  | 
|  **VPC security group \(firewall\)**  |  RDS creates a new security group that is associated with the DB instance\. The security group is named `rds-ec2-n`, where `n` is a number\. This security group includes an inbound rule with the EC2 VPC security group \(firewall\) as the source\. This security group that is associated with the DB instance allows the EC2 instance to access the DB instance\. RDS also creates a new security group that is associated with the EC2 instance\. The security group is named `ec2-rds-n`, where `n` is a number\. This security group includes an outbound rule with the VPC security group of the DB instance as the source\. This security group allows the EC2 instance to send traffic to the DB instance\. You can add another new security group by choosing **Create new** and typing the name of the new security group\. You can add existing security groups by choosing **Choose existing** and selecting security groups to add\.  | 
|  **Availability Zone**  |  When you choose **Single DB instance** in **Availability & durability** \(Single\-AZ deployment\), RDS chooses the Availability Zone of the EC2 instance\. When you choose **Multi\-AZ DB instance** in **Availability & durability** \(Multi\-AZ DB instance deployment\), RDS chooses the Availability Zone of the EC2 instance for one DB instance in the deployment\. RDS randomly chooses a different Availability Zone for the other DB instance\. Either the primary DB instance or the standby replica is created in the same Availability Zone as the EC2 instance\. When you choose **Multi\-AZ DB instance**, there is the possibility of cross Availability Zone costs if the DB instance and EC2 instance are in different Availability Zones\.  | 

For more information about these settings, see [Settings for DB instances](#USER_CreateDBInstance.Settings)\.

If you change these settings after the DB instance is created, the changes might affect the connection between the EC2 instance and the DB instance\.

#### Configure the network manually<a name="USER_CreateDBInstance.Prerequisites.VPC.Manual"></a>

To connect to your DB instance from resources other than EC2 instances in the same VPC, configure the network connections manually\. If you use the AWS Management Console to create your DB instance, you can have Amazon RDS automatically create a VPC for you\. Or you can use an existing VPC or create a new VPC for your DB instance\. With any approach, your VPC requires at least one subnet in each of at least two Availability Zones for use with an RDS DB instance\. 

By default, Amazon RDS creates the DB instance an Availability Zone automatically for you\. To choose a specific Availability Zone, you need to change the **Availability & durability** setting to **Single DB instance**\. Doing so exposes an **Availability Zone** setting that lets you choose from among the Availability Zones in your VPC\. However, if you choose a Multi\-AZ deployment, RDS chooses the Availability Zone of the primary or writer DB instance automatically, and the **Availability Zone** setting doesn't appear\.

In some cases, you might not have a default VPC or haven't created a VPC\. In these cases, you can have Amazon RDS automatically create a VPC for you when you create a DB instance using the console\. Otherwise, do the following:
+ Create a VPC with at least one subnet in each of at least two of the Availability Zones in the AWS Region where you want to deploy your DB instance\. For more information, see [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#Overview.RDSVPC.Create) and [Tutorial: Create a VPC for use with a DB instance \(IPv4 only\)](CHAP_Tutorials.WebServerDB.CreateVPC.md)\.
+ Specify a VPC security group that authorizes connections to your DB instance\. For more information, see [Provide access to your DB instance in your VPC by creating a security group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup) and [Controlling access with security groups](Overview.RDSSecurityGroups.md)\.
+ Specify an RDS DB subnet group that defines at least two subnets in the VPC that can be used by the DB instance\. For more information, see [Working with DB subnet groups](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Subnets)\.

If you want to connect to a resource that isn't in the same VPC as the DB instance, see the appropriate scenarios in [Scenarios for accessing a DB instance in a VPC](USER_VPC.Scenarios.md)\.

### Additional prerequisites<a name="USER_CreateDBInstance.Prerequisites.Additional"></a>

Before you create your DB instance, consider the following additional prerequisites:
+ If you are connecting to AWS using AWS Identity and Access Management \(IAM\) credentials, your AWS account must have certain IAM policies\. These grant the permissions required to perform Amazon RDS operations\. For more information, see [Identity and access management for Amazon RDS](UsingWithRDS.IAM.md)\.

  To use IAM to access the RDS console, sign in to the AWS Management Console with your IAM user credentials\. Then go to the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.
+ To tailor the configuration parameters for your DB instance, specify a DB parameter group with the required parameter settings\. For information about creating or modifying a DB parameter group, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.
+ Determine the TCP/IP port number to specify for your DB instance\. The firewalls at some companies block connections to the default ports for RDS DB instances\. If your company firewall blocks the default port, choose another port for your DB instance\.

## Creating a DB instance<a name="USER_CreateDBInstance.Creating"></a>

You can create an Amazon RDS DB instance using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="USER_CreateDBInstance.CON"></a>

You can create a DB instance by using the AWS Management Console with **Easy create** enabled or not enabled\. With **Easy create** enabled, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy create** uses the default setting for other configuration options\. With **Easy create** not enabled, you specify more configuration options when you create a database, including ones for availability, security, backups, and maintenance\.

**Note**  
In the following procedure, **Standard create** is enabled, and **Easy create** isn't enabled\. This procedure uses Microsoft SQL Server as an example\.  
For examples that use **Easy create** to walk you through creating and connecting to sample DB instances for each engine, see [Getting started with Amazon RDS](CHAP_GettingStarted.md)\.

**To create a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB instance\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**\.

1. In **Choose a database creation method**, select **Standard Create**\.

1. In **Engine options**, choose the engine type: MariaDB, Microsoft SQL Server, MySQL, Oracle, or PostgreSQL\. **Microsoft SQL Server** is shown here\.   
![\[Engine selection\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQLSvr-Launch01.png)

1. For **Edition**, if you're using Oracle or SQL Server choose the DB engine edition that you want to use\.

   MySQL has only one option for the edition, and MariaDB and PostgreSQL have none\.

1. For **Version**, choose the engine version\.

1. In **Templates**, choose the template that matches your use case\. If you choose **Production**, the following are preselected in a later step:
   + **Multi\-AZ** failover option
   + **Provisioned IOPS SSD \(io1\)** storage option
   + **Enable deletion protection** option

   We recommend these features for any production environment\. 
**Note**  
Template choices vary by edition\.

1. To enter your master password, do the following:

   1. In the **Settings** section, open **Credential Settings**\.

   1. If you want to specify a password, clear the **Auto generate a password** check box if it is selected\.

   1. \(Optional\) Change the **Master username** value\.

   1. Enter the same password in **Master password** and **Confirm password**\.

1. \(Optional\) Set up a connection to a compute resource for this DB instance\.

   You can configure connectivity between an Amazon EC2 instance and the new DB instance during DB instance creation\. For more information, see [Configure automatic network connectivity with an EC2 instance](#USER_CreateDBInstance.Prerequisites.VPC.Automatic)\.

1. For the remaining sections, specify your DB instance settings\. For information about each setting, see [Settings for DB instances](#USER_CreateDBInstance.Settings)\. 

1. Choose **Create database**\. 

   If you chose to use an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the master user name and password for the DB instance, choose **View credential details**\.

   To connect to the DB instance as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\. If you need to change the master user password after the DB instance is available, modify the DB instance to do so\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

1. For **Databases**, choose the name of the new DB instance\.

   On the RDS console, the details for the new DB instance appear\. The DB instance has a status of **Creating** until the DB instance is created and ready for use\. When the state changes to **Available**, you can connect to the DB instance\. Depending on the DB instance class and storage allocated, it can take several minutes for the new instance to be available\.   
![\[My DB instances details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQLSvr-Launch05.png)

### AWS CLI<a name="USER_CreateDBInstance.CLI"></a>

To create a DB instance by using the AWS CLI, call the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command with the following parameters:
+ `--db-instance-identifier`
+ `--db-instance-class`
+ `--vpc-security-group-ids`
+ `--db-subnet-group`
+ `--engine`
+ `--master-username`
+ `--master-user-password`
+ `--allocated-storage`
+ `--backup-retention-period`

For information about each setting, see [Settings for DB instances](#USER_CreateDBInstance.Settings)\.

This example uses Microsoft SQL Server\.

**Example**  
For Linux, macOS, or Unix:  

```
 1. aws rds create-db-instance \
 2.     --engine sqlserver-se \
 3.     --db-instance-identifier mymsftsqlserver \
 4.     --allocated-storage 250 \
 5.     --db-instance-class db.t3.large \
 6.     --vpc-security-group-ids mysecuritygroup \
 7.     --db-subnet-group mydbsubnetgroup \
 8.     --master-username masterawsuser \
 9.     --master-user-password masteruserpassword \
10.     --backup-retention-period 3
```
For Windows:  

```
 1. aws rds create-db-instance ^
 2.     --engine sqlserver-se ^
 3.     --db-instance-identifier mydbinstance ^
 4.     --allocated-storage 250 ^
 5.     --db-instance-class db.t3.large ^
 6.     --vpc-security-group-ids mysecuritygroup ^
 7.     --db-subnet-group mydbsubnetgroup ^
 8.     --master-username masterawsuser ^ 
 9.     --master-user-password masteruserpassword ^
10.     --backup-retention-period 3
```
This command produces output similar to the following\.   

```
1. DBINSTANCE  mydbinstance  db.t3.large  sqlserver-se  250  sa  creating  3  ****  n  10.50.2789
2. SECGROUP  default  active
3. PARAMGRP  default.sqlserver-se-14  in-sync
```

### RDS API<a name="USER_CreateDBInstance.API"></a>

To create a DB instance by using the Amazon RDS API, call the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) operation\.

For information about each setting, see [Settings for DB instances](#USER_CreateDBInstance.Settings)\. 

## Settings for DB instances<a name="USER_CreateDBInstance.Settings"></a>

In the following table, you can find details about settings that you choose when you create a DB instance\. The table also shows the DB engines for which each setting is supported\.

You can create a DB instance using the console, the [ create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) CLI command, or the [ CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) RDS API operation\.


****  

| Console setting | Setting description | CLI option and RDS API parameter | Supported DB engines | 
| --- | --- | --- | --- | 
|  **Allocated storage**  |  The amount of storage to allocate for your DB instance \(in gibibytes\)\. In some cases, allocating a higher amount of storage for your DB instance than the size of your database can improve I/O performance\. For more information, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.   |  **CLI option:** `--allocated-storage` **API parameter:**  `AllocatedStorage`  |  All  | 
| Architecture settings |  The architecture of the database: CDB \(single\-tenant\) or non\-CDB\. Oracle Database 21c uses CDB architecture only\. Oracle Database 19c can use either CDB or non\-CDB architecture\. Releases lower than Oracle Database 19c use non\-CDB only\. If you choose **Use multitenant architecture**, RDS for Oracle creates a container database \(CDB\)\. This CDB contains one pluggable database \(PDB\)\. If you don't choose this option, RDS for Oracle creates a non\-CDB\. A non\-CDB uses the traditional Oracle architecture\. For more information, see [RDS for Oracle architecture](Oracle.Concepts.single-tenant.md)\.  |  **CLI option:** `--engine oracle-ee-cdb` \(multitenant\) `--engine oracle-se2-cdb` \(multitenant\) `--engine oracle-ee` \(traditional\) `--engine oracle-se2` \(traditional\) **API parameter:** `Engine`  |  Oracle  | 
| Auto minor version upgrade |  **Enable auto minor version upgrade** to enable your DB instance to receive preferred minor DB engine version upgrades automatically when they become available\. Amazon RDS performs automatic minor version upgrades in the maintenance window\. For more information, see [Automatically upgrading the minor engine version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.AutoMinorVersionUpgrades)\.  |  **CLI option:** `--auto-minor-version-upgrade` `--no-auto-minor-version-upgrade` **API parameter:** `AutoMinorVersionUpgrade`  | All | 
|  Availability zone  |  The Availability Zone for your DB instance\. Use the default value of **No Preference** unless you want to specify an Availability Zone\. For more information, see [Regions, Availability Zones, and Local Zones](Concepts.RegionsAndAvailabilityZones.md)\.  |  **CLI option:** `--availability-zone` **API parameter:** `AvailabilityZone`  | All | 
|   **AWS KMS key**   |  Only available if **Encryption** is set to **Enable encryption**\. Choose the AWS KMS key to use for encrypting this DB instance\. For more information, see [Encrypting Amazon RDS resources](Overview.Encryption.md)\.  |  **CLI option:** `--kms-key-id` **API parameter:** `KmsKeyId`  | All | 
| Backup replication |  Choose **Enable replication in another AWS Region** to create backups in an additional Region for disaster recovery\. Then choose the **Destination Region** for the additional backups\.  |  Not available when creating a DB instance\. For information on enabling cross\-Region backups using the AWS CLI or RDS API, see [Enabling cross\-Region automated backups](USER_ReplicateBackups.md#AutomatedBackups.Replicating.Enable)\.  |  Oracle PostgreSQL SQL Server  | 
|  Backup retention period  |  The number of days that you want automatic backups of your DB instance to be retained\. For any nontrivial DB instance, set this value to **1** or greater\. For more information, see [Working with backups](USER_WorkingWithAutomatedBackups.md)\.  |  **CLI option:** `--backup-retention-period` **API parameter:** `BackupRetentionPeriod`  | All | 
| Backup target |  Choose **AWS Cloud** to store automated backups and manual snapshots in the parent AWS Region\. Choose **Outposts \(on\-premises\)** to store them locally on your Outpost\. This option setting applies only to RDS on Outposts\. For more information, see [Creating DB instances for Amazon RDS on AWS Outposts](rds-on-outposts.creating.md)\.  |  **CLI option:** `--backup-target` **API parameter:** `BackupTarget`  | MySQL, PostgreSQL, SQL Server | 
|  Backup window |  The time period during which Amazon RDS automatically takes a backup of your DB instance\. Unless you have a specific time that you want to have your database backed up, use the default of **No Preference**\. For more information, see [Working with backups](USER_WorkingWithAutomatedBackups.md)\.  |  **CLI option:** `--preferred-backup-window` **API parameter:** `PreferredBackupWindow`  | All | 
|  **Certificate authority**  |  The certificate authority \(CA\) for the server certificate used by the DB instance\. For more information, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.   |  **CLI option:** `--ca-certificate-identifier` **RDS API parameter:** `CACertificateIdentifier`  |  All  | 
|  Character set |  The character set for your DB instance\. The default value of **AL32UTF8** for the DB character set is for the Unicode 5\.0 UTF\-8 Universal character set\. You can't change the DB character set after you create the DB instance\.  In a single\-tenant configuration, a non\-default DB character set affects only the PDB, not the CDB\. For more information, see [RDS for Oracle architecture](Oracle.Concepts.single-tenant.md)\. The DB character set is different from the national character set, which is called the NCHAR character set\. Unlike the DB character set, the NCHAR character set specifies the encoding for NCHAR data types \(NCHAR, NVARCHAR2, and NCLOB\) columns without affecting database metadata\. For more information, see [RDS for Oracle character sets](Appendix.OracleCharacterSets.md)\.  |  **CLI option:** `--character-set-name` **API parameter:** `CharacterSetName`  | Oracle | 
| Collation |  A server\-level collation for your DB instance\. For more information, see [Server\-level collation for Microsoft SQL Server](Appendix.SQLServer.CommonDBATasks.Collation.md#Appendix.SQLServer.CommonDBATasks.Collation.Server)\.  |  **CLI option:** `--character-set-name` **API parameter:** `CharacterSetName`  | SQL Server | 
|  Copy tags to snapshots  |  This option copies any DB instance tags to a DB snapshot when you create a snapshot\. For more information, see [Tagging Amazon RDS resources](USER_Tagging.md)\.   |  **CLI option:** `--copy-tags-to-snapshot` `--no-copy-tags-to-snapshot` **RDS API parameter:** `CopyTagsToSnapshot`  | All | 
|  Database authentication  |  The database authentication option that you want to use\. Choose **Password authentication** to authenticate database users with database passwords only\. Choose **Password and IAM DB authentication** to authenticate database users with database passwords and user credentials through users and roles\. For more information, see [IAM database authentication for MariaDB, MySQL, and PostgreSQL](UsingWithRDS.IAMDBAuth.md)\. This option is only supported for MySQL and PostgreSQL\. Choose **Password and Kerberos authentication** to authenticate database users with database passwords and Kerberos authentication through an AWS Managed Microsoft AD created with AWS Directory Service\. Next, choose the directory or choose **Create a new Directory**\. For more information, see one of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateDBInstance.html)  |  ***IAM:*** **CLI option:** `--enable-iam-database-authentication` `--no-enable-iam-database-authentication` **RDS API parameter:** `EnableIAMDatabaseAuthentication` ***Kerberos:*** **CLI option:** `--domain` `--domain-iam-role-name` **RDS API parameter:** `Domain` `DomainIAMRoleName`  |  Varies by authentication type  | 
| Database management type |  Choose **Amazon RDS** if you don't need to customize your environment\. Choose **Amazon RDS Custom** if you want to customize the database, OS, and infrastructure\. For more information, see [Working with Amazon RDS Custom](rds-custom.md)\.  |  For the CLI and API, you specify the database engine type\.  |  Oracle SQL Server  | 
|  Database port  |  The port that you want to access the DB instance through\. The default port is shown\.  The firewalls at some companies block connections to the default MariaDB, MySQL, and PostgreSQL ports\. If your company firewall blocks the default port, enter another port for your DB instance\.   |  **CLI option:** `--port` **RDS API parameter:** `Port`  | All | 
|  DB engine version  |  The version of database engine that you want to use\.  |  **CLI option:** `--engine-version` **RDS API parameter:** `EngineVersion`  | All | 
|  DB instance class  |  The configuration for your DB instance\. For example, a **db\.t3\.small** DB instance class has 2 GiB memory, 2 vCPUs, 1 virtual core, a variable ECU, and a moderate I/O capacity\. If possible, choose a DB instance class large enough that a typical query working set can be held in memory\. When working sets are held in memory, the system can avoid writing to disk, which improves performance\. For more information, see [DB instance classes](Concepts.DBInstanceClass.md)\.  In RDS for Oracle, you can select **Include additional memory configurations**\. These configurations are optimized for a high ratio of memory to vCPU\. For example, **db\.r5\.6xlarge\.tpc2\.mem4x** is a db\.r5\.8x DB instance that has 2 threads per core \(tpc2\) and 4x the memory of a standard db\.r5\.6xlarge DB instance\. For more information, see [RDS for Oracle instance classes](Oracle.Concepts.InstanceClasses.md)\.  |  **CLI option:** `--db-instance-class` **RDS API parameter:** `DBInstanceClass`  | All | 
|  DB instance identifier  |  The name for your DB instance\. Name your DB instances in the same way that you name your on\-premises servers\. Your DB instance identifier can contain up to 63 alphanumeric characters, and must be unique for your account in the AWS Region you chose\.  |  **CLI option:** `--db-instance-identifier` **RDS API parameter:** `DBInstanceIdentifier`  | All | 
|  DB parameter group  |  A parameter group for your DB instance\. You can choose the default parameter group, or you can create a custom parameter group\.  For more information, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.  |  **CLI option:** `--db-parameter-group-name` **RDS API parameter:** `DBParameterGroupName`  | All | 
|  DB subnet group  | The DB subnet group you want to use for the DB cluster\. Select Choose existing to use an existing DB subnet group\. Then choose the required subnet group from the Existing DB subnet groups dropdown list\.Choose **Automatic setup** to let RDS select a compatible DB subnet group\. If none exist, RDS creates a new subnet group for your cluster\.For more information, see [Working with DB subnet groups](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Subnets)\. |  **CLI option:** `--db-subnet-group-name` **RDS API parameter:** `DBSubnetGroupName`  | All | 
| Deletion protection |  **Enable deletion protection** to prevent your DB instance from being deleted\. If you create a production DB instance with the AWS Management Console, deletion protection is enabled by default\. For more information, see [Deleting a DB instance](USER_DeleteInstance.md)\.  |  **CLI option:** `--deletion-protection` `--no-deletion-protection` **RDS API parameter:** `DeletionProtection`  | All | 
|  Encryption  |  **Enable Encryption** to enable encryption at rest for this DB instance\. For more information, see [Encrypting Amazon RDS resources](Overview.Encryption.md)\.  |  **CLI option:** `--storage-encrypted` `--no-storage-encrypted` **RDS API parameter:** `StorageEncrypted`  | All | 
|  Enhanced Monitoring  |  **Enable enhanced monitoring** to enable gathering metrics in real time for the operating system that your DB instance runs on\. For more information, see [Monitoring OS metrics with Enhanced Monitoring](USER_Monitoring.OS.md)\.  |  **CLI options:** `--monitoring-interval` `--monitoring-role-arn` **RDS API parameters:** `MonitoringInterval` `MonitoringRoleArn`  | All | 
|  Engine type  |  Choose the database engine to be used for this DB instance\.  |  **CLI option:** `--engine` **RDS API parameter:** `Engine`  | All | 
|  Initial database name  |  The name for the database on your DB instance\. If you don't provide a name, Amazon RDS doesn't create a database on the DB instance \(except for Oracle and PostgreSQL\)\. The name can't be a word reserved by the database engine, and has other constraints depending on the DB engine\. MariaDB and MySQL: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateDBInstance.html) Oracle: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateDBInstance.html) PostgreSQL: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateDBInstance.html)  |  **CLI option:** `--db-name` **RDS API parameter:** `DBName`  | All except SQL Server | 
|  License |  The license model: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateDBInstance.html)  |  **CLI option:** `--license-model` **RDS API parameter:** `LicenseModel`  |  Oracle SQL Server  | 
|  **Log exports**  |  The types of database log files to publish to Amazon CloudWatch Logs\.  For more information, see [Publishing database logs to Amazon CloudWatch Logs](USER_LogAccess.Procedural.UploadtoCloudWatch.md)\.   |  **CLI option:** `--enable-cloudwatch-logs-exports` **RDS API parameter:** `EnableCloudwatchLogsExports`  |  All  | 
|  Maintenance window  |  The 30\-minute window in which pending modifications to your DB instance are applied\. If the time period doesn't matter, choose **No Preference**\. For more information, see [The Amazon RDS maintenance window](USER_UpgradeDBInstance.Maintenance.md#Concepts.DBMaintenance)\.  |  **CLI option:** `--preferred-maintenance-window` **RDS API parameter:** `PreferredMaintenanceWindow`  | All | 
|  Manage master credentials in AWS Secrets Manager  |  Select **Manage master credentials in AWS Secrets Manager** to manage the master user password in a secret in Secrets Manager\. Optionally, choose a KMS key to use to protect the secret\. Choose from the KMS keys in your account, or enter the key from a different account\. For more information, see [Password management with Amazon RDS and AWS Secrets Manager](rds-secrets-manager.md)\.  |  **CLI option:** `--manage-master-user-password \| --no-manage-master-user-password` `--master-user-secret-kms-key-id` **RDS API parameter:** `ManageMasterUserPassword` `MasterUserSecretKmsKeyId`  | All | 
|  Master password  |  The password for your master user account\. The password has the following number of printable ASCII characters \(excluding `/`, `"`, a space, and `@`\) depending on the DB engine: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateDBInstance.html)  |  **CLI option:** `--master-user-password` **RDS API parameter:** `MasterUserPassword`  | All | 
|  Master username  |  The name that you use as the master user name to log on to your DB instance with all database privileges\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateDBInstance.html) You can't change the master user name after the DB instance is created\. For more information on privileges granted to the master user, see [Master user account privileges](UsingWithRDS.MasterAccounts.md)\.  |  **CLI option:** `--master-username` **RDS API parameter:** `MasterUsername`  | All | 
| Microsoft SQL Server Windows Authentication |  **Enable Microsoft SQL Server Windows authentication**, then **Browse Directory** to choose the directory where you want to allow authorized domain users to authenticate with this SQL Server instance using Windows Authentication\.  |  **CLI options:** `--domain` `--domain-iam-role-name` **RDS API parameters:** `Domain`  `DomainIAMRoleName`  |  SQL Server  | 
|  Multi\-AZ deployment  |  **Create a standby instance** to create a passive secondary replica of your DB instance in another Availability Zone for failover support\. We recommend Multi\-AZ for production workloads to maintain high availability\. For development and testing, you can choose **Do not create a standby instance**\. For more information, see [Multi\-AZ deployments for high availability](Concepts.MultiAZ.md)\.  |  **CLI option:** `--multi-az` `--no-multi-az` **RDS API parameter:** `MultiAZ`  | All | 
| National character set \(NCHAR\) |  The national character set for your DB instance, commonly called the NCHAR character set\. You can set the national character set to either AL16UTF16 \(default\) or UTF\-8\. You can't change the national character set after you create the DB instance\.  The national character set is different from the DB character set\. Unlike the DB character set, the national character set specifies the encoding only for NCHAR data types \(NCHAR, NVARCHAR2, and NCLOB\) columns without affecting database metadata\. For more information, see [RDS for Oracle character sets](Appendix.OracleCharacterSets.md)\.  |  **CLI option:** `--nchar-character-set-name` **API parameter:** `NcharCharacterSetName`  | Oracle | 
|  Network type  |  The IP addressing protocols supported by the DB instance\. **IPv4** \(the default\) to specify that resources can communicate with the DB instance only over the Internet Protocol version 4 \(IPv4\) addressing protocol\. **Dual\-stack mode** to specify that resources can communicate with the DB instance over IPv4, Internet Protocol version 6 \(IPv6\), or both\. Use dual\-stack mode if you have any resources that must communicate with your DB instance over the IPv6 addressing protocol\. Also, make sure that you associate an IPv6 CIDR block with all subnets in the DB subnet group that you specify\. For more information, see [Amazon RDS IP addressing](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.IP_addressing)\.  |  **CLI option:** `--network-type` **RDS API parameter:** `NetworkType`  |  All  | 
|  Option group  |  An option group for your DB instance\. You can choose the default option group or you can create a custom option group\. For more information, see [Working with option groups](USER_WorkingWithOptionGroups.md)\.  |  **CLI option:** `--option-group-name` **RDS API parameter:** `OptionGroupName`  | All | 
| Performance Insights |  **Enable Performance Insights** to monitor your DB instance load so that you can analyze and troubleshoot your database performance\. Choose a retention period to determine how much Performance Insights data history to keep\. The retention setting in the free tier is **Default \(7 days\)**\. To retain your performance data for longer, specify 1–24 months\. For more information about retention periods, see [Pricing and data retention for Performance Insights](USER_PerfInsights.Overview.cost.md)\. Choose a KMS key to use to protect the key used to encrypt this database volume\. Choose from the KMS keys in your account, or enter the key from a different account\. For more information, see [Monitoring DB load with Performance Insights on Amazon RDS](USER_PerfInsights.md)\.  |  **CLI options:** `--enable-performance-insights` `--no-enable-performance-insights` `--performance-insights-retention-period` `--performance-insights-kms-key-id` **RDS API parameters:** `EnablePerformanceInsights` `PerformanceInsightsRetentionPeriod` `PerformanceInsightsKMSKeyId`  | All | 
|  **Provisioned IOPS**  |  The Provisioned IOPS \(I/O operations per second\) value for the DB instance\. This setting is available only if you choose one of the following for **Storage type**: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateDBInstance.html) For more information, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.  |  **CLI option:** `--iops` **RDS API parameter:** `Iops`  |  All  | 
|  Public access  |  **Yes** to give the DB instance a public IP address, meaning that it's accessible outside the VPC\. To be publicly accessible, the DB instance also has to be in a public subnet in the VPC\. **No** to make the DB instance accessible only from inside the VPC\. For more information, see [Hiding a DB instance in a VPC from the internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\. To connect to a DB instance from outside of its VPC, the DB instance must be publicly accessible\. Also, access must be granted using the inbound rules of the DB instance's security group\. In addition, other requirements must be met\. For more information, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\. If your DB instance isn't publicly accessible, use an AWS Site\-to\-Site VPN connection or an AWS Direct Connect connection to access it from a private network\. For more information, see [Internetwork traffic privacy](inter-network-traffic-privacy.md)\.  |  **CLI option:** `--publicly-accessible` `--no-publicly-accessible` **RDS API parameter:** `PubliclyAccessible`  | All | 
|  **RDS Proxy**  |  Choose **Create an RDS Proxy** to create a proxy for your DB instance\. Amazon RDS automatically creates an IAM role and a Secrets Manager secret for the proxy\. For more information, see [Using Amazon RDS Proxy](rds-proxy.md)\.   |  Not available when creating a DB instance\.  |  MariaDB MySQL PostgreSQL  | 
|  Storage autoscaling  |  **Enable storage autoscaling** to enable Amazon RDS to automatically increase storage when needed to avoid having your DB instance run out of storage space\. Use **Maximum storage threshold** to set the upper limit for Amazon RDS to automatically increase storage for your DB instance\. The default is 1,000 GiB\. For more information, see [Managing capacity automatically with Amazon RDS storage autoscaling](USER_PIOPS.StorageTypes.md#USER_PIOPS.Autoscaling)\.   |  **CLI option:** `--max-allocated-storage` **RDS API parameter:** `MaxAllocatedStorage`  | All | 
|  **Storage throughput**  |  The storage throughput value for the DB instance\. This setting is available only if you choose **General purpose SSD \(gp3\)** for **Storage type**\. For more information, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.  |  **CLI option:** `--storage-throughput` **RDS API parameter:** `StorageThroughput`  | All | 
|  Storage type  |  The storage type for your DB instance\. If you choose **General Purpose SSD \(gp3\)**, you can provision additional provisioned IOPS and storage throughput under **Advanced settings**\. If you choose **Provisioned IOPS SSD \(io1\)**, enter the **Provisioned IOPS** value\. For more information, see [Amazon RDS storage types](CHAP_Storage.md#Concepts.Storage)\.  |  **CLI option:** `--storage-type` **RDS API parameter:** `StorageType`  | All | 
|  Subnet group  |  A DB subnet group to associate with this DB instance\. For more information, see [Working with DB subnet groups](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Subnets)\.  |  **CLI option:** `--db-subnet-group-name` **RDS API parameter:** `DBSubnetGroupName`  | All | 
|  Time zone  |  The time zone for your DB instance\. If you don't choose a time zone, your DB instance uses the default time zone\. You can't change the time zone after the DB instance is created\. For more information, see [Local time zone for Microsoft SQL Server DB instances](CHAP_SQLServer.md#SQLServer.Concepts.General.TimeZone)\.  |  **CLI option:** `--timezone` **RDS API parameter:** `Timezone`  | SQL Server | 
|  Virtual Private Cloud \(VPC\)  |  A VPC based on the Amazon VPC service to associate with this DB instance\. For more information, see [Amazon VPC VPCs and Amazon RDS](USER_VPC.md)\.  |  For the CLI and API, you specify the VPC security group IDs\.  | All | 
|  VPC security group \(firewall\)  |  The security group to associate with the DB instance\. For more information, see [Overview of VPC security groups](Overview.RDSSecurityGroups.md#Overview.RDSSecurityGroups.VPCSec)\.  |  **CLI option:** `--vpc-security-group-ids` **RDS API parameter:** `VpcSecurityGroupIds`  | All | 