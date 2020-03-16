# Creating a DB Instance Running the PostgreSQL Database Engine<a name="USER_CreatePostgreSQLInstance"></a>

 The basic building block of Amazon RDS is the DB instance\. This is the environment in which you run your PostgreSQL databases\.

**Important**  
Before you can create or connect to a DB instance, you must complete the tasks in [Setting Up for Amazon RDS](CHAP_SettingUp.md)\.

If you are using the console, a new console interface is available for database creation\. Choose either the **New Console** or the **Original Console** instructions based on the console that you are using\. The **New Console** instructions are open by default\.

## New Console<a name="USER_CreatePostgreSQLInstance.CON"></a>

**To launch a PostgreSQL DB instance**

You can create a DB instance running PostgreSQL with the AWS Management Console with **Easy Create** enabled or disabled\. With **Easy Create** enabled, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy Create** uses the default setting for other configuration options\. With **Easy Create** not enabled, you specify more configuration options when you create a database, including ones for availability, security, backups, and maintenance\.
**Note**  
For this example, **Standard Create** is enabled, and **Easy Create** isn't enabled\. For information about creating a PostgreSQL DB instance with **Easy Create** enabled, see [Creating a PostgreSQL DB Instance and Connecting to a Database on a PostgreSQL DB Instance](CHAP_GettingStarted.CreatingConnecting.PostgreSQL.md)\.

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region where you want to create the DB instance\. 

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**\.

1. In **Choose a database creation method**, choose **Standard Create**\.

