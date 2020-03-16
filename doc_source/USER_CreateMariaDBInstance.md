# Creating a DB Instance Running the MariaDB Database Engine<a name="USER_CreateMariaDBInstance"></a>

The basic building block of Amazon RDS is the DB instance\. The DB instance is where you create your MariaDB databases\. 

**Important**  
Before you can create or connect to a DB instance, you must complete the tasks in [Setting Up for Amazon RDS](CHAP_SettingUp.md)\.

For an example that walks you through the process of creating and connecting to a sample DB instance, see [Creating a MariaDB DB Instance and Connecting to a Database on a MariaDB DB Instance](CHAP_GettingStarted.CreatingConnecting.MariaDB.md)\. 

**Note**  
If you are using the console, a new console interface is available for database creation\. Choose either the **New Console** or the **Original Console** instructions based on the console that you are using\. The **New Console** instructions are open by default\.

## New Console<a name="USER_CreateMariaDBInstance.CON"></a>

You can create a DB instance running MariaDB with the AWS Management Console with **Easy Create** enabled or not enabled\. With **Easy Create** enabled, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy Create** uses the default setting for other configuration options\. With **Easy Create** not enabled, you specify more configuration options when you create a database, including ones for availability, security, backups, and maintenance\.

**Note**  
For this example, **Standard Create** is enabled, and **Easy Create** isn't enabled\. For information about creating a MariaDB DB instance with **Easy Create** enabled, see [Creating a MariaDB Instance](CHAP_GettingStarted.CreatingConnecting.MariaDB.md#CHAP_GettingStarted.Creating.MariaDB)\.

**To create a MariaDB DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**\.

1. In **Choose a database creation method**, choose **Standard Create**\.

