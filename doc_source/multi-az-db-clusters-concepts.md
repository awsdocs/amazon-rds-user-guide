# Multi\-AZ DB cluster deployments \(preview\)<a name="multi-az-db-clusters-concepts"></a>


|  | 
| --- |
| Multi\-AZ DB clusters are in preview for RDS for MySQL and RDS for PostgreSQL and are subject to change\. | 

A *Multi\-AZ DB cluster deployment* is a high availability deployment mode of Amazon RDS with two readable standby DB instances\. A Multi\-AZ DB cluster has a writer DB instance and two reader DB instances in three separate Availability Zones in the same AWS Region\. Multi\-AZ DB clusters provide high availability, increased capacity for read workloads, and lower write latency when compared to Multi\-AZ DB instance deployments\.

**Topics**
+ [Overview of Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-overview)
+ [Supported DB instance classes for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-db-instance-classes)
+ [Creating a Multi\-AZ DB cluster](#create-multi-az-db-cluster)
+ [Managing connections for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-connection-management)
+ [Managing a Multi\-AZ DB cluster with the AWS Management Console](#multi-az-db-clusters-concepts-managing-console)
+ [Modifying a Multi\-AZ DB cluster](#modify-multi-az-db-cluster)
+ [Rebooting Multi\-AZ DB clusters and reader DB instances](#multi-az-db-clusters-concepts-rebooting)
+ [Working with parameter groups for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-parameter-groups)
+ [Failover process for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-failover)
+ [Creating a Multi\-AZ DB cluster snapshot](#USER_CreateMultiAZDBClusterSnapshot)
+ [Restoring from a snapshot to a Multi\-AZ DB cluster](#USER_RestoreFromMultiAZDBClusterSnapshot.Restoring)
+ [Restoring a Multi\-AZ DB cluster to a specified time](#USER_PIT.MultiAZDBCluster)
+ [Deleting a Multi\-AZ DB cluster](#USER_DeleteMultiAZDBCluster.Deleting)
+ [Multi\-AZ DB cluster metrics](#multi-az-db-cluster-metrics)
+ [Limitations for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts.Limitations)

**Important**  
Multi\-AZ DB clusters aren't the same as Aurora DB clusters\. For information about Aurora DB clusters, see the [Amazon Aurora User Guide](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html)\.

## Overview of Multi\-AZ DB clusters<a name="multi-az-db-clusters-concepts-overview"></a>

With a Multi\-AZ DB cluster, Amazon RDS replicates data from the writer DB instance to both of the reader DB instances using the DB engine's native replication capabilities\. When a change is made on the writer DB instance, it's sent to each reader DB instance\. Acknowledgment from at least one reader DB instance is required for a change to be committed and applied\. Reader DB instances act as automatic failover targets and also serve read traffic to increase application read throughput\.

The following diagram shows a Multi\-AZ DB cluster\.

![\[Multi-AZ DB cluster\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster.png)

Multi\-AZ DB clusters typically have lower write latency when compared to Multi\-AZ DB instance deployments\. The RDS console shows the Availability Zone of the writer DB instance and the Availability Zones of the reader DB instances\. You can also use the [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) CLI command or the [DescribeDBClusters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusters.html) API operation to find this information\. 

## Supported DB instance classes for Multi\-AZ DB clusters<a name="multi-az-db-clusters-concepts-db-instance-classes"></a>

Multi\-AZ DB clusters support the following DB instance classes\.


****  

| Instance class | vCPU | ECU | Memory \(GiB\) | VPC only | EBS optimized | Max\. bandwidth \(mbps\) | Network performance | 
| --- | --- | --- | --- | --- | --- | --- | --- | 
| db\.m6gd | 
| db\.m6gd\.16xlarge | 64 | – | 256 | Yes | Yes | 19,000 | 25 Gbps | 
| db\.m6gd\.12xlarge | 48 | – | 192 | Yes | Yes | 13,500 | 20 Gbps | 
| db\.m6gd\.8xlarge | 32 | – | 128 | Yes | Yes | 9,000 | 12 Gbps | 
| db\.m6gd\.4xlarge | 16 | – | 64 | Yes | Yes | 4,750 | Up to 10 Gbps | 
| db\.m6gd\.2xlarge | 8 | – | 32 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.m6gd\.xlarge | 4 | – | 16 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.m6gd\.large | 2 | – | 8 | Yes | Yes | Up to 4,750 | Up to 10 Gbps | 
| db\.r6gd | 
| db\.r6gd\.16xlarge | 64 | – | 512 | Yes | Yes | 19,000 | 25 Gbps | 
| db\.r6gd\.12xlarge | 48 | – | 384 | Yes | Yes | 13,500 | 20 Gbps | 
| db\.r6gd\.8xlarge | 32 | – | 256 | Yes | Yes | 9,000 | 12 Gbps | 
| db\.r6gd\.4xlarge | 16 | – | 128 | Yes | Yes | 4,750 | Up to 10 Gbps  | 
| db\.r6gd\.2xlarge | 8 | – | 64 | Yes | Yes | Up to 4,750 | Up to 10 Gbps  | 
| db\.r6gd\.xlarge | 4 | – | 32 | Yes | Yes | Up to 4,750 | Up to 10 Gbps  | 
| db\.r6gd\.large | 2 | – | 16 | Yes | Yes | Up to 4,750 | Up to 10 Gbps  | 

## Creating a Multi\-AZ DB cluster<a name="create-multi-az-db-cluster"></a>

A Multi\-AZ DB cluster has a writer DB instance and two reader DB instances in three separate Availability Zones\. Multi\-AZ DB clusters provide high availability, increased capacity for read workloads, and lower latency when compared to Multi\-AZ deployments\. For more information about Multi\-AZ DB clusters, see [Multi\-AZ DB cluster deployments \(preview\)](#multi-az-db-clusters-concepts)\.

**Note**  
Multi\-AZ DB clusters are supported only for the MySQL and PostgreSQL DB engines\.

In the following topic, you can find out how to create a Multi\-AZ DB cluster\. To get started, first see [DB cluster prerequisites](#create-multi-az-db-cluster-prerequisites)\.

For instructions on connecting to your Multi\-AZ DB cluster, see [Connecting to an Amazon RDS DB instance](CHAP_CommonTasks.Connect.md)\.

### DB cluster prerequisites<a name="create-multi-az-db-cluster-prerequisites"></a>

Before you create a Multi\-AZ DB cluster, make sure to complete the tasks in [Setting up for Amazon RDS](CHAP_SettingUp.md)\. The following are additional prerequisites to creating a Multi\-AZ DB cluster\.

#### VPC prerequisites<a name="create-multi-az-db-cluster-prerequisites.VPC"></a>

You can only create a Multi\-AZ DB cluster in a virtual private cloud \(VPC\) based on the Amazon VPC service, in an AWS Region that has at least three Availability Zones\. The DB subnet group that you choose for the DB cluster must cover at least three Availability Zones\. This configuration ensures that your DB cluster always has at least two DB instance available for failover, in the unlikely event of an Availability Zone failure\.

If you are using the AWS Management Console to create your Multi\-AZ DB cluster, you can have Amazon RDS automatically create a VPC for you\. Or you can use an existing VPC or create a new VPC for your Multi\-AZ DB cluster\. Your VPC must have at least one subnet in each of at least three Availability Zones for you to use it with a Multi\-AZ DB cluster\. For information on VPCs, see [Amazon Virtual Private Cloud VPCs and Amazon RDS](USER_VPC.md)\.

If you don't have a default VPC or you haven't created a VPC, and you don't plan to use the console, do the following:
+ Create a VPC with at least one subnet in each of at least three of the Availability Zones in the AWS Region where you want to deploy your DB cluster\. For more information, see [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#Overview.RDSVPC.Create)\.
+ Specify a VPC security group that authorizes connections to your DB cluster\. For more information, see [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#Overview.RDSVPC.Create)\.
+ Specify an RDS DB subnet group that defines at least three subnets in the VPC that can be used by the Multi\-AZ DB cluster\. For more information, see [Working with DB subnet groups](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Subnets)\.

#### Additional prerequisites<a name="create-multi-az-db-cluster-prerequisites.Additional"></a>

If you are connecting to AWS using AWS Identity and Access Management \(IAM\) credentials, your AWS account must have IAM policies that grant the permissions required to perform Amazon RDS operations\. For more information, see [Identity and access management in Amazon RDS](UsingWithRDS.IAM.md)\.

If you are using IAM to access the Amazon RDS console, first sign on to the AWS Management Console with your IAM user credentials\. Then go to the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

If you want to tailor the configuration parameters for your DB cluster, specify a DB cluster parameter group with the required parameter settings\. For information about creating or modifying a DB cluster parameter group, see [Working with parameter groups for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-parameter-groups)\.

Determine the TCP/IP port number to specify for your DB cluster\. The firewalls at some companies block connections to the default ports\. If your company firewall blocks the default port, choose another port for your DB cluster\. All DB instances in a DB cluster use the same port\.

### Creating a DB cluster<a name="create-multi-az-db-cluster-creating"></a>

You can create a Multi\-AZ DB cluster using the AWS Management Console, the AWS CLI, or the RDS API\.

#### Console<a name="create-multi-az-db-cluster-creating-console"></a>

You can create a Multi\-AZ DB cluster by choosing **Multi\-AZ DB cluster \- preview** in the **Availability and durability** section\.

**To create a Multi\-AZ DB cluster using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region in which you want to create the DB cluster\.

   For preview, only the US East \(N\. Virginia\), US West \(Oregon\), and Europe \(Ireland\) AWS Regions support Multi\-AZ DB clusters\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**\.

   To create a Multi\-AZ DB cluster, make sure that **Standard Create** is selected and **Easy Create** isn't\.

1. In **Engine type**, choose **MySQL** or **PostgreSQL**\.

1. For **Version**, choose the DB engine version\.

   You can create a Multi\-AZ DB cluster only with MySQL version 8\.0\.26 and PostgreSQL version 13\.4\.

1. In **Templates**, choose the appropriate template for your deployment\.

1. In **Availability and durability**, choose **Multi\-AZ DB cluster \- preview**\.  
![\[Multi-AZ DB cluster choice\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-create.png)

1. In **DB cluster identifier**, enter the identifier for your DB cluster\.

1. In **Master username**, enter your master user name, or keep the default setting\.

1. Enter your master password:

   1. In the **Settings** section, open **Credential Settings**\.

   1. If you want to specify a password, clear the **Auto generate a password** box if it is selected\.

   1. \(Optional\) Change the **Master username** value\.

   1. Enter the same password in **Master password** and **Confirm password**\.

1. In **DB instance class**, choose a DB instance class\.

1. For the remaining sections, specify your DB cluster settings\. For information about each setting, see [Settings for creating Multi\-AZ DB clusters](#create-multi-az-db-cluster-settings)\.

1. Choose **Create database**\. 

   If you chose to use an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the master user name and password for the DB cluster, choose **View credential details**\.  
![\[Master user credentials after automatic password generation.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-credentials.png)

   To connect to the DB cluster as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\.

1. For **Databases**, choose the name of the new DB cluster\.

On the RDS console, the details for the new DB cluster appear\. The DB cluster has a status of **Creating** until the DB cluster is created and ready for use\. When the state changes to **Available**, you can connect to the DB cluster\. Depending on the DB cluster class and storage allocated, it can take several minutes for the new DB cluster to be available\. 

#### AWS CLI<a name="create-multi-az-db-cluster-creating-cli"></a>

Before you create a Multi\-AZ DB cluster using the AWS CLI, make sure to fulfill the required prerequisites, such as creating a VPC and an RDS DB subnet group\. For more information, see [DB cluster prerequisites](#create-multi-az-db-cluster-prerequisites)\.

To create a Multi\-AZ DB cluster by using the AWS CLI, call the [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) command\. Specify the `--db-cluster-identifier`\. For the `--engine` option, specify either `mysql` or `postgres`\.

For information about each option, see [Settings for creating Multi\-AZ DB clusters](#create-multi-az-db-cluster-settings)\.

The `create-db-cluster` command creates the writer DB instance for your DB cluster, and two reader DB instances\. Each DB instance is in a different Availability Zone\.

For example, the following command creates a MySQL 8\.0 Multi\-AZ DB cluster named `mysql-multi-az-db-cluster`\.

**Example**  
For Linux, macOS, or Unix:  

```
 1. aws rds create-db-cluster \
 2.    --db-cluster-identifier mysql-multi-az-db-cluster \
 3.    --engine mysql \
 4.    --engine-version 8.0.26  \
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
 4.    --engine-version 8.0.26 ^
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

#### RDS API<a name="create-multi-az-db-cluster-creating-api"></a>

Before you can create a Multi\-AZ DB cluster using the RDS API, make sure to fulfill the required prerequisites, such as creating a VPC and an RDS DB subnet group\. For more information, see [DB cluster prerequisites](#create-multi-az-db-cluster-prerequisites)\.

To create a Multi\-AZ DB cluster by using the RDS API, call the [ CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) operation\. Specify the `DBClusterIdentifier`\. For the `Engine` parameter, specify either `mysql` or `postgresql`\.

For information about each option, see [Settings for creating Multi\-AZ DB clusters](#create-multi-az-db-cluster-settings)\.

The `CreateDBCluster` operation creates the writer DB instance for your DB cluster, and two reader DB instances\. Each DB instance is in a different Availability Zone\.

### Settings for creating Multi\-AZ DB clusters<a name="create-multi-az-db-cluster-settings"></a>

For details about settings that you choose when you create a Multi\-AZ DB cluster, see the following table\. For more information about the AWS CLI options, see [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html)\. For more information about the RDS API parameters, see [ CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html)\.


| Console setting | Setting description | CLI option and RDS API parameter | 
| --- | --- | --- | 
|  **Allocated storage**  |  The amount of storage to allocate for each DB instance in your DB cluster \(in gibibyte\)\. For more information, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.   |  **CLI option:** `--allocated-storage` **API parameter:**  `AllocatedStorage`  | 
| Auto minor version upgrade |  **Enable auto minor version upgrade** to have your DB cluster receive preferred minor DB engine version upgrades automatically when they become available\. Amazon RDS performs automatic minor version upgrades in the maintenance window\.  |  **CLI option:** `--auto-minor-version-upgrade` `--no-auto-minor-version-upgrade` **API parameter:** `AutoMinorVersionUpgrade`  | 
|  Backup retention period  |  The number of days that you want automatic backups of your DB cluster to be retained\. For a Multi\-AZ DB cluster, this value must be set to **1** or greater\. For more information, see [Working with backups](USER_WorkingWithAutomatedBackups.md)\.  |  **CLI option:** `--backup-retention-period` **API parameter:** `BackupRetentionPeriod`  | 
|  Backup window |  The time period during which Amazon RDS automatically takes a backup of your DB cluster\. Unless you have a specific time that you want to have your database backed up, use the default of **No preference**\. For more information, see [Working with backups](USER_WorkingWithAutomatedBackups.md)\.  |  **CLI option:** `--preferred-backup-window` **API parameter:** `PreferredBackupWindow`  | 
|  Database authentication  |  For Multi\-AZ DB clusters, only **Password authentication** is supported\.  |  None because password authentication is the default\.  | 
|  Database port  |  The port that you want to access the DB cluster through\. The default port is shown\. If you use a DB security group with your DB cluster, this port value must be the same one that you provided when creating the DB security group\. In the preview, the port can't be changed after the DB cluster is created\. The firewalls at some companies block connections to the default ports\. If your company firewall blocks the default port, enter another port for your DB cluster\.  |  **CLI option:** `--port` **RDS API parameter:** `Port`  | 
|  DB cluster identifier  |  The name for your DB cluster\. Name your DB clusters in the same way that you name your on\-premises servers\. Your DB cluster identifier can contain up to 63 alphanumeric characters, and must be unique for your account in the AWS Region you chose\.  |  **CLI option:** `--db-cluster-identifier` **RDS API parameter:** `DBClusterIdentifier`  | 
|  DB cluster instance class  |  The compute and memory capacity of each DB instance in the Multi\-AZ DB cluster, for example `db.r6gd.xlarge`\.  If possible, choose a DB instance class large enough that a typical query working set can be held in memory\. When working sets are held in memory the system can avoid writing to disk, which improves performance\. For more information, see [Supported DB instance classes for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-db-instance-classes)\.   |  **CLI option:** `--db-cluster-instance-class` **RDS API parameter:** `DBClusterInstanceClass`  | 
|  **DB cluster parameter group**  |  The DB cluster parameter group that you want associated with the DB cluster\.  For more information, see [Working with parameter groups for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-parameter-groups)\.   |  **CLI option:** `--db-cluster-parameter-group-name` **RDS API parameter:** `DBClusterParameterGroupName`  | 
|  DB engine version  |  The version of database engine that you want to use\.  |  **CLI option:** `--engine-version` **RDS API parameter:** `EngineVersion`  | 
|  DB parameter group  |  The DB instance parameter group that you want associated with the DB instances in the DB cluster\. For more information, see [Working with parameter groups for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-parameter-groups)\.  |  Not applicable\. Amazon RDS associates each DB instance with the appropriate default parameter group\.  | 
| Deletion protection |  **Enable deletion protection** to prevent your DB cluster from being deleted\. If you create a production DB cluster with the console, deletion protection is turned on by default\. For more information, see [Deleting a DB instance](USER_DeleteInstance.md)\.  |  **CLI option:** `--deletion-protection` `--no-deletion-protection` **RDS API parameter:** `DeletionProtection`  | 
|  Encryption  |  **Enable Encryption** to turn on encryption at rest for this DB cluster\. Encryption is turned on by default for Multi\-AZ DB clusters\. For more information, see [Encrypting Amazon RDS resources](Overview.Encryption.md)\.  |  **CLI options:** `--kms-key-id` `--storage-encrypted` `--no-storage-encrypted` **RDS API parameters:** `KmsKeyId` `StorageEncrypted`  | 
|  Enhanced Monitoring  |  **Enable enhanced monitoring** to turn on metrics gathering in real time for the operating system that your DB cluster runs on\. For more information, see [Monitoring the OS by using Enhanced Monitoring](USER_Monitoring.OS.md)\.  |  **CLI options:** `--monitoring-interval` `--monitoring-role-arn` **RDS API parameters:** `MonitoringInterval` `MonitoringRoleArn`  | 
|  Initial database name  |  The name for the database on your DB cluster\. If you don't provide a name, Amazon RDS doesn't create a database on the DB cluster for MySQL, but it does create a database on the DB cluster for PostgreSQL\. The name can't be a word reserved by the database engine, and has other constraints depending on the DB engine\. MySQL: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/multi-az-db-clusters-concepts.html) PostgreSQL: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/multi-az-db-clusters-concepts.html)  |  **CLI option:** `--database-name` **RDS API parameter:** `DatabaseName`  | 
|  Maintenance window  |  The 30\-minute window in which pending modifications to your DB cluster are applied\. If the time period doesn't matter, choose **No preference**\. For more information, see [The Amazon RDS maintenance window](USER_UpgradeDBInstance.Maintenance.md#Concepts.DBMaintenance)\.  |  **CLI option:** `--preferred-maintenance-window` **RDS API parameter:** `PreferredMaintenanceWindow`  | 
|  Master password  |  The password for your master user account\.  |  **CLI option:** `--master-user-password` **RDS API parameter:** `MasterUserPassword`  | 
|  Master username  |  The name that you use as the master user name to log on to your DB cluster with all database privileges\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/multi-az-db-clusters-concepts.html) For more information on privileges granted to the master user, see [Master user account privileges](UsingWithRDS.MasterAccounts.md)\.  |  **CLI option:** `--master-username` **RDS API parameter:** `MasterUsername`  | 
| Performance Insights |  **Enable Performance Insights** to monitor your DB cluster load so that you can analyze and troubleshoot your database performance\. Choose a retention period to determine how much rolling data history to keep\. The default of seven days is in the free tier\. Long\-term retention \(two years\) is priced per vCPU per month\. Choose a master key to use to protect the key used to encrypt this database volume\. Choose from the master keys in your account, or enter the key from a different account\. For more information, see [Monitoring DB load with Performance Insights on Amazon RDS](USER_PerfInsights.md)\.  |  **CLI options:** `--enable-performance-insights` `--no-enable-performance-insights` `--performance-insights-retention-period` `--performance-insights-kms-key-id` **RDS API parameters:** `EnablePerformanceInsights` `PerformanceInsightsRetentionPeriod` `PerformanceInsightsKMSKeyId`  | 
|  Provisioned IOPS  |  The amount of Provisioned IOPS \(input/output operations per second\) to be initially allocated for the DB cluster\. This setting is available only if Provisioned IOPS \(`io1`\) is selected as the storage type\. For more information, see [Provisioned IOPS SSD storage](CHAP_Storage.md#USER_PIOPS)\.   |  **CLI option:** `--iops` **RDS API parameter:** `Iops`  | 
|  Public access  |  **Publicly accessible** to give the DB cluster a public IP address, meaning that it's accessible outside the VPC\. To be publicly accessible, the DB cluster also has to be in a public subnet in the VPC\. **Not publicly accessible** to make the DB cluster accessible only from inside the VPC\. For more information, see [Hiding a DB instance in a VPC from the internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\. To connect to a DB cluster from outside of its VPC, the DB cluster must be publicly accessible\. Also, access must be granted using the inbound rules of the DB cluster's security group, and other requirements must be met\. For more information, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.  If your DB cluster isn't publicly accessible, you can use an AWS Site\-to\-Site VPN connection or an AWS Direct Connect connection to access it from a private network\. For more information, see [Internetwork traffic privacy](inter-network-traffic-privacy.md)\.  |  **CLI option:** `--publicly-accessible` `--no-publicly-accessible` **RDS API parameter:** `PubliclyAccessible`  | 
|  Storage type  |  The storage type for your DB cluster\.  For preview, only Provisioned IOPS \(io1\) storage is supported\. For more information, see [Amazon RDS storage types](CHAP_Storage.md#Concepts.Storage)\.  |  **CLI option:** `--storage-type` **RDS API parameter:** `StorageType`  | 
|  Subnet group  |  A DB subnet group to associate with this DB cluster\. For more information, see [Working with DB subnet groups](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Subnets)\.  |  **CLI option:** `--db-subnet-group-name` **RDS API parameter:** `DBSubnetGroupName`  | 
|  Virtual Private Cloud \(VPC\)  |  A Amazon VPC to associate with this DB cluster\. For more information, see [Amazon Virtual Private Cloud VPCs and Amazon RDS](USER_VPC.md)\.   |  For the CLI and API, you specify the VPC security group IDs\.  | 
|  VPC security groups  |  The security groups to associate with the DB cluster\. For more information, see [VPC security groups](Overview.RDSSecurityGroups.md#Overview.RDSSecurityGroups.VPCSec)\.   |  **CLI option:** `--vpc-security-group-ids` **RDS API parameter:** `VpcSecurityGroupIds`  | 

### Settings that don't apply when creating Multi\-AZ DB clusters<a name="create-multi-az-db-cluster-settings-not-applicable"></a>

The following settings in the AWS CLI command [ `create-db-cluster`](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and the RDS API operation [ `CreateDBCluster`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) don't apply to Multi\-AZ DB clusters in the preview\.

You also can't specify these settings for Multi\-AZ DB clusters in the console\.


| AWS CLI setting | RDS API setting | 
| --- | --- | 
|  `--availability-zones`  |  `AvailabilityZones`  | 
|  `--backtrack-window`  |  `BacktrackWindow`  | 
|  `--character-set-name`  |  `CharacterSetName`  | 
|  `--copy-tags-to-snapshot \| --no-copy-tags-to-snapshot`  |  `CopyTagsToSnapshot`  | 
|  `--domain`  |  `Domain`  | 
|  `--domain-iam-role-name`  |  `DomainIAMRoleName`  | 
|  `--enable-cloudwatch-logs-exports \| --no-enable-cloudwatch-logs-exports`  |  `EnableCloudwatchLogsExports`  | 
|  `--enable-global-write-forwarding \| --no-enable-global-write-forwarding`  |  `EnableGlobalWriteForwarding`  | 
|  `--enable-http-endpoint \| --no-enable-http-endpoint`  |  `EnableHttpEndpoint`  | 
|  `--enable-iam-database-authentication \| --no-enable-iam-database-authentication`  |  `EnableIAMDatabaseAuthentication`  | 
|  `--global-cluster-identifier`  |  `GlobalClusterIdentifier`  | 
|  `--option-group-name`  |  `OptionGroupName`  | 
|  `--pre-signed-url`  |  `PreSignedUrl`  | 
|  `--replication-source-identifier`  |  `ReplicationSourceIdentifier`  | 
|  `--scaling-configuration`  |  `ScalingConfiguration`  | 

## Managing connections for Multi\-AZ DB clusters<a name="multi-az-db-clusters-concepts-connection-management"></a>

 A Multi\-AZ DB cluster has three DB instances instead of a single DB instance\. Each connection is handled by a specific DB instance\. When you connect to a Multi\-AZ DB cluster, the host name and port that you specify point to an intermediate handler called an *endpoint*\. The Multi\-AZ DB cluster uses the endpoint mechanism to abstract these connections\. Thus, you don't have to hardcode all the host names or write your own logic for load\-balancing and rerouting connections when some DB instances aren't available\. 

 For certain tasks, different DB instances or groups of DB instances perform different roles\. For example, the writer DB instance handles all data definition language \(DDL\) and data manipulation language \(DML\) statements\. 

 Using endpoints, you can map each connection to the appropriate DB instance or group of DB instances based on your use case\. For example, to perform DDL and DML statements, you can connect to whichever DB instance is the writer DB instance\. To perform queries, you can connect to the reader endpoint, with the Multi\-AZ DB cluster automatically performing load\-balancing among the reader DB instances\. For diagnosis or tuning, you can connect to a specific DB instance endpoint to examine details about a specific DB instance\. 

For information about connecting to a DB instance, see [Connecting to an Amazon RDS DB instance](CHAP_CommonTasks.Connect.md)\.

**Topics**
+ [Types of Multi\-AZ DB cluster endpoints](#multi-az-db-clusters-concepts-connection-management-endpoint-types)
+ [Viewing the endpoints for a Multi\-AZ DB cluster](#multi-az-db-clusters-concepts-connection-management-viewing)
+ [Using the cluster endpoint](#multi-az-db-clusters-concepts-connection-management-endpoints-cluster)
+ [Using the reader endpoint](#multi-az-db-clusters-concepts-connection-management-endpoints-reader)
+ [Using the instance endpoints](#multi-az-db-clusters-concepts-connection-management-endpoints-instance)
+ [How Multi\-AZ DB endpoints work with high availability](#multi-az-db-clusters-concepts-connection-management-endpoints-ha)

### Types of Multi\-AZ DB cluster endpoints<a name="multi-az-db-clusters-concepts-connection-management-endpoint-types"></a>

 An endpoint is represented by a unique identifier that contains a host address\. The following types of endpoints are available from a Multi\-AZ DB cluster: 

**Cluster endpoint**  
 A *cluster endpoint* \(or *writer endpoint*\) for a Multi\-AZ DB cluster connects to the current writer DB instance for that DB cluster\. This endpoint is the only one that can perform write operations such as DDL and DML statements\. This endpoint can also perform read operations\.   
 Each Multi\-AZ DB cluster has one cluster endpoint and one writer DB instance\.   
 You use the cluster endpoint for all write operations on the DB cluster, including inserts, updates, deletes, and DDL changes\. You can also use the cluster endpoint for read operations, such as queries\.   
 The cluster endpoint provides failover support for read/write connections to the DB cluster\. If the current writer DB instance of a DB cluster fails, the Multi\-AZ DB cluster automatically fails over to a new writer DB instance\. During a failover, the DB cluster continues to serve connection requests to the cluster endpoint from the new writer DB instance, with minimal interruption of service\.   
 The following example illustrates a cluster endpoint for a Multi\-AZ DB cluster\.   
 `mydbcluster.cluster-123456789012.us-east-1.rds.amazonaws.com` 

**Reader endpoint**  
 A *reader endpoint* for a Multi\-AZ DB cluster provides load\-balancing support for read\-only connections to the DB cluster\. Use the reader endpoint for read operations, such as queries\. By processing those statements on the reader DB instances, this endpoint reduces the overhead on the writer DB instance\. It also helps the cluster to scale the capacity to handle simultaneous `SELECT` queries\. Each Multi\-AZ DB cluster has one reader endpoint\.   
 The reader endpoint load\-balances each connection request among the reader DB instances\. When you use the reader endpoint for a session, you can only perform read\-only statements such as `SELECT` in that session\.   
 The following example illustrates a reader endpoint for a Multi\-AZ DB cluster\.   
 `mydbcluster.cluster-ro-123456789012.us-east-1.rds.amazonaws.com` 

**Instance endpoint**  
 An *instance endpoint* connects to a specific DB instance within a Multi\-AZ DB cluster\. Each DB instance in a DB cluster has its own unique instance endpoint\. So there is one instance endpoint for the current writer DB instance of the DB cluster, and there is one instance endpoint for each of the reader DB instances in the DB cluster\.   
 The instance endpoint provides direct control over connections to the DB cluster\. This control can help you address scenarios where using the cluster endpoint or reader endpoint might not be appropriate\. For example, your client application might require more fine\-grained load balancing based on workload type\. In this case, you can configure multiple clients to connect to different reader DB instances in a DB cluster to distribute read workloads\.   
 The following example illustrates an instance endpoint for a DB instance in a Multi\-AZ DB cluster\.   
 `mydbinstance.123456789012.us-east-1.rds.amazonaws.com` 

### Viewing the endpoints for a Multi\-AZ DB cluster<a name="multi-az-db-clusters-concepts-connection-management-viewing"></a>

 In the AWS Management Console, you see the cluster endpoint and the reader endpoint on the details page for each Multi\-AZ DB cluster\. You see the instance endpoint in the details page for each DB instance\. 

 With the AWS CLI, you see the writer and reader endpoints in the output of the [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) command\. For example, the following command shows the endpoint attributes for all clusters in your current AWS Region\. 

```
aws rds describe-db-cluster-endpoints
```

 With the Amazon RDS API, you retrieve the endpoints by calling the [DescribeDBClusterEndpoints](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusterEndpoints.html) function\. The output also shows Amazon Aurora DB cluster endpoints, if any exist\.

### Using the cluster endpoint<a name="multi-az-db-clusters-concepts-connection-management-endpoints-cluster"></a>

Each Multi\-AZ DB cluster has a single built\-in cluster endpoint, whose name and other attributes are managed by Amazon RDS\. You can't create, delete, or modify this kind of endpoint\. 

 You use the cluster endpoint when you administer your DB cluster, perform extract, transform, load \(ETL\) operations, or develop and test applications\. The cluster endpoint connects to the writer DB instance of the cluster\. The writer DB instance is the only DB instance where you can create tables and indexes, run `INSERT` statements, and perform other DDL and DML operations\. 

 The physical IP address pointed to by the cluster endpoint changes when the failover mechanism promotes a new DB instance to be the writer DB instance for the cluster\. If you use any form of connection pooling or other multiplexing, be prepared to flush or reduce the time\-to\-live for any cached DNS information\. Doing so ensures that you don't try to establish a read/write connection to a DB instance that became unavailable or is now read\-only after a failover\. 

### Using the reader endpoint<a name="multi-az-db-clusters-concepts-connection-management-endpoints-reader"></a>

 You use the reader endpoint for read\-only connections to your Multi\-AZ DB cluster\. This endpoint uses a load\-balancing mechanism to help your cluster handle a query\-intensive workload\. The reader endpoint is the endpoint that you supply to applications that do reporting or other read\-only operations on the cluster\. 

 The reader endpoint load\-balances connections to available reader DB instances in a Multi\-AZ DB cluster\. It doesn't load\-balance individual queries\. If you want to load\-balance each query to distribute the read workload for a DB cluster, open a new connection to the reader endpoint for each query\. 

 Each Multi\-AZ cluster has a single built\-in reader endpoint, whose name and other attributes are managed by Amazon RDS\. You can't create, delete, or modify this kind of endpoint\. 

### Using the instance endpoints<a name="multi-az-db-clusters-concepts-connection-management-endpoints-instance"></a>

 Each DB instance in a Multi\-AZ DB cluster has its own built\-in instance endpoint, whose name and other attributes are managed by Amazon RDS\. You can't create, delete, or modify this kind of endpoint\. With a Multi\-AZ DB cluster, you typically use the writer and reader endpoints more often than the instance endpoints\. 

In day\-to\-day operations, the main way that you use instance endpoints is to diagnose capacity or performance issues that affect one specific DB instance in a Multi\-AZ DB cluster\. While connected to a specific DB instance, you can examine its status variables, metrics, and so on\. Doing this can help you determine what's happening for that DB instance that's different from what's happening for other DB instances in the cluster\. 

### How Multi\-AZ DB endpoints work with high availability<a name="multi-az-db-clusters-concepts-connection-management-endpoints-ha"></a>

For Multi\-AZ DB clusters where high availability is important, use the writer endpoint for read/write or general\-purpose connections and the reader endpoint for read\-only connections\. The writer and reader endpoints manage DB instance failover better than instance endpoints do\. Unlike the instance endpoints, the writer and reader endpoints automatically change which DB instance they connect to if a DB instance in your cluster becomes unavailable\. 

 If the writer DB instance of a DB cluster fails, Amazon RDS automatically fails over to a new writer DB instance\. It does so by promoting a reader DB instance to a new writer DB instance\. If a failover occurs, you can use the writer endpoint to reconnect to the newly promoted writer DB instance\. Or you can use the reader endpoint to reconnect to one of the reader DB instances in the DB cluster\. During a failover, the reader endpoint might direct connections to the new writer DB instance of a DB cluster for a short time after a reader DB instance is promoted to the new writer DB instance\. If you design your own application logic to manage instance endpoint connections, you can manually or programmatically discover the resulting set of available DB instances in the DB cluster\. 

## Managing a Multi\-AZ DB cluster with the AWS Management Console<a name="multi-az-db-clusters-concepts-managing-console"></a>

You can manage a Multi\-AZ DB cluster with the console\.

**To manage a Multi\-AZ DB cluster with the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the Multi\-AZ DB cluster that you want to manage\.

The following image shows a Multi\-AZ DB cluster in the console\.

![\[Multi-AZ DB cluster in the AWS Management Console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-view-console-postgresql.png)

The available actions in the **Actions** menu depend on whether the Multi\-AZ DB cluster is selected or a DB instance in the cluster is selected\.

Choose the Multi\-AZ DB cluster to view the cluster details and perform actions at the cluster level\.

![\[Multi-AZ DB cluster actions in the AWS Management Console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-actions-console-postgresql.png)

Choose a DB instance in a Multi\-AZ DB cluster to view the DB instance details and perform actions at the DB instance level\.

![\[DB instance actions in a Multi-AZ DB cluster in the AWS Management Console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-instance-actions-console-postgresql.png)

## Modifying a Multi\-AZ DB cluster<a name="modify-multi-az-db-cluster"></a>

A Multi\-AZ DB cluster has a writer DB instance and two reader DB instances in three separate Availability Zones\. Multi\-AZ DB clusters provide high availability, increased capacity for read workloads, and lower latency when compared to Multi\-AZ deployments\. For more information about Multi\-AZ DB clusters, see [Multi\-AZ DB cluster deployments \(preview\)](#multi-az-db-clusters-concepts)\.

You can modify a Multi\-AZ DB cluster to change its settings\. You can also perform operations on a Multi\-AZ DB cluster, such as taking a snapshot of it\. However, you can't modify the DB instances in a Multi\-AZ DB cluster, and the only supported operation is rebooting a DB instance\.

**Note**  
Multi\-AZ DB clusters are supported only for the MySQL and PostgreSQL DB engines\.

You can modify a Multi\-AZ DB cluster using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="modify-multi-az-db-cluster-console"></a>

**To modify a Multi\-AZ DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the Multi\-AZ DB cluster that you want to modify\. 

1. Choose **Modify**\. The **Modify DB cluster** page appears\.

1. Change any of the settings that you want\. For information about each setting, see [Settings for modifying Multi\-AZ DB clusters](#modify-multi-az-db-cluster-settings)\. 

1. When all the changes are as you want them, choose **Continue** and check the summary of modifications\. 

1. \(Optional\) Choose **Apply immediately** to apply the changes immediately\. Choosing this option can cause downtime in some cases\. For more information, see [Using the Apply Immediately setting](#modify-multi-az-db-cluster-apply-immediately)\. 

1. On the confirmation page, review your changes\. If they're correct, choose **Modify DB cluster** to save your changes\. 

   Or choose **Back** to edit your changes or **Cancel** to cancel your changes\. 

### AWS CLI<a name="modify-multi-az-db-cluster-cli"></a>

To modify a Multi\-AZ DB cluster by using the AWS CLI, call the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) command\. Specify the DB cluster identifier and the values for the options that you want to modify\. For information about each option, see [Settings for modifying Multi\-AZ DB clusters](#modify-multi-az-db-cluster-settings)\. 

**Example**  
The following code modifies `my-multi-az-dbcluster` by setting the backup retention period to 1 week \(7 days\)\. The code turns on deletion protection by using `--deletion-protection`\. To turn off deletion protection, use `--no-deletion-protection`\. The changes are applied during the next maintenance window by using `--no-apply-immediately`\. Use `--apply-immediately` to apply the changes immediately\. For more information, see [Using the Apply Immediately setting](#modify-multi-az-db-cluster-apply-immediately)\.   
For Linux, macOS, or Unix:  

```
aws rds modify-db-cluster \
    --db-instance-identifier my-multi-az-dbcluster \
    --backup-retention-period 7 \
    --deletion-protection \
    --no-apply-immediately
```
For Windows:  

```
aws rds modify-db-cluster ^
    --db-instance-identifier my-multi-az-dbcluster ^
    --backup-retention-period 7 ^
    --deletion-protection ^
    --no-apply-immediately
```

### RDS API<a name="modify-multi-az-db-cluster-api"></a>

To modify a Multi\-AZ DB cluster by using the Amazon RDS API, call the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) operation\. Specify the DB cluster identifier, and the parameters for the settings that you want to modify\. For information about each parameter, see [Settings for modifying Multi\-AZ DB clusters](#modify-multi-az-db-cluster-settings)\. 

### Using the Apply Immediately setting<a name="modify-multi-az-db-cluster-apply-immediately"></a>

When you modify a Multi\-AZ DB cluster, you can apply the changes immediately\. To apply changes immediately, you choose the **Apply Immediately** option in the AWS Management Console\. Or you use the `--apply-immediately` option when calling the AWS CLI or set the `ApplyImmediately` parameter to `true` when using the Amazon RDS API\. 

If you don't choose to apply changes immediately, the changes are put into the pending modifications queue\. During the next maintenance window, any pending changes in the queue are applied\. If you choose to apply changes immediately, your new changes and any changes in the pending modifications queue are applied\. 

**Important**  
If any of the pending modifications require the DB cluster to be temporarily unavailable \(*downtime*\), choosing the apply immediately option can cause unexpected downtime\.  
When you choose to apply a change immediately, any pending modifications are also applied immediately, instead of during the next maintenance window\.   
If you don't want a pending change to be applied in the next maintenance window, you can modify the DB instance to revert the change\. You can do this by using the AWS CLI and specifying the `--apply-immediately` option\.

Changes to some database settings are applied immediately, even if you choose to defer your changes\. To see how the different database settings interact with the apply immediately setting, see [Settings for modifying Multi\-AZ DB clusters](#modify-multi-az-db-cluster-settings)\.

### Settings for modifying Multi\-AZ DB clusters<a name="modify-multi-az-db-cluster-settings"></a>

For details about settings that you can use to modify a Multi\-AZ DB cluster, see the following table\. For more information about the AWS CLI options, see [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html)\. For more information about the RDS API parameters, see [ ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html)\.


| Console setting | Setting description | CLI option and RDS API parameter | When the change occurs | Downtime notes | 
| --- | --- | --- | --- | --- | 
|  **Allocated storage**  |  The amount of storage to allocate for each DB instance in your DB cluster \(in gibibytes\)\. For more information, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.   |  **CLI option:** `--allocated-storage` **API parameter:**  `AllocatedStorage`  |  If you choose to apply the change immediately, it occurs immediately\. If you don't choose to apply the change immediately, it occurs during the next maintenance window\.  |  Downtime doesn't occur during this change\.  | 
| Auto minor version upgrade |  **Enable auto minor version upgrade** to have your DB cluster receive preferred minor DB engine version upgrades automatically when they become available\. Amazon RDS performs automatic minor version upgrades in the maintenance window\.  |  **CLI option:** `--auto-minor-version-upgrade` `--no-auto-minor-version-upgrade` **API parameter:** `AutoMinorVersionUpgrade`  |  The change occurs immediately\. This setting ignores the apply immediately setting\.   |  Downtime doesn't occur during this change\.  | 
| Backup retention period  |  The number of days that you want automatic backups of your DB cluster to be retained\. For any nontrivial DB cluster, set this value to **1** or greater\. For more information, see [Working with backups](USER_WorkingWithAutomatedBackups.md)\.  |  **CLI option:** `--backup-retention-period` **API parameter:** `BackupRetentionPeriod`  |  If you choose to apply the change immediately, it occurs immediately\.  If you don't choose to apply the change immediately, and you change the setting from a nonzero value to another nonzero value, the change is applied asynchronously, as soon as possible\. Otherwise, the change occurs during the next maintenance window\.   |  Downtime occurs if you change from 0 to a nonzero value, or from a nonzero value to 0\.  | 
|  Backup window |  The time period during which Amazon RDS automatically takes a backup of your DB cluster\. Unless you have a specific time that you want to have your database backed up, use the default of **No preference**\. For more information, see [Working with backups](USER_WorkingWithAutomatedBackups.md)\.  |  **CLI option:** `--preferred-backup-window` **API parameter:** `PreferredBackupWindow`  |  The change is applied asynchronously, as soon as possible\.   |  Downtime doesn't occur during this change\.  | 
|  Database authentication  |  For Multi\-AZ DB clusters, only **Password authentication** is supported\.  |  None because password authentication is the default\.  |  If you choose to apply the change immediately, it occurs immediately\. If you don't choose to apply the change immediately, it occurs during the next maintenance window\.  |  Downtime doesn't occur during this change\.  | 
|  DB cluster instance class  |  The compute and memory capacity of each DB instance in the Multi\-AZ DB cluster, for example `db.r6gd.xlarge`\.  If possible, choose a DB instance class large enough that a typical query working set can be held in memory\. When working sets are held in memory, the system can avoid writing to disk, which improves performance\. For more information, see [Supported DB instance classes for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-db-instance-classes)\.  |  **CLI option:** `--db-cluster-instance-class` **RDS API parameter:** `DBClusterInstanceClass`  |  If you choose to apply the change immediately, it occurs immediately\. If you don't choose to apply the change immediately, it occurs during the next maintenance window\.  |  Downtime occurs during this change\.  | 
|  **DB cluster parameter group**  |  The DB cluster parameter group that you want associated with the DB cluster\.  For more information, see [Working with parameter groups for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-parameter-groups)\.   |  **CLI option:** `--db-cluster-parameter-group-name` **RDS API parameter:** `DBClusterParameterGroupName`  |  The parameter group change occurs immediately\.   |  An outage doesn't occur during this change\. When you change the parameter group, changes to some parameters are applied to the DB instances in the Multi\-AZ DB cluster immediately without a reboot\. Changes to other parameters are applied only after the DB instances are rebooted\.  | 
|  DB engine version  |  The version of database engine that you want to use\.  |  **CLI option:** `--engine-version` **RDS API parameter:** `EngineVersion`  |  If you choose to apply the change immediately, it occurs immediately\. If you don't choose to apply the change immediately, it occurs during the next maintenance window\.  |  An outage occurs during this change\.  | 
| Deletion protection |  **Enable deletion protection** to prevent your DB cluster from being deleted\. For more information, see [Deleting a DB instance](USER_DeleteInstance.md)\.  |  **CLI option:** `--deletion-protection` `--no-deletion-protection` **RDS API parameter:** `DeletionProtection`  |  The change occurs immediately\. This setting ignores the apply immediately setting\.   |  An outage doesn't occur during this change\.  | 
|  Maintenance window  |  The 30\-minute window in which pending modifications to your DB cluster are applied\. If the time period doesn't matter, choose **No preference**\. For more information, see [The Amazon RDS maintenance window](USER_UpgradeDBInstance.Maintenance.md#Concepts.DBMaintenance)\.  |  **CLI option:** `--preferred-maintenance-window` **RDS API parameter:** `PreferredMaintenanceWindow`  |  The change occurs immediately\. This setting ignores the apply immediately setting\.   |  If there are one or more pending actions that cause downtime, and the maintenance window is changed to include the current time, those pending actions are applied immediately and downtime occurs\.  | 
|  New master password  |  The password for your master user account\.  |  **CLI option:** `--master-user-password` **RDS API parameter:** `MasterUserPassword`  |  The change is applied asynchronously, as soon as possible\. This setting ignores the apply immediately setting\.   |  Downtime doesn't occur during this change\.  | 
|  Provisioned IOPS  |  The amount of Provisioned IOPS \(input/output operations per second\) to be initially allocated for the DB cluster\. This setting is available only if Provisioned IOPS \(`io1`\) is selected as the storage type\. For more information, see [Provisioned IOPS SSD storage](CHAP_Storage.md#USER_PIOPS)\.   |  **CLI option:** `--iops` **RDS API parameter:** `Iops`  |  If you choose to apply the change immediately, it occurs immediately\. If you don't choose to apply the change immediately, it occurs during the next maintenance window\.  |  Downtime doesn't occur during this change\.  | 
|  Public access  |  **Publicly accessible** to give the DB cluster a public IP address, meaning that it's accessible outside its virtual private cloud \(VPC\)\. To be publicly accessible, the DB cluster also has to be in a public subnet in the VPC\. **Not publicly accessible** to make the DB cluster accessible only from inside the VPC\. For more information, see [Hiding a DB instance in a VPC from the internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\. To connect to a DB cluster from outside of its VPC, the DB cluster must be publicly accessible\. Also, access must be granted using the inbound rules of the DB cluster's security group, and other requirements must be met\. For more information, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.   If your DB cluster isn't publicly accessible, you can use an AWS Site\-to\-Site VPN connection or an AWS Direct Connect connection to access it from a private network\. For more information, see [Internetwork traffic privacy](inter-network-traffic-privacy.md)\.  |  **CLI option:** `--publicly-accessible` `--no-publicly-accessible` **RDS API parameter:** `PubliclyAccessible`  |  The change occurs immediately\. This setting ignores the apply immediately setting\.  |  An outage doesn't occur during this change\.  | 
|  Storage type  |  The storage type for your DB cluster\.  For preview, only Provisioned IOPS \(io1\) storage is supported\. For more information, see [Amazon RDS storage types](CHAP_Storage.md#Concepts.Storage)\.   |  **CLI option:** `--storage-type` **RDS API parameter:** `StorageType`  |  For the preview, you can't change the storage type\.  |  For the preview, you can't change the storage type\.  | 
|  VPC security group  |  The security groups to associate with the DB cluster\. For more information, see [VPC security groups](Overview.RDSSecurityGroups.md#Overview.RDSSecurityGroups.VPCSec)\.  |  **CLI option:** `--vpc-security-group-ids` **RDS API parameter:** `VpcSecurityGroupIds`  |  The change is applied asynchronously, as soon as possible\. This setting ignores the apply immediately setting\.   |  An outage doesn't occur during this change\.  | 

### Settings that don't apply when modifying Multi\-AZ DB clusters<a name="modify-multi-az-db-cluster-settings-not-applicable"></a>

The following settings in the AWS CLI command [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) and the RDS API operation [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) don't apply to Multi\-AZ DB clusters in the preview\.

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
|  `--new-db-cluster-identifier`  |  `NewDBClusterIdentifier`  | 
|  `--option-group-name`  |  `OptionGroupName`  | 
|  `--port`  |  `Port`  | 
|  `--scaling-configuration`  |  `ScalingConfiguration`  | 

## Rebooting Multi\-AZ DB clusters and reader DB instances<a name="multi-az-db-clusters-concepts-rebooting"></a>

You might need to reboot your Multi\-AZ DB cluster, usually for maintenance reasons\. For example, if you make certain modifications, or if you change the DB cluster parameter group associated with the DB cluster, you reboot the DB cluster for the changes to take effect\. 

If a DB cluster isn't using the latest changes to its associated DB cluster parameter group, the AWS Management Console shows the DB cluster parameter group with a status of **pending\-reboot**\. The **pending\-reboot** parameter groups status doesn't result in an automatic reboot during the next maintenance window\. To apply the latest parameter changes to that DB cluster, manually reboot the DB cluster\. For more information about parameter groups, see [Working with parameter groups for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-parameter-groups)\.

Rebooting a DB cluster restarts the database engine service\. Rebooting a DB cluster results in a momentary outage, during which the DB cluster status is set to **rebooting**\.

You can't reboot your DB cluster if it isn't in the **Available** state\. Your database can be unavailable for several reasons, such as an in\-progress backup, a previously requested modification, or a maintenance\-window action\.

**Important**  
When you reboot the writer instance of a Multi\-AZ DB cluster, it doesn't affect the reader DB instances in that DB cluster, and no failover occurs\. When you reboot a reader DB instance, no failover occurs\. To fail over a Multi\-AZ DB cluster, choose **Failover** in the console, call the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/failover-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/failover-db-cluster.html), or call the API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_FailoverDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_FailoverDBCluster.html)\.

The time required to reboot your DB cluster depends on the crash recovery process, database activity at the time of reboot, and the behavior of your specific DB cluster\. To improve the reboot time, we recommend that you reduce database activity as much as possible during the reboot process\. Reducing database activity reduces rollback activity for in\-transit transactions\. 

Multi\-AZ DB clusters don't support reboot with a failover\.

### Console<a name="USER_RebootMultiAZDBCluster.Console"></a>

**To reboot a DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the Multi\-AZ DB cluster that you want to reboot\. 

1. For **Actions**, choose **Reboot**\. 

   The **Reboot DB cluster** page appears\.

1. Choose **Reboot** to reboot your DB cluster\. 

   Or choose **Cancel**\. 

### AWS CLI<a name="USER_RebootMultiAZDBCluster.CLI"></a>

To reboot a Multi\-AZ DB cluster by using the AWS CLI, call the [reboot\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/reboot-db-cluster.html) command\. 

```
aws rds reboot-db-cluster --db-cluster-identifier mymultiazdbcluster
```

### RDS API<a name="USER_RebootMultiAZDBCluster.API"></a>

To reboot a Multi\-AZ DB cluster by using the Amazon RDS API, call the [RebootDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RebootDBCluster.html) operation\. 

## Working with parameter groups for Multi\-AZ DB clusters<a name="multi-az-db-clusters-concepts-parameter-groups"></a>

In a Multi\-AZ DB cluster, a *DB cluster parameter group* acts as a container for engine configuration values that are applied to every DB instance in the Multi\-AZ DB cluster\. The DB cluster parameter group also includes default values for all the instance\-level parameters\.

In a Multi\-AZ DB cluster, a *DB parameter group* is set to the default DB parameter group for the DB engine and DB engine version\. The settings in the DB cluster parameter group are used for all of the DB instances in the cluster\.

For information about parameter groups, see [Working with DB parameter groups](USER_WorkingWithParamGroups.md)\.

## Failover process for Multi\-AZ DB clusters<a name="multi-az-db-clusters-concepts-failover"></a>

If a planned or unplanned outage of your writer DB instance in a Multi\-AZ DB cluster results from an infrastructure defect, Amazon RDS automatically switches to a reader DB instance in another Availability Zone\. The time it takes for the failover to complete depends on the database activity and other conditions when the writer DB instance became unavailable\. Failover times are typically 20–40 seconds\. Failover completes when both reader DB instances have applied outstanding transactions from the failed writer\. When the failover is complete, it can take additional time for the RDS console to reflect the new Availability Zone\.

**Topics**
+ [Automatic failovers](#multi-az-db-clusters-concepts-failover-automatic)
+ [Manually failing over a Multi\-AZ DB cluster](#multi-az-db-clusters-concepts-failover-manual)
+ [Determining whether a Multi\-AZ DB cluster has failed over](#multi-az-db-clusters-concepts-failover-determining)
+ [Setting the JVM TTL for DNS name lookups](#multi-az-db-clusters-concepts-failover-java-dns)

### Automatic failovers<a name="multi-az-db-clusters-concepts-failover-automatic"></a>

Amazon RDS handles failovers automatically so you can resume database operations as quickly as possible without administrative intervention\. To fail over, the writer DB instance switches automatically to a reader DB instance\.

### Manually failing over a Multi\-AZ DB cluster<a name="multi-az-db-clusters-concepts-failover-manual"></a>

You can fail over a Multi\-AZ DB cluster manually using the AWS Management Console, the AWS CLI, or the RDS API\.

#### Console<a name="multi-az-db-clusters-concepts-failover-manual-con"></a>

**To fail over a Multi\-AZ DB cluster manually**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Multi\-AZ DB cluster that you want to fail over\.

1. For **Actions**, choose **Failover**\.

   The **Failover DB Cluster** page appears\.

1. Choose **Failover** to confirm the manual failover\.

#### AWS CLI<a name="multi-az-db-clusters-concepts-failover-manual-cli"></a>

To fail over a Multi\-AZ DB cluster manually, use the AWS CLI command [ failover\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/failover-db-cluster.html)\.

**Example**  

```
1. aws rds failover-db-cluster --db-cluster-identifier mymultiazdbcluster
```

#### RDS API<a name="multi-az-db-clusters-concepts-failover-manual-api"></a>

To fail over a Multi\-AZ DB cluster manually, call the Amazon RDS API [FailoverDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_FailoverDBCluster.html) and specify the `DBClusterIdentifier`\.

### Determining whether a Multi\-AZ DB cluster has failed over<a name="multi-az-db-clusters-concepts-failover-determining"></a>

To determine if your Multi\-AZ DB cluster has failed over, you can do the following:
+ Set up DB event subscriptions to notify you by email or SMS that a failover has been initiated\. For more information about events, see [Using Amazon RDS event notification](USER_Events.md)\.
+ View your DB events by using the Amazon RDS console or API operations\.
+ View the current state of your Multi\-AZ DB cluster by using the Amazon RDS console, the AWS CLI, and the RDS API\.

For information on how you can respond to failovers, reduce recovery time, and other best practices for Amazon RDS, see [Best practices for Amazon RDS](CHAP_BestPractices.md)\.

### Setting the JVM TTL for DNS name lookups<a name="multi-az-db-clusters-concepts-failover-java-dns"></a>

The failover mechanism automatically changes the Domain Name System \(DNS\) record of the DB instance to point to the reader DB instance\. As a result, you need to re\-establish any existing connections to your DB instance\. In a Java virtual machine \(JVM\) environment, due to how the Java DNS caching mechanism works, you might need to reconfigure JVM settings\.

The JVM caches DNS name lookups\. When the JVM resolves a host name to an IP address, it caches the IP address for a specified period of time, known as the *time\-to\-live* \(TTL\)\.

Because AWS resources use DNS name entries that occasionally change, we recommend that you configure your JVM with a TTL value of no more than 60 seconds\. Doing this makes sure that when a resource's IP address changes, your application can receive and use the resource's new IP address by requerying the DNS\.

On some Java configurations, the JVM default TTL is set so that it never refreshes DNS entries until the JVM is restarted\. Thus, if the IP address for an AWS resource changes while your application is still running, it can't use that resource until you manually restart the JVM and the cached IP information is refreshed\. In this case, it's crucial to set the JVM's TTL so that it periodically refreshes its cached IP information\.

**Note**  
The default TTL can vary according to the version of your JVM and whether a security manager is installed\. Many JVMs provide a default TTL less than 60 seconds\. If you're using such a JVM and not using a security manager, you can ignore the rest of this topic\. For more information on security managers in Oracle, see [The security manager](https://docs.oracle.com/javase/tutorial/essential/environment/security.html) in the Oracle documentation\.

To modify the JVM's TTL, set the [ `networkaddress.cache.ttl`](https://docs.oracle.com/javase/7/docs/technotes/guides/net/properties.html) property value\. Use one of the following methods, depending on your needs:
+ To set the property value globally for all applications that use the JVM, set `networkaddress.cache.ttl` in the `$JAVA_HOME/jre/lib/security/java.security` file\.

  ```
  networkaddress.cache.ttl=60					
  ```
+ To set the property locally for your application only, set `networkaddress.cache.ttl` in your application's initialization code before any network connections are established\.

  ```
  java.security.Security.setProperty("networkaddress.cache.ttl" , "60");					
  ```

## Creating a Multi\-AZ DB cluster snapshot<a name="USER_CreateMultiAZDBClusterSnapshot"></a>

When you create a Multi\-AZ DB cluster snapshot, make sure to identify which Multi\-AZ DB cluster you are going to back up, and then give your DB cluster snapshot a name so you can restore from it later\.

You can create a Multi\-AZ DB cluster snapshot using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="USER_CreateMultiAZDBClusterSnapshot.CON"></a>

**To create a DB cluster snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. In the list, choose the Multi\-AZ DB cluster for which you want to take a snapshot\.

1. For **Actions**, choose **Take snapshot**\.

   The **Take DB snapshot** window appears\.

1. For **Snapshot name**, enter the name of the snapshot\.

1. Choose **Take snapshot**\.

The **Snapshots** page appears, with the new Multi\-AZ DB cluster snapshot's status shown as `Creating`\. After its status is `Available`, you can see its creation time\.

### AWS CLI<a name="USER_CreateMultiAZDBClusterSnapshot.CLI"></a>

You can create a Multi\-AZ DB cluster snapshot by using the AWS CLI [ create\-db\-cluster\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster-snapshot.html) command with the following options:
+ `--db-cluster-identifier`
+ `--db-cluster-snapshot-identifier`

In this example, you create a Multi\-AZ DB cluster snapshot called *mymultiazdbclustersnapshot* for a DB cluster called *mymultiazdbcluster*\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds create-db-cluster-snapshot \
2.     --db-cluster-identifier mymultiazdbcluster \
3.     --db-cluster-snapshot-identifier mymultiazdbclustersnapshot
```
For Windows:  

```
1. aws rds create-db-cluster-snapshot ^
2.     --db-cluster-identifier mymultiazdbcluster ^
3.     --db-cluster snapshot-identifier mymultiazdbclustersnapshot
```

### RDS API<a name="USER_CreateMultiAZDBClusterSnapshot.API"></a>

You can create a Multi\-AZ DB cluster snapshot by using the Amazon RDS API [CreateDBClusterSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBClusterSnapshot.html) operation with the following parameters:
+ `DBClusterIdentifier`
+ `DBClusterSnapshotIdentifier`

## Restoring from a snapshot to a Multi\-AZ DB cluster<a name="USER_RestoreFromMultiAZDBClusterSnapshot.Restoring"></a>

You can restore a snapshot to a Multi\-AZ DB cluster using the AWS Management Console, the AWS CLI, or the RDS API\. You can restore each of these types of snapshots to a Multi\-AZ DB cluster:
+ A snapshot of a single\-AZ deployment
+ A snapshot of a Multi\-AZ DB instance deployment with a single DB instance
+ A snapshot of a Multi\-AZ DB cluster

**Tip**  
You can migrate a single\-AZ deployment or a Multi\-AZ DB instance deployment to a Multi\-AZ DB cluster deployment by restoring a snapshot\.

### Console<a name="USER_RestoreFromMultiAZDBClusterSnapshot.CON"></a>

**To restore a snapshot to a Multi\-AZ DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the snapshot that you want to restore from\.

1. For **Actions**, choose **Restore snapshot**\.

1. On the **Restore snapshot** page, in **Availability and durability**, choose **Multi\-AZ DB cluster \- preview**\.  
![\[Multi-AZ DB cluster choice\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-create.png)

1. In **DB instance class**, choose a DB instance class\.

   For more information, see [Supported DB instance classes for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-db-instance-classes)\.

1. For **DB cluster identifier**, enter the name for your restored Multi\-AZ DB cluster\.

1. For the remaining sections, specify your DB cluster settings\. For information about each setting, see [Settings for creating Multi\-AZ DB clusters](#create-multi-az-db-cluster-settings)\.

1. Choose **Restore DB instance**\. 

### AWS CLI<a name="USER_RestoreFromMultiAZDBClusterSnapshot.CLI"></a>

To restore a snapshot to a Multi\-AZ DB cluster, use the AWS CLI command [restore\-db\-cluster\-from\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-snapshot.html)\.

In this example, you restore from a previously created snapshot named `mysnapshot`\. You restore to a new Multi\-AZ DB cluster named `mynewmultiazdbcluster`\. You also specify the DB instance class used by the DB instances in the Multi\-AZ DB cluster\. Specify either `mysql` or `postgres` for the DB engine\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds restore-db-cluster-from-snapshot \
2.     --db-cluster-identifier mynewmultiazdbcluster \
3.     --snapshot-identifier mysnapshot \
4.     --engine mysql|postgres \
5.     --db-cluster-instance-class db.r6gd.xlarge
```
For Windows:  

```
1. aws rds restore-db-cluster-from-snapshot ^
2.     --db-cluster-identifier mynewmultiazdbcluster ^
3.     --snapshot-identifier mysnapshot ^
4.     --engine mysql|postgres ^
5.     --db-cluster-instance-class db.r6gd.xlarge
```

After the DB cluster has been restored, make sure to add the Multi\-AZ DB cluster to the security group used by the DB cluster or DB instance used to create the snapshot\. Doing so provides the same functionality as that of the previous DB cluster or DB instance\.

### RDS API<a name="USER_RestoreFromMultiAZDBClusterSnapshot.API"></a>

To restore a snapshot to a Multi\-AZ DB cluster, call the RDS API operation [RestoreDBClusterFromSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromSnapshot.html) with the following parameters: 
+ `DBClusterIdentifier` 
+ `SnapshotIdentifier` 
+ `Engine` 

You can also specify other optional parameters\.

After the DB cluster has been restored, make sure to add the Multi\-AZ DB cluster to the security group used by the DB cluster or DB instance used to create the snapshot\. Doing so provides the same functionality as that of the previous DB cluster or DB instance\.

## Restoring a Multi\-AZ DB cluster to a specified time<a name="USER_PIT.MultiAZDBCluster"></a>

When you restore a Multi\-AZ DB cluster to a point in time, you can choose the default VPC security group or apply a custom VPC security group to your Multi\-AZ DB cluster\.

Restored Multi\-AZ DB clusters are automatically associated with the default DB cluster parameter group\. However, you can apply a customer DB cluster parameter group by specifying it during a restore\.

RDS uploads transaction logs for Multi\-AZ DB clusters to Amazon S3 continuously\. To see the latest restorable time for a Multi\-AZ DB cluster, use the AWS CLI [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) command\. Look at the value returned in the `LatestRestorableTime` field for the DB cluster\.

You can restore to any point in time within your backup retention period\. To see the earliest restorable time for a Multi\-AZ DB cluster, use the AWS CLI [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) command\. Look at the value returned in the `EarliestRestorableTime` field for the DB cluster\.

**Note**  
We recommend that you restore to the same or similar Multi\-AZ DB cluster size—and IOPS if using Provisioned IOPS storage—as the source DB cluster\. You might get an error if, for example, you choose a DB cluster size with an incompatible IOPS value\.

You can restore a Multi\-AZ DB cluster to a point in time using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="USER_PIT.MultiAZDBCluster.CON"></a>

**To restore a Multi\-AZ DB cluster to a specified time**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Multi\-AZ DB cluster that you want to restore\.

1. For **Actions**, choose **Restore to point in time**\.

   The **Restore to point in time** window appears\.

1. Choose **Latest restorable time** to restore to the latest possible time, or choose **Custom** to choose a time\.

   If you chose **Custom**, enter the date and time to which you want to restore the Multi\-AZ DB cluster\.
**Note**  
Times are shown in your local time zone, which is indicated by an offset from Coordinated Universal Time \(UTC\)\. For example, UTC\-5 is Eastern Standard Time/Central Daylight Time\.

1. For **DB cluster identifier**, enter the name for your restored Multi\-AZ DB cluster\.

1. In **Availability and durability**, choose **Multi\-AZ DB cluster \- preview**\.  
![\[Multi-AZ DB cluster choice\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-create.png)

1. In **DB instance class**, choose a DB instance class\.

   For more information, see [Supported DB instance classes for Multi\-AZ DB clusters](#multi-az-db-clusters-concepts-db-instance-classes)\.

1. For the remaining sections, specify your DB cluster settings\. For information about each setting, see [Settings for creating Multi\-AZ DB clusters](#create-multi-az-db-cluster-settings)\.

1. Choose **Restore to point in time**\.

### AWS CLI<a name="USER_PIT.MultiAZDBCluster.CLI"></a>

To restore a Multi\-AZ DB cluster to a specified time, use the AWS CLI command [ restore\-db\-cluster\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html) to create a new Multi\-AZ DB cluster\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds restore-db-cluster-to-point-in-time \
2.     --source-db-cluster-identifier mysourcemultiazdbcluster \
3.     --db-cluster-identifier mytargetmultiazdbcluster \
4.     --restore-to-time 2021-08-14T23:45:00.000Z \
5.     --db-cluster-instance-class db.r6gd.xlarge
```
For Windows:  

```
1. aws rds restore-db-cluster-to-point-in-time ^
2.     --source-db-cluster-identifier mysourcemultiazdbcluster ^
3.     --db-cluster-identifier mytargetmultiazdbcluster ^
4.     --restore-to-time 2021-08-14T23:45:00.000Z ^
5.     --db-cluster-instance-class db.r6gd.xlarge
```

### RDS API<a name="USER_PIT.MultiAZDBCluster.API"></a>

To restore a DB cluster to a specified time, call the Amazon RDS API [RestoreDBClusterToPointInTime](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html) operation with the following parameters:
+ `SourceDBClusterIdentifier`
+ `DBClusterIdentifier`
+ `RestoreToTime`

## Deleting a Multi\-AZ DB cluster<a name="USER_DeleteMultiAZDBCluster.Deleting"></a>

You can delete a DB Multi\-AZ DB cluster using the AWS Management Console, the AWS CLI, or the RDS API\.

The time required to delete a Multi\-AZ DB cluster can vary depending on the backup retention period \(that is, how many backups to delete\), how much data is deleted, and whether a final snapshot is taken\.

You can't delete a Multi\-AZ DB cluster when deletion protection is turned on for it\. For more information, see [Deletion protection](USER_DeleteInstance.md#USER_DeleteInstance.DeletionProtection)\. You can turn off deletion protection by modifying the Multi\-AZ DB cluster\. For more information, see [Modifying a Multi\-AZ DB cluster](#modify-multi-az-db-cluster)\.

### Console<a name="USER_DeleteMultiAZDBCluster.Deleting.CON"></a>

**To delete a Multi\-AZ DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the Multi\-AZ DB cluster that you want to delete\.

1. For **Actions**, choose **Delete**\.

1. Choose **Create final snapshot?** to create a final DB snapshot for the Multi\-AZ DB cluster\. 

   If you create a final snapshot, enter a name for **Final snapshot name**\.

1. Choose **Retain automated backups** to retain automated backups\.

1. Enter **delete me** in the box\.

1. Choose **Delete**\.

### AWS CLI<a name="USER_DeleteMultiAZDBCluster.Deleting.CLI"></a>

To delete a Multi\-AZ DB cluster by using the AWS CLI, call the [delete\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-cluster.html) command with the following options:
+ `--db-cluster-identifier`
+ `--final-db-snapshot-identifier` or `--skip-final-snapshot`

**Example With a final snapshot**  
For Linux, macOS, or Unix:  

```
aws rds delete-db-cluster \
    --db-cluster-identifier mymultiazdbcluster \
    --final-db-snapshot-identifier mymultiazdbclusterfinalsnapshot
```
For Windows:  

```
aws rds delete-db-instance ^
    --db-cluster-identifier mymultiazdbcluster ^
    --final-db-snapshot-identifier mymultiazdbclusterfinalsnapshot
```

**Example With no final snapshot**  
For Linux, macOS, or Unix:  

```
aws rds delete-db-instance \
    --db-cluster-identifier mymultiazdbcluster \
    --skip-final-snapshot
```
For Windows:  

```
aws rds delete-db-instance ^
    --db-cluster-identifier mymultiazdbcluster ^
    --skip-final-snapshot
```

### RDS API<a name="USER_DeleteMultiAZDBCluster.Deleting.API"></a>

To delete a Multi\-AZ DB cluster by using the Amazon RDS API, call the [DeleteDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBCluster.html) operation with the following parameters:
+ `DBClusterIdentifier`
+ `FinalDBSnapshotIdentifier` or `SkipFinalSnapshot`

## Multi\-AZ DB cluster metrics<a name="multi-az-db-cluster-metrics"></a>

Multi\-AZ DB clusters include the following Amazon CloudWatch metrics\.


| Metric | Console name | Description | Units | 
| --- | --- | --- | --- | 
| FreeLocalStorage |   **Free Local Storage \(MB\)**   |  The amount of available local storage space\.  |  Bytes  | 
| ReadIOPSLocalStorage |   **Read IOPS Local Storage \(Count/Second\)**   |  The average number of disk read I/O operations to local storage per second\.  |  Count/Second  | 
| ReadLatencyLocalStorage |   **Read Latency Local Storage \(Milliseconds\)**   |  The average amount of time taken per disk I/O operation for local storage\.  |  Seconds  | 
| ReadThroughputLocalStorage |   **Read Throughput Local Storage \(MB/Second\)**   |  The average number of bytes read from disk per second for local storage\.  |  Bytes/Second  | 
| ReplicaLag |   **Replica Lag \(Milliseconds\)**   |  The difference in time between the latest transaction on the writer DB instance and the latest applied transaction on a reader DB instance in a Multi\-AZ DB cluster\.  |  Milliseconds  | 
| WriteIOPSLocalStorage |   **Write IOPS Local Storage \(Count/Second\)**   |  The average number of disk write I/O operations per second on local storage in a Multi\-AZ DB cluster\.  |  Count/Second  | 
| WriteLatencyLocalStorage |   **Write Latency Local Storage \(Milliseconds\)**   |  The average amount of time taken per disk I/O operation on local storage in a Multi\-AZ DB cluster\.  |  Milliseconds  | 
| WriteThroughputLocalStorage |   **Write Throughput Local Storage \(MB/Second\)**   |  The average number of bytes written to disk per second for local storage\.  |  Bytes/Second  | 

For more information about Amazon CloudWatch metrics, see [Monitoring Amazon RDS metrics with Amazon CloudWatch](monitoring-cloudwatch.md)\.

## Limitations for Multi\-AZ DB clusters<a name="multi-az-db-clusters-concepts.Limitations"></a>

The following limitations apply to the Multi\-AZ DB cluster preview:
+ You can create a Multi\-AZ DB cluster only with MySQL version 8\.0\.26 and higher 8\.0 versions, and PostgreSQL version 13\.4 and higher 13 versions\.
+ You can create Multi\-AZ DB clusters only in the following AWS Regions:
  + US East \(N\. Virginia\)
  + US West \(Oregon\)
  + Europe \(Ireland\)
+ Multi\-AZ DB clusters only support Provisioned IOPS storage\.
+ You can't change a single\-AZ DB instance deployment or Multi\-AZ DB instance deployment into a Multi\-AZ DB cluster\. As an alternative, you can restore a snapshot of a single\-AZ DB instance deployment or Multi\-AZ DB instance deployment to a Multi\-AZ DB cluster\.
+ You can't restore a Multi\-AZ DB cluster snapshot to a Multi\-AZ DB instance deployment or single\-AZ deployment\.
+ Multi\-AZ DB clusters don't support modifications at the DB instance level because all modifications are done at the DB cluster level\.
+ Multi\-AZ DB clusters don't support the following features:
  + Amazon RDS Proxy
  + AWS Backup
  + AWS CloudFormation
  + Exporting Multi\-AZ DB cluster snapshot data to an Amazon S3 bucket
  + IAM DB authentication
  + Kerberos authentication
  + Modifying the port

    As an alternative, you can restore a Multi\-AZ DB cluster to a point in time and specify a different port\.
  + Option groups
  + Publishing logs to CloudWatch Logs
  + Read replicas
  + Reserved DB instances
  + Restoring a Multi\-AZ DB cluster snapshot from an Amazon S3 bucket
  + Storage autoscaling by setting the maximum allocated storage

    As an alternative, you can scale storage manually\.
  + Stopping and starting the DB cluster
  + Tags
+ RDS for MySQL Multi\-AZ DB clusters don't support replication to an external target database\.
+ RDS for MySQL Multi\-AZ DB clusters don't support the stored procedures described in [MySQL on Amazon RDS SQL reference](Appendix.MySQL.SQLRef.md)\.
+ RDS for PostgreSQL Multi\-AZ DB clusters don't support the following PostgreSQL extensions: `aws_s3`, `pg_transport`, and `pglogical`\.
+ RDS for PostgreSQL Multi\-AZ DB clusters don't support using a custom DNS server for outbound network access\.
+ RDS for PostgreSQL Multi\-AZ DB clusters don't support logical replication\.