1. In **Engine options**, choose **PostgreSQL**\.  
![\[Engine selection\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Launch01a.png)

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

1. For the remaining sections, specify your DB instance settings\. For information about each setting, see [Settings for PostgreSQL DB Instances](#USER_CreatePostgreSQLInstance.Settings)\. 

1. Choose **Create database**\. 

   If you chose to use an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the master user name and password for the DB instance, choose **View credential details**\.  
![\[Master user credentials after automatic password generation.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-credentials.png)

   To connect to the DB instance as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\. If you need to change the master user password after the DB instance is available, you can modify the DB instance to do so\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

1. For **Databases**, choose the name of the new PostgreSQL DB instance\.

   On the RDS console, the details for the new DB instance appear\. The DB instance has a status of **creating** until the DB instance is created and ready for use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and storage allocated, it can take several minutes for the new instance to be available\.   
![\[My DB instances list\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Launch06.png)

## Original Console<a name="USER_CreatePostgreSQLInstance.CurrentCON"></a>

**To launch a PostgreSQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top\-right corner of the AWS Management Console, choose the AWS Region where you want to create the DB instance\. 

1. In the navigation pane, choose **Databases**\.

   If the navigation pane is closed, choose the menu icon at the top left to open it\.

1. Choose **Create database** to start open on the **Select engine** page\.   
![\[Engine selection\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Launch01a.png)

1. On the **Select engine** page, choose the PostgreSQL icon, and then choose **Next**\.

1. Next, the **Use case** page asks if you are planning to use the DB instance you are creating for production\. If you are, choose **Production**\. If you choose this option, the following are also chosen in a later step:
   + **Multi\-AZ** failover option 
   + **Provisioned IOPS** storage option
   + **Enable deletion protection** option

   Choose **Next** when you are finished\.

1. On the **Specify DB Details** page, specify your DB instance information\. Choose **Next** when you are finished\.  
****    
<a name="rds-postgresql-creating-db-details-settings-guidance"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreatePostgreSQLInstance.html)

1.  On the **Configure Advanced Settings** page, provide additional information that RDS needs to launch the PostgreSQL DB instance\. The table shows settings for an example DB instance\. Specify your DB instance information, then choose **Create database**\.  
****    
<a name="rds-postgresql-creating-advanced-settings-guidance"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreatePostgreSQLInstance.html)

1.  On the final page, choose **Create database**\. 

1. On the Amazon RDS console, the new DB instance appears in the list of DB instances\. The DB instance will have a status of **creating** until the DB instance is created and ready for use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and store allocated, it could take several minutes for the new instance to be available\.  
![\[My DB instances list\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Launch06.png)

## AWS CLI<a name="USER_CreatePostgreSQLInstance.CLI"></a>

To create a PostgreSQL DB instance, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command with the following parameters:
+ `--db-instance-identifier`
+ `--allocated-storage`
+ `--db-instance-class`
+ `--engine`
+ `--master-username`
+ `--master-user-password`

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds create-db-instance 
2.     --db-instance-identifier pgdbinstance \
3.     --allocated-storage 20 \ 
4.     --db-instance-class db.t2.small \
5.     --engine postgres \
6.     --master-username masterawsuser \
7.     --master-user-password masteruserpassword
```
For Windows:  

```
1. aws rds create-db-instance 
2.     --db-instance-identifier pgdbinstance ^
3.     --allocated-storage 20 ^ 
4.     --db-instance-class db.t2.small ^
5.     --engine postgres ^
6.     --master-username masterawsuser ^
7.     --master-user-password masteruserpassword
```
This command should produce output similar to the following:  

```
1. DBINSTANCE  pgdbinstance  db.t2.small  postgres  20  sa  creating  3  ****  n  9.3
2. SECGROUP  default  active
3. PARAMGRP  default.PostgreSQL9.3  in-sync
```

## RDS API<a name="USER_CreatePostgreSQLInstance.API"></a>

To create a PostgreSQL DB instance, use the Amazon RDS API[https://docs.aws.amazon.com/](https://docs.aws.amazon.com/) command with the following parameters:
+ `Engine = postgres`
+ `DBInstanceIdentifier = pgdbinstance`
+ `DBInstanceClass = db.t2.small`
+ `AllocatedStorage = 20`
+ `BackupRetentionPeriod = 3`
+ `MasterUsername = masterawsuser`
+ `MasterUserPassword = masteruserpassword`

**Example**  

```
 1. https://rds.amazonaws.com/
 2.     ?Action=CreateDBInstance
 3.     &AllocatedStorage=20
 4.     &BackupRetentionPeriod=3
 5.     &DBInstanceClass=db.t2.small
 6.     &DBInstanceIdentifier=pgdbinstance
 7.     &DBName=mydatabase
 8.     &DBSecurityGroups.member.1=mysecuritygroup
 9.     &DBSubnetGroup=mydbsubnetgroup
10.     &Engine=postgres
11.     &MasterUserPassword=<masteruserpassword>
12.     &MasterUsername=<masterawsuser>
13.     &SignatureMethod=HmacSHA256
14.     &SignatureVersion=4
15.     &Version=2013-09-09
16.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
17.     &X-Amz-Credential=AKIADQKE4SARGYLE/20140212/us-west-2/rds/aws4_request
18.     &X-Amz-Date=20140212T190137Z
19.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
20.     &X-Amz-Signature=60d520ca0576c191b9eac8dbfe5617ebb6a6a9f3994d96437a102c0c2c80f88d
```

## Settings for PostgreSQL DB Instances<a name="USER_CreatePostgreSQLInstance.Settings"></a>

The following table contains details about settings that you choose when you create a PostgreSQL DB instance\. 


****  

| Setting | Setting Description | 
| --- | --- | 
|  Allocated storage  |  The amount of storage to allocate for your DB instance \(in gigabytes\)\. In some cases, allocating a higher amount of storage for your DB instance than the size of your database can improve I/O performance\.  For more information, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.   | 
|  Auto minor version upgrade  |  **Enable auto minor version upgrade** to enable your DB instance to receive preferred minor DB engine version upgrades automatically when they become available\. Amazon RDS performs automatic minor version upgrades in the maintenance window\.  | 
|  Availability zone  |  The Availability Zone for your DB instance\. Use the default value of **No Preference** unless you want to specify an Availability Zone\.  For more information, see [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)\.   | 
|  Backup retention period  |  The number of days that you want automatic backups of your DB instance to be retained\. For any nontrivial DB instance, set this value to **1** or greater\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   | 
|  Backup window  |  The time period during which Amazon RDS automatically takes a backup of your DB instance\. Unless you have a specific time that you want to have your database backed up, use the default of **No Preference**\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   | 
|  Copy tags To snapshots  |  This option copies any DB instance tags to a DB snapshot when you create a snapshot\.  For more information, see [Tagging Amazon RDS Resources](USER_Tagging.md)\.   | 
|  Database authentication  |  The database authentication option you want to use\. Choose **Password authentication** to authenticate database users with database passwords only\. Choose **Password and IAM DB authentication** to authenticate database users with database passwords and user credentials through IAM users and roles\. For more information, see [IAM Database Authentication for MySQL and PostgreSQL](UsingWithRDS.IAMDBAuth.md)\.  | 
|  Database name  |  The name for the database on your DB instance\. If you don't provide a name, Amazon RDS doesn't create a database on the DB instance\. This name has these constraints: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreatePostgreSQLInstance.html) To create additional databases, connect to the DB instance and use the SQL command `CREATE DATABASE`\. For more information about connecting to the DB instance, see [Connecting to a DB Instance Running the PostgreSQL Database Engine](USER_ConnectToPostgreSQLInstance.md)\.  | 
|  Database port  |  The port that you want to access the DB instance through\. PostgreSQL installations default to port 3306\. If you use a DB security group with your DB instance, this port value must be the same one you provided when creating the DB security group\.  The firewalls at some companies block connections to the default PostgreSQL port\. If your company firewall blocks the default port, enter another port for your DB instance\.   | 
|  DB engine version  |  The version of PostgreSQL that you want to use\.  | 
|  DB instance class  |  The configuration for your DB instance\.  If possible, choose an instance class large enough that a typical query working set can be held in memory\. When working sets are held in memory, the system can avoid writing to disk, and this improves performance\.  For more information, see [DB Instance Classes](Concepts.DBInstanceClass.md)\.   | 
|  DB instance identifier  |  The name for your DB instance\. Your DB instance identifier can contain up to 63 alphanumeric characters, and must be unique for your account in the AWS Region you chose\. You can add some intelligence to the name, such as including the AWS Region you chose, for example **postgresql\-instance1**\.   | 
|  DB parameter group |  A parameter group for your DB instance\. You can choose the default parameter group or you can create a custom parameter group\.  For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.   | 
| Deletion protection |  **Enable deletion protection** to prevent your DB instance from being deleted\. If you create a production DB instance with the AWS Management Console, deletion protection is enabled by default\. For more information, see [Deleting a DB Instance](USER_DeleteInstance.md)\.  | 
| Encryption  |  **Enable Encryption** to enable encryption at rest for this DB instance\.  For more information, see [Encrypting Amazon RDS Resources](Overview.Encryption.md)\.   | 
| Enhanced Monitoring  |  **Enable enhanced monitoring** to enable gathering metrics in real time for the operating system that your DB instance runs on\.   For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.   | 
|  License model  |  PostgreSQL has only one license model, `general-public-license`, the general license agreement for PostgreSQL\.   | 
| **Log exports** |  The types of PostgreSQL database log files to generate\. For more information, see [PostgreSQL Database Log Files](USER_LogAccess.Concepts.PostgreSQL.md)\.   | 
|  Maintenance window  |  The 30\-minute window in which pending modifications to your DB instance are applied\. If the time period doesn't matter, choose **No Preference**\.  For more information, see [The Amazon RDS Maintenance Window](USER_UpgradeDBInstance.Maintenance.md#Concepts.DBMaintenance)\.   | 
|  Master password  |  The password for your master user account\. The password must contain 8–128 printable ASCII characters \(excluding `/`,`"`, a space, and `@`\)\.  | 
|  Master username  |  The name that you use as the master user name to log on to your DB instance: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreatePostgreSQLInstance.html) For information on the default privileges granted to the master user name, see [Amazon RDS for PostgreSQL Versions and Extensions](CHAP_PostgreSQL.md#PostgreSQL.Concepts)\.  | 
|  Multi\-AZ deployment  |  **Create a standby instance** to create a passive secondary replica of your DB instance in another Availability Zone for failover support\. We recommend Multi\-AZ for production workloads to maintain high availability\. For development and testing, you can choose **Do not create a standby instance**\. For more information, see [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)\.   | 
|  Option group  |  An option group for your DB instance\. You can choose the default option group or you can create a custom option group\.  For more information, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\.   | 
| Performance Insights |  **Enable Performance Insights** to monitor your DB instance load so that you can analyze and troubleshoot your database performance\. Choose a retention period to determine how much rolling data history to keep\. The default of seven days is in the free tier\. Long\-term retention \(two years\) is priced per vCPU per month\. Choose a master key to use to protect the key used to encrypt this database volume\. Choose from the master keys in your account, or enter the key from a different account\. For more information, see [Using Amazon RDS Performance Insights](USER_PerfInsights.md)\.  | 
|  Publicly accessible  |  **Yes** to give the DB instance a public IP address, meaning that it's accessible outside the VPC\. To be publicly accessible, the DB instance also has to be in a public subnet in the VPC\. **No** to make the DB instance accessible only from inside the VPC\. For more information, see [Hiding a DB Instance in a VPC from the Internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\.   | 
|  Storage autoscaling  |  **Enable storage autoscaling** to enable Amazon RDS to automatically increase storage when needed to avoid having your DB instance run out of storage space\. Use **Maximum storage threshold** to set the upper limit for Amazon RDS to automatically increase storage for your DB instance\. The default is 1,000 GiB\. For more information, see [Managing Capacity Automatically with Amazon RDS Storage Autoscaling](USER_PIOPS.StorageTypes.md#USER_PIOPS.Autoscaling)\.   | 
|  Storage type  |  The storage type for your DB instance\.  For more information, see [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage)\.   | 
|  Subnet group  |  This setting depends on the platform that you are on\. If you are a new customer to AWS, keep the subnet group set to **default**, which is the default DB subnet group that was created for your account\. If you are creating a DB instance on the earlier E2\-Classic platform, you might want your DB instance in a specific VPC\. In this case, choose the DB subnet group that you created for that VPC\.   | 
|  Virtual Private Cloud \(VPC\)  |  This setting depends on the platform that you are on\. If you are a new customer to AWS, choose the default VPC shown\. If you are creating a DB instance on the earlier E2\-Classic platform that doesn't use a VPC, choose **Not in VPC**\.  For more information, see [Amazon Virtual Private Cloud VPCs and Amazon RDS](USER_VPC.md)\.   | 
|  VPC security groups  | If you are a new customer to AWS, Create new to create a new VPC security group\. Otherwise, Choose existing, then choose from security groups that you previously created\.When you choose **Create new** in the RDS console, a new security group is created\. This new security group has an inbound rule that allows access to the DB instance from the IP address detected in your browser\.For more information, see [Working with DB Security Groups \(EC2\-Classic Platform\)](USER_WorkingWithSecurityGroups.md)\.  | 