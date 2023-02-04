# Creating a Multi\-AZ DB cluster<a name="create-multi-az-db-cluster"></a>

A Multi\-AZ DB cluster has a writer DB instance and two reader DB instances in three separate Availability Zones\. Multi\-AZ DB clusters provide high availability, increased capacity for read workloads, and lower latency when compared to Multi\-AZ deployments\. For more information about Multi\-AZ DB clusters, see [Multi\-AZ DB cluster deployments](multi-az-db-clusters-concepts.md)\.

**Note**  
Multi\-AZ DB clusters are supported only for the MySQL and PostgreSQL DB engines\.

## DB cluster prerequisites<a name="create-multi-az-db-cluster-prerequisites"></a>

**Important**  
Before you can create a Multi\-AZ DB cluster, you must complete the tasks in [Setting up for Amazon RDS](CHAP_SettingUp.md)\.

The following are prerequisites to complete before creating a Multi\-AZ DB cluster\.

**Topics**
+ [Configure the network for the DB cluster](#create-multi-az-db-cluster-prerequisites-VPC)
+ [Additional prerequisites](#create-multi-az-db-cluster-prerequisites-additional)

### Configure the network for the DB cluster<a name="create-multi-az-db-cluster-prerequisites-VPC"></a>

You can create a Multi\-AZ DB cluster only in a virtual private cloud \(VPC\) based on the Amazon VPC service\. It must be in an AWS Region that has at least three Availability Zones\. The DB subnet group that you choose for the DB cluster must cover at least three Availability Zones\. This configuration ensures that each DB instance in the DB cluster is in a different Availability Zone\.

To set up connectivity between your new DB cluster and an Amazon EC2 instance in the same VPC, do so when you create the DB cluster\. To connect to your DB cluster from resources other than EC2 instances in the same VPC, configure the network connections manually\.

**Topics**
+ [Configure automatic network connectivity with an EC2 instance](#create-multi-az-db-cluster-prerequisites-VPC-automatic)
+ [Configure the network manually](#create-multi-az-db-cluster-prerequisites-VPC-manual)

#### Configure automatic network connectivity with an EC2 instance<a name="create-multi-az-db-cluster-prerequisites-VPC-automatic"></a>

When you create a Multi\-AZ DB cluster, you can use the AWS Management Console to set up connectivity between an EC2 instance and the new DB cluster\. When you do so, RDS configures your VPC and network settings automatically\. The DB cluster is created in the same VPC as the EC2 instance so that the EC2 instance can access the DB cluster\.

The following are requirements for connecting an EC2 instance with the DB cluster:
+ The EC2 instance must exist in the AWS Region before you create the DB cluster\.

  If no EC2 instances exist in the AWS Region, the console provides a link to create one\.
+ The user who is creating the DB cluster must have permissions to perform the following operations:
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

Using this option creates a private DB cluster\. The DB cluster uses a DB subnet group with only private subnets to restrict access to resources within the VPC\.

To connect an EC2 instance to the DB cluster, choose **Connect to an EC2 compute resource** in the **Connectivity** section on the **Create database** page\.

![\[Connect an EC2 instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ec2-set-up-connection-create.png)

When you choose **Connect to an EC2 compute resource**, RDS sets the following options automatically\. You can't change these settings unless you choose not to set up connectivity with an EC2 instance by choosing **Don't connect to an EC2 compute resource**\.


****  

| Console option | Automatic setting | 
| --- | --- | 
|  **Virtual Private Cloud \(VPC\)**  |  RDS sets the VPC to the one associated with the EC2 instance\.  | 
|  **DB subnet group**  | RDS requires a DB subnet group with a private subnet in the same Availability Zone as the EC2 instance\. If a DB subnet group that meets this requirement exists, then RDS uses the existing DB subnet group\. By default, this option is set to Automatic setup\. When you choose **Automatic setup** and there is no DB subnet group that meets this requirement, the following action happens\. RDS uses three available private subnets in three Availability Zones where one of the Availability Zones is the same as the EC2 instance\. If a private subnet isn’t available in an Availability Zone, RDS creates a private subnet in the Availability Zone\. Then RDS creates the DB subnet group\.When a private subnet is available, RDS uses the route table associated with the subnet and adds any subnets it creates to this route table\. When no private subnet is available, RDS creates a route table without internet gateway access and adds the subnets it creates to the route table\.RDS also allows you to use existing DB subnet groups\. Select **Choose existing** if you want to use an existing DB subnet group of your choice\. | 
|  **Public access**  |  RDS chooses **No** so that the DB cluster isn't publicly accessible\. For security, it is a best practice to keep the database private and make sure it isn't accessible from the internet\.  | 
|  **VPC security group \(firewall\)**  |  RDS creates a new security group that is associated with the DB cluster\. The security group is named `rds-ec2-n`, where `n` is a number\. This security group includes an inbound rule with the EC2 VPC security group \(firewall\) as the source\. This security group that is associated with the DB cluster allows the EC2 instance to access the DB cluster\. RDS also creates a new security group that is associated with the EC2 instance\. The security group is named `ec2-rds-n`, where `n` is a number\. This security group includes an outbound rule with the VPC security group of the DB cluster as the source\. This security group allows the EC2 instance to send traffic to the DB cluster\. You can add another new security group by choosing **Create new** and typing the name of the new security group\. You can add existing security groups by choosing **Choose existing** and selecting security groups to add\.  | 
|  **Availability Zone**  |  RDS chooses the Availability Zone of the EC2 instance for one DB instance in the Multi\-AZ DB cluster deployment\. RDS randomly chooses a different Availability Zone for both of the other DB instances\. The writer DB instance is created in the same Availability Zone as the EC2 instance\. There is the possibility of cross Availability Zone costs if a failover occurs and the writer DB instance is in a different Availability Zone\.  | 

For more information about these settings, see [Settings for creating Multi\-AZ DB clusters](#create-multi-az-db-cluster-settings)\.

If you change these settings after the DB cluster is created, the changes might affect the connection between the EC2 instance and the DB cluster\.

#### Configure the network manually<a name="create-multi-az-db-cluster-prerequisites-VPC-manual"></a>

To connect to your DB cluster from resources other than EC2 instances in the same VPC, configure the network connections manually\. If you use the AWS Management Console to create your Multi\-AZ DB cluster, you can have Amazon RDS automatically create a VPC for you\. Or you can use an existing VPC or create a new VPC for your Multi\-AZ DB cluster\. Your VPC must have at least one subnet in each of at least three Availability Zones for you to use it with a Multi\-AZ DB cluster\. For information on VPCs, see [Amazon VPC VPCs and Amazon RDS](USER_VPC.md)\.

If you don't have a default VPC or you haven't created a VPC, and you don't plan to use the console, do the following:
+ Create a VPC with at least one subnet in each of at least three of the Availability Zones in the AWS Region where you want to deploy your DB cluster\. For more information, see [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#Overview.RDSVPC.Create)\.
+ Specify a VPC security group that authorizes connections to your DB cluster\. For more information, see [Provide access to your DB instance in your VPC by creating a security group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup) and [Controlling access with security groups](Overview.RDSSecurityGroups.md)\.
+ Specify an RDS DB subnet group that defines at least three subnets in the VPC that can be used by the Multi\-AZ DB cluster\. For more information, see [Working with DB subnet groups](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Subnets)\.

For information about limitations that apply to Multi\-AZ DB clusters, see [Limitations for Multi\-AZ DB clusters](multi-az-db-clusters-concepts.md#multi-az-db-clusters-concepts.Limitations)\.

If you want to connect to a resource that isn't in the same VPC as the Multi\-AZ DB cluster, see the appropriate scenarios in [Scenarios for accessing a DB instance in a VPC](USER_VPC.Scenarios.md)\.

### Additional prerequisites<a name="create-multi-az-db-cluster-prerequisites-additional"></a>

Before you create your Multi\-AZ DB cluster, consider the following additional prerequisites:
+ To connect to AWS using AWS Identity and Access Management \(IAM\) credentials, your AWS account must have certain IAM policies\. These grant the permissions required to perform Amazon RDS operations\. For more information, see [Identity and access management for Amazon RDS](UsingWithRDS.IAM.md)\.

  If you use IAM to access the RDS console, first sign in to the AWS Management Console with your IAM user credentials\. Then go to the RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.
+ To tailor the configuration parameters for your DB cluster, specify a DB cluster parameter group with the required parameter settings\. For information about creating or modifying a DB cluster parameter group, see [Working with parameter groups for Multi\-AZ DB clusters](multi-az-db-clusters-concepts.md#multi-az-db-clusters-concepts-parameter-groups)\.
+ Determine the TCP/IP port number to specify for your DB cluster\. The firewalls at some companies block connections to the default ports\. If your company firewall blocks the default port, choose another port for your DB cluster\. All DB instances in a DB cluster use the same port\.

## Creating a DB cluster<a name="create-multi-az-db-cluster-creating"></a>

You can create a Multi\-AZ DB cluster using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="create-multi-az-db-cluster-creating-console"></a>

You can create a Multi\-AZ DB cluster by choosing **Multi\-AZ DB cluster** in the **Availability and durability** section\.

**To create a Multi\-AZ DB cluster using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region in which you want to create the DB cluster\.

   For information about the AWS Regions that support Multi\-AZ DB clusters, see [Limitations for Multi\-AZ DB clusters](multi-az-db-clusters-concepts.md#multi-az-db-clusters-concepts.Limitations)\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**\.

   To create a Multi\-AZ DB cluster, make sure that **Standard Create** is selected and **Easy Create** isn't\.

1. In **Engine type**, choose **MySQL** or **PostgreSQL**\.

1. For **Version**, choose the DB engine version\.

   For information about the DB engine versions that support Multi\-AZ DB clusters, see [Limitations for Multi\-AZ DB clusters](multi-az-db-clusters-concepts.md#multi-az-db-clusters-concepts.Limitations)\.

1. In **Templates**, choose the appropriate template for your deployment\.

1. In **Availability and durability**, choose **Multi\-AZ DB cluster**\.  
![\[Multi-AZ DB cluster choice\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-create.png)

1. In **DB cluster identifier**, enter the identifier for your DB cluster\.

1. In **Master username**, enter your master user name, or keep the default setting\.

1. Enter your master password:

   1. In the **Settings** section, open **Credential Settings**\.

   1. If you want to specify a password, clear the **Auto generate a password** box if it is selected\.

   1. \(Optional\) Change the **Master username** value\.

   1. Enter the same password in **Master password** and **Confirm password**\.

1. In **DB instance class**, choose a DB instance class\.

1. \(Optional\) Set up a connection to a compute resource for this DB cluster\.

   You can configure connectivity between an Amazon EC2 instance and the new DB cluster during DB cluster creation\. For more information, see [Configure automatic network connectivity with an EC2 instance](#create-multi-az-db-cluster-prerequisites-VPC-automatic)\.

1. For the remaining sections, specify your DB cluster settings\. For information about each setting, see [Settings for creating Multi\-AZ DB clusters](#create-multi-az-db-cluster-settings)\.

1. Choose **Create database**\. 

   If you chose to use an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the master user name and password for the DB cluster, choose **View credential details**\.

   To connect to the DB cluster as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\.

1. For **Databases**, choose the name of the new DB cluster\.

On the RDS console, the details for the new DB cluster appear\. The DB cluster has a status of **Creating** until the DB cluster is created and ready for use\. When the state changes to **Available**, you can connect to the DB cluster\. Depending on the DB cluster class and storage allocated, it can take several minutes for the new DB cluster to be available\. 

### AWS CLI<a name="create-multi-az-db-cluster-creating-cli"></a>

Before you create a Multi\-AZ DB cluster using the AWS CLI, make sure to fulfill the required prerequisites\. These include creating a VPC and an RDS DB subnet group\. For more information, see [DB cluster prerequisites](#create-multi-az-db-cluster-prerequisites)\.

To create a Multi\-AZ DB cluster by using the AWS CLI, call the [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) command\. Specify the `--db-cluster-identifier`\. For the `--engine` option, specify either `mysql` or `postgres`\.

For information about each option, see [Settings for creating Multi\-AZ DB clusters](#create-multi-az-db-cluster-settings)\.

For information about the AWS Regions, DB engines, and DB engine versions that support Multi\-AZ DB clusters, see [Limitations for Multi\-AZ DB clusters](multi-az-db-clusters-concepts.md#multi-az-db-clusters-concepts.Limitations)\.

The `create-db-cluster` command creates the writer DB instance for your DB cluster, and two reader DB instances\. Each DB instance is in a different Availability Zone\.

For example, the following command creates a MySQL 8\.0 Multi\-AZ DB cluster named `mysql-multi-az-db-cluster`\.

**Example**  
For Linux, macOS, or Unix:  

```
 1. aws rds create-db-cluster \
 2.    --db-cluster-identifier mysql-multi-az-db-cluster \
 3.    --engine mysql \
 4.    --engine-version 8.0.28  \
 5.    --master-user-password password \
 6.    --master-username admin \
 7.    --port 3306 \
 8.    --backup-retention-period 1  \
 9.    --db-subnet-group-name default \
10.    --allocated-storage 4000 \
11.    --storage-type io1 \
12.    --iops 10000 \
13.    --db-cluster-instance-class db.r6gd.xlarge
```
For Windows:  

```
 1. aws rds create-db-cluster ^
 2.    --db-cluster-identifier mysql-multi-az-db-cluster ^
 3.    --engine mysql ^
 4.    --engine-version 8.0.28 ^
 5.    --master-user-password password ^
 6.    --master-username admin ^
 7.    --port 3306 ^
 8.    --backup-retention-period 1 ^
 9.    --db-subnet-group-name default ^
10.    --allocated-storage 4000 ^
11.    --storage-type io1 ^
12.    --iops 10000 ^
13.    --db-cluster-instance-class db.r6gd.xlarge
```

The following command creates a PostgreSQL 13\.4 Multi\-AZ DB cluster named `postgresql-multi-az-db-cluster`\.

**Example**  
For Linux, macOS, or Unix:  

```
 1. aws rds create-db-cluster \
 2.    --db-cluster-identifier postgresql-multi-az-db-cluster \
 3.    --engine postgres \
 4.    --engine-version 13.4 \
 5.    --master-user-password password \
 6.    --master-username postgres \
 7.    --port 5432 \
 8.    --backup-retention-period 1  \
 9.    --db-subnet-group-name default \
10.    --allocated-storage 4000 \
11.    --storage-type io1 \
12.    --iops 10000 \
13.    --db-cluster-instance-class db.r6gd.xlarge
```
For Windows:  

```
 1. aws rds create-db-cluster ^
 2.    --db-cluster-identifier postgresql-multi-az-db-cluster ^
 3.    --engine postgres ^
 4.    --engine-version 13.4 ^
 5.    --master-user-password password ^
 6.    --master-username postgres ^
 7.    --port 5432 ^
 8.    --backup-retention-period 1 ^
 9.    --db-subnet-group-name default ^
10.    --allocated-storage 4000 ^
11.    --storage-type io1 ^
12.    --iops 10000 ^
13.    --db-cluster-instance-class db.r6gd.xlarge
```

### RDS API<a name="create-multi-az-db-cluster-creating-api"></a>

Before you can create a Multi\-AZ DB cluster using the RDS API, make sure to fulfill the required prerequisites, such as creating a VPC and an RDS DB subnet group\. For more information, see [DB cluster prerequisites](#create-multi-az-db-cluster-prerequisites)\.

To create a Multi\-AZ DB cluster by using the RDS API, call the [ CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) operation\. Specify the `DBClusterIdentifier`\. For the `Engine` parameter, specify either `mysql` or `postgresql`\.

For information about each option, see [Settings for creating Multi\-AZ DB clusters](#create-multi-az-db-cluster-settings)\.

The `CreateDBCluster` operation creates the writer DB instance for your DB cluster, and two reader DB instances\. Each DB instance is in a different Availability Zone\.

## Settings for creating Multi\-AZ DB clusters<a name="create-multi-az-db-cluster-settings"></a>

For details about settings that you choose when you create a Multi\-AZ DB cluster, see the following table\. For more information about the AWS CLI options, see [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html)\. For more information about the RDS API parameters, see [ CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html)\.


| Console setting | Setting description | CLI option and RDS API parameter | 
| --- | --- | --- | 
|  **Allocated storage**  |  The amount of storage to allocate for each DB instance in your DB cluster \(in gibibyte\)\. For more information, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.   |  **CLI option:** `--allocated-storage` **API parameter:**  `AllocatedStorage`  | 
| Auto minor version upgrade |  **Enable auto minor version upgrade** to have your DB cluster receive preferred minor DB engine version upgrades automatically when they become available\. Amazon RDS performs automatic minor version upgrades in the maintenance window\.  |  **CLI option:** `--auto-minor-version-upgrade` `--no-auto-minor-version-upgrade` **API parameter:** `AutoMinorVersionUpgrade`  | 
|  Backup retention period  |  The number of days that you want automatic backups of your DB cluster to be retained\. For a Multi\-AZ DB cluster, this value must be set to **1** or greater\. For more information, see [Working with backups](USER_WorkingWithAutomatedBackups.md)\.  |  **CLI option:** `--backup-retention-period` **API parameter:** `BackupRetentionPeriod`  | 
|  Backup window |  The time period during which Amazon RDS automatically takes a backup of your DB cluster\. Unless you have a specific time that you want to have your database backed up, use the default of **No preference**\. For more information, see [Working with backups](USER_WorkingWithAutomatedBackups.md)\.  |  **CLI option:** `--preferred-backup-window` **API parameter:** `PreferredBackupWindow`  | 
|  Copy tags to snapshots  |  This option copies any DB cluster tags to a DB snapshot when you create a snapshot\. For more information, see [Tagging Amazon RDS resources](USER_Tagging.md)\.   |  **CLI option:** `-copy-tags-to-snapshot` `-no-copy-tags-to-snapshot` **RDS API parameter:** `CopyTagsToSnapshot`  | 
|  Database authentication  |  For Multi\-AZ DB clusters, only **Password authentication** is supported\.  |  None because password authentication is the default\.  | 
|  Database port  |  The port that you want to access the DB cluster through\. The default port is shown\. The port can't be changed after the DB cluster is created\. The firewalls at some companies block connections to the default ports\. If your company firewall blocks the default port, enter another port for your DB cluster\.  |  **CLI option:** `--port` **RDS API parameter:** `Port`  | 
|  DB cluster identifier  |  The name for your DB cluster\. Name your DB clusters in the same way that you name your on\-premises servers\. Your DB cluster identifier can contain up to 63 alphanumeric characters, and must be unique for your account in the AWS Region you chose\.  |  **CLI option:** `--db-cluster-identifier` **RDS API parameter:** `DBClusterIdentifier`  | 
|  DB cluster instance class  |  The compute and memory capacity of each DB instance in the Multi\-AZ DB cluster, for example `db.r6gd.xlarge`\.  If possible, choose a DB instance class large enough that a typical query working set can be held in memory\. When working sets are held in memory the system can avoid writing to disk, which improves performance\. Currently, Multi\-AZ DB clusters only support db\.m6gd and db\.r6gd DB instance classes\. For more information about DB instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\.  |  **CLI option:** `--db-cluster-instance-class` **RDS API parameter:** `DBClusterInstanceClass`  | 
|  **DB cluster parameter group**  |  The DB cluster parameter group that you want associated with the DB cluster\.  For more information, see [Working with parameter groups for Multi\-AZ DB clusters](multi-az-db-clusters-concepts.md#multi-az-db-clusters-concepts-parameter-groups)\.   |  **CLI option:** `--db-cluster-parameter-group-name` **RDS API parameter:** `DBClusterParameterGroupName`  | 
|  DB engine version  |  The version of database engine that you want to use\.  |  **CLI option:** `--engine-version` **RDS API parameter:** `EngineVersion`  | 
|  DB parameter group  |  The DB instance parameter group that you want associated with the DB instances in the DB cluster\. For more information, see [Working with parameter groups for Multi\-AZ DB clusters](multi-az-db-clusters-concepts.md#multi-az-db-clusters-concepts-parameter-groups)\.  |  Not applicable\. Amazon RDS associates each DB instance with the appropriate default parameter group\.  | 
|  DB subnet group  | The DB subnet group you want to use for the DB cluster\. Select Choose existing to use an existing DB subnet group\. Then choose the required subnet group from the Existing DB subnet groups dropdown list\.Choose **Automatic setup** to let RDS select a compatible DB subnet group\. If none exist, RDS creates a new subnet group for your cluster\.For more information, see [Working with DB subnet groups](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Subnets)\. |  **CLI option:** `--db-subnet-group-name` **RDS API parameter:** `DBSubnetGroupName`  | 
| Deletion protection |  **Enable deletion protection** to prevent your DB cluster from being deleted\. If you create a production DB cluster with the console, deletion protection is turned on by default\. For more information, see [Deleting a DB instance](USER_DeleteInstance.md)\.  |  **CLI option:** `--deletion-protection` `--no-deletion-protection` **RDS API parameter:** `DeletionProtection`  | 
|  Encryption  |  **Enable Encryption** to turn on encryption at rest for this DB cluster\. Encryption is turned on by default for Multi\-AZ DB clusters\. For more information, see [Encrypting Amazon RDS resources](Overview.Encryption.md)\.  |  **CLI options:** `--kms-key-id` `--storage-encrypted` `--no-storage-encrypted` **RDS API parameters:** `KmsKeyId` `StorageEncrypted`  | 
|  Enhanced Monitoring  |  **Enable enhanced monitoring** to turn on metrics gathering in real time for the operating system that your DB cluster runs on\. For more information, see [Monitoring OS metrics with Enhanced Monitoring](USER_Monitoring.OS.md)\.  |  **CLI options:** `--monitoring-interval` `--monitoring-role-arn` **RDS API parameters:** `MonitoringInterval` `MonitoringRoleArn`  | 
|  Initial database name  |  The name for the database on your DB cluster\. If you don't provide a name, Amazon RDS doesn't create a database on the DB cluster for MySQL\. However, it does create a database on the DB cluster for PostgreSQL\. The name can't be a word reserved by the database engine\. It has other constraints depending on the DB engine\. MySQL: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/create-multi-az-db-cluster.html) PostgreSQL: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/create-multi-az-db-cluster.html)  |  **CLI option:** `--database-name` **RDS API parameter:** `DatabaseName`  | 
|  **Log exports**  |  The types of database log files to publish to Amazon CloudWatch Logs\.  For more information, see [Publishing database logs to Amazon CloudWatch Logs](USER_LogAccess.Procedural.UploadtoCloudWatch.md)\.   |  **CLI option:** `-enable-cloudwatch-logs-exports` **RDS API parameter:** `EnableCloudwatchLogsExports`  | 
|  Maintenance window  |  The 30\-minute window in which pending modifications to your DB cluster are applied\. If the time period doesn't matter, choose **No preference**\. For more information, see [The Amazon RDS maintenance window](USER_UpgradeDBInstance.Maintenance.md#Concepts.DBMaintenance)\.  |  **CLI option:** `--preferred-maintenance-window` **RDS API parameter:** `PreferredMaintenanceWindow`  | 
|  Manage master credentials in AWS Secrets Manager  |  Select **Manage master credentials in AWS Secrets Manager** to manage the master user password in a secret in Secrets Manager\. Optionally, choose a KMS key to use to protect the secret\. Choose from the KMS keys in your account, or enter the key from a different account\. For more information, see [Password management with Amazon RDS and AWS Secrets Manager](rds-secrets-manager.md)\.  |  **CLI option:** `--manage-master-user-password \| --no-manage-master-user-password` `--master-user-secret-kms-key-id` **RDS API parameter:** `ManageMasterUserPassword` `MasterUserSecretKmsKeyId`  | 
|  Master password  |  The password for your master user account\.  |  **CLI option:** `--master-user-password` **RDS API parameter:** `MasterUserPassword`  | 
|  Master username  |  The name that you use as the master user name to log on to your DB cluster with all database privileges\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/create-multi-az-db-cluster.html) You can't change the master user name after the Multi\-AZ DB cluster is created\. For more information on privileges granted to the master user, see [Master user account privileges](UsingWithRDS.MasterAccounts.md)\.  |  **CLI option:** `--master-username` **RDS API parameter:** `MasterUsername`  | 
| Performance Insights |  **Enable Performance Insights** to monitor your DB cluster load so that you can analyze and troubleshoot your database performance\. Choose a retention period to determine how much Performance Insights data history to keep\. The retention setting in the free tier is **Default \(7 days\)**\. To retain your performance data for longer, specify 1–24 months\. For more information about retention periods, see [Pricing and data retention for Performance Insights](USER_PerfInsights.Overview.cost.md)\. Choose a master key to use to protect the key used to encrypt this database volume\. Choose from the master keys in your account, or enter the key from a different account\. For more information, see [Monitoring DB load with Performance Insights on Amazon RDS](USER_PerfInsights.md)\.  |  **CLI options:** `--enable-performance-insights` `--no-enable-performance-insights` `--performance-insights-retention-period` `--performance-insights-kms-key-id` **RDS API parameters:** `EnablePerformanceInsights` `PerformanceInsightsRetentionPeriod` `PerformanceInsightsKMSKeyId`  | 
|  Provisioned IOPS  |  The amount of Provisioned IOPS \(input/output operations per second\) to be initially allocated for the DB cluster\. This setting is available only if Provisioned IOPS \(`io1`\) is selected as the storage type\. For more information, see [Provisioned IOPS SSD storage](CHAP_Storage.md#USER_PIOPS)\.   |  **CLI option:** `--iops` **RDS API parameter:** `Iops`  | 
|  Public access  |  **Publicly accessible** to give the DB cluster a public IP address, meaning that it's accessible outside the VPC\. To be publicly accessible, the DB cluster also has to be in a public subnet in the VPC\. **Not publicly accessible** to make the DB cluster accessible only from inside the VPC\. For more information, see [Hiding a DB instance in a VPC from the internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\. To connect to a DB cluster from outside of its VPC, the DB cluster must be publicly accessible\. Also, access must be granted using the inbound rules of the DB cluster's security group, and other requirements must be met\. For more information, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.  If your DB cluster isn't publicly accessible, you can use an AWS Site\-to\-Site VPN connection or an AWS Direct Connect connection to access it from a private network\. For more information, see [Internetwork traffic privacy](inter-network-traffic-privacy.md)\.  |  **CLI option:** `--publicly-accessible` `--no-publicly-accessible` **RDS API parameter:** `PubliclyAccessible`  | 
|  Storage type  |  The storage type for your DB cluster\.   Only Provisioned IOPS \(io1\) storage is supported\. General Purpose \(gp2\) and General Purpose \(gp3\) storage aren't supported\. For more information, see [Amazon RDS storage types](CHAP_Storage.md#Concepts.Storage)\.  |  **CLI option:** `--storage-type` **RDS API parameter:** `StorageType`  | 
|  Virtual Private Cloud \(VPC\)  |  A VPC based on the Amazon VPC service to associate with this DB cluster\. For more information, see [Amazon VPC VPCs and Amazon RDS](USER_VPC.md)\.   |  For the CLI and API, you specify the VPC security group IDs\.  | 
|  VPC security group \(firewall\)  |  The security groups to associate with the DB cluster\. For more information, see [Overview of VPC security groups](Overview.RDSSecurityGroups.md#Overview.RDSSecurityGroups.VPCSec)\.   |  **CLI option:** `--vpc-security-group-ids` **RDS API parameter:** `VpcSecurityGroupIds`  | 

## Settings that don't apply when creating Multi\-AZ DB clusters<a name="create-multi-az-db-cluster-settings-not-applicable"></a>

The following settings in the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) don't apply to Multi\-AZ DB clusters\.

You also can't specify these settings for Multi\-AZ DB clusters in the console\.


| AWS CLI setting | RDS API setting | 
| --- | --- | 
|  `--availability-zones`  |  `AvailabilityZones`  | 
|  `--backtrack-window`  |  `BacktrackWindow`  | 
|  `--character-set-name`  |  `CharacterSetName`  | 
|  `--domain`  |  `Domain`  | 
|  `--domain-iam-role-name`  |  `DomainIAMRoleName`  | 
|  `--enable-global-write-forwarding \| --no-enable-global-write-forwarding`  |  `EnableGlobalWriteForwarding`  | 
|  `--enable-http-endpoint \| --no-enable-http-endpoint`  |  `EnableHttpEndpoint`  | 
|  `--enable-iam-database-authentication \| --no-enable-iam-database-authentication`  |  `EnableIAMDatabaseAuthentication`  | 
|  `--global-cluster-identifier`  |  `GlobalClusterIdentifier`  | 
|  `--option-group-name`  |  `OptionGroupName`  | 
|  `--pre-signed-url`  |  `PreSignedUrl`  | 
|  `--replication-source-identifier`  |  `ReplicationSourceIdentifier`  | 
|  `--scaling-configuration`  |  `ScalingConfiguration`  | 