1. In **Engine options**, choose **MariaDB**\.   
![\[Select engine\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MariaDB-Launch01.png)

1. In **Templates**, choose the template that matches your use case\. If you choose **Production**, the following are also chosen in a later step:
   + **Multi\-AZ** failover option 
   + **Provisioned IOPS** storage option
   + **Enable deletion protection** option

   We recommend these features for any production environment\. 

1. To enter your master password, do the following:

   1. In the **Settings** section, open **Credential Settings**\.

   1. Clear the **Auto generate a password** check box\.

   1. \(Optional\) Change the **Master username** value and enter the same password in **Master password** and **Confirm password**\.

   By default, the new DB instance uses an automatically generated password for the master user\.

1. For the remaining sections, specify your DB instance settings\. For information about each setting, see [Settings for MariaDB DB Instances](#USER_CreateMariaDBInstance.Settings)\. 

1. Choose **Create database**\. 

   If you chose to use an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the master username and password for the DB instance, choose **View credential details**\.  
![\[Master user credentials after automatic password generation.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-credentials.png)

   To connect to the DB instance as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\. If you need to change the master user password after the DB instance is available, you can modify the DB instance to do so\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

1. In the **Databases** list, choose the name of the new MariaDB DB instance\.

   On the RDS console, the details for the new DB instance appear\. The DB instance has a status of **creating** until the DB instance is created and ready for use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and storage allocated, it could take several minutes for the new instance to be available\.   
![\[My DB instances details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MariaDB-Launch06.png)

## Original Console<a name="USER_CreateMariaDBInstance.CurrentCON"></a>

**To launch a MariaDB DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top right corner of the AWS Management Console, choose the AWS Region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Databases**\.

   If the navigation pane is closed, choose the menu icon at the top left to open it\.

1. Choose **Create database** to open the **Select engine** page\.  
![\[Select engine\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/CURRENT-MariaDB-Launch01.png)

1. Choose **MariaDB**, and then choose **Next**\. 

1. The **Choose use case** page asks if you are planning to use the DB instance you are creating for production\. If you are, choose **Production \- MariaDB**\. If you choose **Production \- MariaDB**, the following are also chosen in a later step:
   + **Multi\-AZ** failover option 
   + **Provisioned IOPS** storage option
   + **Enable deletion protection** option

   We recommend these features for any production environment\. 

1. Choose **Next** to continue\. The **Specify DB details** page appears\. 

   On the **Specify DB details** page, specify your DB instance information\. For information about each setting, see [Settings for MariaDB DB Instances](#USER_CreateMariaDBInstance.Settings)\.   
![\[Specify DB details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/CURRENT-MariaDB-Launch02a.png)

1. Choose **Next** to continue\. 

   On the **Configure advanced settings** page, provide additional information that Amazon RDS needs to launch the DB instance\. For information about each setting, see [Settings for MySQL DB Instances](USER_CreateInstance.md#USER_CreateInstance.Settings)\. 

1. Choose **Create database**\. 

1.  On the final page, choose **View DB instance details**\. 

On the RDS console, the details for the new DB instance appear\. The DB instance has a status of **creating** until the DB instance is created and ready for use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and storage allocated, it could take several minutes for the new instance to be available\. 

![\[My DB instances details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/CURRENT-MariaDB-Launch06.png)

## AWS CLI<a name="USER_CreateMariaDBInstance.CLI"></a>

To create a MariaDB DB instance by using the AWS CLI, call the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command with the parameters below\. For information about each setting, see [Settings for MariaDB DB Instances](#USER_CreateMariaDBInstance.Settings)\. 
+ `--db-instance-identifier`
+ `--db-instance-class`
+ `--db-security-groups`
+ `--db-subnet-group`
+ `--engine`
+ `--master-user-name`
+ `--master-user-password`
+ `--allocated-storage`
+ `--backup-retention-period`

**Note**  
If you require a specific minor version of MariaDB, include the `--engine-version` parameter\.

**Example**  
The following command creates a MariaDB instance named *mydbinstance*\.  
For Linux, OS X, or Unix:  

```
1. aws rds create-db-instance \
2.     --db-instance-identifier mydbinstance \
3.     --db-instance-class db.m4.xlarge \
4.     --engine mariadb \
5.     --allocated-storage 20 \
6.     --master-username masteruser \
7.     --master-user-password masteruserpassword \
8.     --backup-retention-period 3
```
For Windows:  

```
1. aws rds create-db-instance ^
2.     --db-instance-identifier mydbinstance ^
3.     --db-instance-class db.m4.xlarge ^
4.     --engine mariadb ^
5.     --allocated-storage 20 ^
6.     --master-username masteruser ^
7.     --master-user-password masteruserpassword ^
8.     --backup-retention-period 3
```
This command should produce output that begins with information that is similar to the following:  

```
1. DBINSTANCE 20 True 3 rds-ca-2015 False   arn:aws:rds:us-east-1:1234567890:db:mydbinstance db.m4.xlarge mydbinstance creating 0 **** mariadb 10.1.26 
```

## RDS API<a name="USER_CreateMariaDBInstance.API"></a>

To create a MariaDB DB instance by using the Amazon RDS API, call the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) operation with the parameters below\. For information about each setting, see [Settings for MariaDB DB Instances](#USER_CreateMariaDBInstance.Settings)\. 
+ `AllocatedStorage`
+ `BackupRetentionPeriod`
+ `DBInstanceClass`
+ `DBInstanceIdentifier`
+ `DBSecurityGroups`
+ `DBSubnetGroup`
+ `Engine`
+ `MasterUsername`
+ `MasterUserPassword`

**Note**  
If you require a specific minor version of MariaDB, include the `EngineVersion` parameter\.

**Example**  

```
 1. https://rds.us-west-2.amazonaws.com/
 2.     ?Action=CreateDBInstance
 3.     &AllocatedStorage=20
 4.     &BackupRetentionPeriod=3
 5.     &DBInstanceClass=db.m4.xlarge
 6.     &DBInstanceIdentifier=mydbinstance
 7.     &DBName=mydatabase
 8.     &DBSecurityGroups.member.1=mysecuritygroup
 9.     &DBSubnetGroup=mydbsubnetgroup
10.     &Engine=mariadb
11.     &MasterUserPassword=masteruserpassword
12.     &MasterUsername=masterawsuser
13.     &Version=2014-10-31
14.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
15.     &X-Amz-Credential=AKIADQKE4SARGYLE/20140213/us-west-2/rds/aws4_request
16.     &X-Amz-Date=20140213T162136Z
17.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
18.     &X-Amz-Signature=8052a76dfb18469393c5f0182cdab0ebc224a9c7c5c949155376c1c250fc7ec3
```

## Settings for MariaDB DB Instances<a name="USER_CreateMariaDBInstance.Settings"></a>

The following table contains details about settings that you choose when you create a Maria DB instance\. 


****  

| Setting | Setting Description | 
| --- | --- | 
|  Allocated storage  |  The amount of storage to allocate for your DB instance \(in gigabytes\)\. In some cases, allocating a higher amount of storage for your DB instance than the size of your database can improve I/O performance\.  For more information, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.   | 
|  Auto minor version upgrade  |  **Enable auto minor version upgrade** to enable your DB instance to receive preferred minor DB engine version upgrades automatically when they become available\. Amazon RDS performs automatic minor version upgrades in the maintenance window\.  | 
|  Availability zone  |  The Availability Zone for your DB instance\. Use the default value of **No Preference** unless you want to specify an Availability Zone\.  For more information, see [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)\.   | 
|  Backup retention period  |  The number of days that you want automatic backups of your DB instance to be kept\. For any nontrivial DB instance, set this value to **1** or greater\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   | 
|  Backup window  |  The time period during which Amazon RDS automatically takes a backup of your DB instance\. Unless you have a specific time that you want to have your database backed up, use the default of **No Preference**\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   | 
|  Copy tags to snapshots  |  This option copies any DB instance tags to a DB snapshot when you create a snapshot\.  For more information, see [Tagging Amazon RDS Resources](USER_Tagging.md)\.   | 
|  Database name  |  The name for the database on your DB instance\. If you don't provide a name, Amazon RDS doesn't create a database on the DB instance you are creating\. For this name, these constraints apply: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateMariaDBInstance.html) To create additional databases on your DB instance, connect to your DB instance and use the SQL command CREATE DATABASE\. For more information, see [Connecting to a DB Instance Running the MariaDB Database Engine](USER_ConnectToMariaDBInstance.md)\.   | 
|  Database port  |  The port that you want to access the DB instance through\. MariaDB installations default to port 3306\. If you use a DB security group with your DB instance, this port value must be the same one that you provided when creating the DB security group\.  The firewalls at some companies block connections to the default MariaDB port\. If your company firewall blocks the default port, enter another port for your DB instance\.   | 
|  DB engine version  |  The version of MariaDB that you want to use\.  | 
|  DB instance class  |  The configuration for your DB instance\.  If possible, choose an instance class large enough that a typical query working set can be held in memory\. When working sets are held in memory, the system can avoid writing to disk, and this improves performance\.  For more information, see [DB Instance Classes](Concepts.DBInstanceClass.md)\.   | 
|  DB instance identifier  |  The name for your DB instance\. Your DB instance identifier can contain up to 63 alphanumeric characters, and must be unique for your account in the AWS Region you chose\. You can add some intelligence to the name, such as including the AWS Region you chose, for example **mariadb\-instance1**\.   | 
|  DB parameter group  |  A parameter group for your DB instance\. You can choose the default parameter group or you can create a custom parameter group\.  For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.   | 
| Deletion protection |  **Enable deletion protection** to prevent your DB instance from being deleted\. If you create a production DB instance with the AWS Management Console, deletion protection is enabled by default\. For more information, see [Deleting a DB Instance](USER_DeleteInstance.md)\.   | 
|  Encryption  |  **Enable Encryption** to enable encryption at rest for this DB instance\.  For more information, see [Encrypting Amazon RDS Resources](Overview.Encryption.md)\.   | 
|  Enhanced Monitoring  |  **Enable enhanced monitoring** to enable gathering metrics in real time for the operating system that your DB instance runs on\.   For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.  | 
|  License model  |  MariaDB has only one license model, **general\-public\-license**, the general license agreement for MariaDB\.  | 
| **Log exports** |  The types of MariaDB database log files to generate\. For more information, see [MariaDB Database Log Files](USER_LogAccess.Concepts.MariaDB.md)\.   | 
|  Maintenance window  |  The 30\-minute window in which pending modifications to your DB instance are applied\. If the time period doesn't matter, choose **No Preference**\.  For more information, see [The Amazon RDS Maintenance Window](USER_UpgradeDBInstance.Maintenance.md#Concepts.DBMaintenance)\.   | 
|  Master password  |  The password for your master user account\. The password must contain 8–41 printable ASCII characters \(excluding `/`, `"`, a space, and `@`\)\.  | 
|  Master username  |  The name that you use as the master user name to log on to your DB instance\. For this name, these constraints apply: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateMariaDBInstance.html) For more information, and a list of the default privileges for the master user, see [MariaDB Security on Amazon RDS](CHAP_MariaDB.md#MariaDB.Concepts.UsersAndPrivileges)\.  | 
|  Multi\-AZ deployment  |  **Create replica in different zone** to create a standby mirror of your DB instance in another Availability Zone for failover support\. We recommend Multi\-AZ for production workloads to maintain high availability\. For development and testing, you can choose **No**\.  For more information, see [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)\.   | 
|  Option group  |  An option group for your DB instance\. You can choose the default option group or you can create a custom option group\.  For more information, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\.   | 
| Performance Insights |  **Enable Performance Insights** to monitor your DB instance load so that you can analyze and troubleshoot your database performance\. Choose a retention period to determine how much rolling data history to retain\. The default of seven days is in the free tier\. Long\-term retention \(two years\) is priced per vCPU per month\. Choose a master key to use to protect the key used to encrypt this database volume\. Choose from the master keys in your account, or enter the key from a different account\. For more information, see [Using Amazon RDS Performance Insights](USER_PerfInsights.md)\.  | 
|  Publicly accessible  |  **Yes** to give the DB instance a public IP address, meaning that it's accessible outside the VPC\. To be publicly accessible, the DB instance also has to be in a public subnet in the VPC\. **No** to make the DB instance accessible only from inside the VPC\. For more information, see [Hiding a DB Instance in a VPC from the Internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\.   | 
|  Storage autoscaling  |  **Enable storage autoscaling** to enable Amazon RDS to automatically increase storage when needed to avoid having your DB instance run out of storage space\. Use **Maximum storage threshold** to set the upper limit for Amazon RDS to automatically increase storage for your DB instance\. The default is 1,000 GiB\. For more information, see [Managing Capacity Automatically with Amazon RDS Storage Autoscaling](USER_PIOPS.StorageTypes.md#USER_PIOPS.Autoscaling)\.   | 
|  Storage type  |  The storage type for your DB instance\.  For more information, see [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage)\.   | 
|  Subnet group  |  This setting depends on the platform that you are on\. If you are a new customer to AWS, keep the subnet group set to **default**, which is the default DB subnet group that was created for your account\. If you are creating a DB instance on the earlier E2\-Classic platform, you might want your DB instance in a specific VPC\. In this case, choose the DB subnet group that you created for that VPC\.   | 
|  Virtual Private Cloud \(VPC\)  |  This setting depends on the platform that you are on\. If you are a new customer to AWS, choose the default VPC shown\. If you are creating a DB instance on the earlier E2\-Classic platform that doesn't use a VPC, choose **Not in VPC**\.  For more information, see [Amazon Virtual Private Cloud VPCs and Amazon RDS](USER_VPC.md)\.   | 
|  VPC security groups  | If you are a new customer to AWS, Create new to create a new VPC security group\. Otherwise, Choose existing, then choose from security groups that you previously created\.When you choose **Create new** in the RDS console, a new security group is created\. This new security group has an inbound rule that allows access to the DB instance from the IP address detected in your browser\.For more information, see [Working with DB Security Groups \(EC2\-Classic Platform\)](USER_WorkingWithSecurityGroups.md)\.  | 