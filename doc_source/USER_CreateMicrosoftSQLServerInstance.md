# Creating a DB Instance Running the Microsoft SQL Server Database Engine<a name="USER_CreateMicrosoftSQLServerInstance"></a>

The basic building block of Amazon RDS is the DB instance\. Your Amazon RDS DB instance is similar to your on\-premises Microsoft SQL Server\. After you create your SQL Server DB instance, you can add one or more custom databases to it\. 

**Important**  
Before you can create or connect to a DB instance, you must complete the tasks in [Setting Up for Amazon RDS](CHAP_SettingUp.md)\.

For an example that walks you through the process of creating and connecting to a sample DB instance, see [Creating a Microsoft SQL Server DB Instance and Connecting to a DB Instance](CHAP_GettingStarted.CreatingConnecting.SQLServer.md)\. 

**Note**  
If you are using the console, a new console interface is available for database creation\. Choose either the **New Console** or the **Original Console** instructions based on the console that you are using\. The **New Console** instructions are open by default\.

## New Console<a name="USER_CreateMicrosoftSQLServerInstance.CON"></a>

You can create a DB instance running Microsoft SQL Server with the AWS Management Console with **Easy create** enabled or not enabled\. With **Easy Create** enabled, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy Create** uses the default setting for other configuration options\. With **Easy Create** not enabled, you specify more configuration options when you create a database, including ones for availability, security, backups, and maintenance\.

**Note**  
For this example, **Standard Create** is enabled, and **Easy Create** isn't enabled\. For information about creating a Microsoft SQL Server DB instance with **Easy Create** enabled, see [Creating a Microsoft SQL Server DB Instance and Connecting to a DB Instance](CHAP_GettingStarted.CreatingConnecting.SQLServer.md)\.

**To create a SQL Server DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**\.

1. In **Choose a database creation method**, select **Standard Create**\.

1. In **Engine options**, choose **Microsoft SQL Server**\.   
![\[Engine selection\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQLSvr-Launch01.png)

1. For **Edition**, choose the SQL Server DB engine edition that you want to use\. The SQL Server editions that are available vary by AWS Region\.

1. For **Version**, choose the SQL server version\.

1. In **Templates**, choose the template that matches your use case\. If you choose **Production**, the following are preselected in a later step:
   + **Multi\-AZ** failover option
   + **Provisioned IOPS** storage option
   + **Enable deletion protection** option

   We recommend these features for any production environment\. 
**Note**  
Template choices vary by edition\.

1. To enter your master password, do the following:

   1. In the **Settings** section, open **Credential Settings**\.

   1. Clear the **Auto generate a password** check box\.

   1. \(Optional\) Change the **Master username** value and enter the same password in **Master password** and **Confirm password**\.

   By default, the new DB instance uses an automatically generated password for the master user\.

1. For the remaining sections, specify your DB instance settings\. For information about each setting, see [Settings for Microsoft SQL Server DB Instances](#USER_CreateMicrosoftSQLServerInstance.Settings)\. 

1. Choose **Create database**\. 

   If you chose to use an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the master user name and password for the DB instance, choose **View credential details**\.  
![\[Master user credentials after automatic password generation.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-credentials.png)

   To connect to the DB instance as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\. If you need to change the master user password after the DB instance is available, you can modify the DB instance to do so\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

1. For **Databases**, choose the name of the new SQL Server DB instance\.

   On the RDS console, the details for the new DB instance appear\. The DB instance has a status of **creating** until the DB instance is created and ready for use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and storage allocated, it can take several minutes for the new instance to be available\.   
![\[My DB instances details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQLSvr-Launch05.png)

## Original Console<a name="USER_CreateMicrosoftSQLServerInstance.CurrentCON"></a>

**To launch a SQL Server DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Databases**\.

   If the navigation pane is closed, choose the menu icon at the top left to open it\.

1. Choose **Create database** to open the **Select engine** page\.

1. Choose the **Microsoft SQL Server** icon\.   
![\[Engine selection\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/CURRENT-SQLSvr-Launch01.png)

1. Choose the SQL Server DB engine edition that you want to use\. The SQL Server editions that are available vary by AWS Region\. 

1. For some editions, the **Use Case** step asks if you are planning to use the DB instance you are creating for production\. If you are, choose **Production**\. If you choose **Production**, the following are all preselected in a later step:
   + **Multi\-AZ** failover option 
   + **Provisioned IOPS** storage option
   + **Enable deletion protection** option

   We recommend these features for any production environment\. 

1. Choose **Next** to continue\. The **Specify DB Details** page appears\. 

   On the **Specify DB Details** page, specify your DB instance information\. For information about each setting, see [Settings for Microsoft SQL Server DB Instances](#USER_CreateMicrosoftSQLServerInstance.Settings)\.   
![\[DB instance details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/CURRENT-SQLSvr-Launch02.png)

1. Choose **Next** to continue\. The **Configure Advanced Settings** page appears\. 

   On the **Configure Advanced Settings** page, provide additional information that Amazon RDS needs to launch the DB instance\. For information about each setting, see [Settings for Microsoft SQL Server DB Instances](#USER_CreateMicrosoftSQLServerInstance.Settings)\.   
![\[Additional Configuration panel\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/CURRENT-SQLSvr-Launch03.png)

1. Choose **Launch DB Instance**\. 

1. On the final page of the wizard, choose **Close**\. 

On the RDS console, the new DB instance appears in the list of DB instances\. The DB instance has a status of **creating** until the DB instance is ready to use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\. 

![\[My DB instances list\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/CURRENT-SQLSvr-Launch05.png)

## AWS CLI<a name="USER_CreateMicrosoftSQLServerInstance.CLI"></a>

To create a Microsoft SQL Server DB instance by using the AWS CLI, call the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command with the parameters below\. For information about each setting, see [Settings for Microsoft SQL Server DB Instances](#USER_CreateMicrosoftSQLServerInstance.Settings)\. 
+ `--db-instance-identifier`
+ `--db-instance-class`
+ `--db-security-groups`
+ `--db-subnet-group`
+ `--engine`
+ `--master-user-name`
+ `--master-user-password`
+ `--allocated-storage`
+ `--backup-retention-period`

**Example**  
For Linux, OS X, or Unix:  

```
 1. aws rds create-db-instance 
 2.     --engine sqlserver-se \
 3.     --db-instance-identifier mymsftsqlserver \
 4.     --allocated-storage 250 \
 5.     --db-instance-class db.m1.large \
 6.     --db-security-groups mydbsecuritygroup \
 7.     --db-subnet-group mydbsubnetgroup \
 8.     --master-user-name masterawsuser \
 9.     --master-user-password masteruserpassword \
10.     --backup-retention-period 3
```
For Windows:  

```
 1. aws rds create-db-instance ^
 2.     --engine sqlserver-se ^
 3.     --db-instance-identifier mydbinstance ^
 4.     --allocated-storage 250 ^
 5.     --db-instance-class db.m1.large ^
 6.     --db-security-groups mydbsecuritygroup ^
 7.     --db-subnet-group mydbsubnetgroup ^
 8.     --master-user-name masterawsuser ^ 
 9.     --master-user-password masteruserpassword ^
10.     --backup-retention-period 3
```
This command should produce output similar to the following:   

```
1. DBINSTANCE  mydbinstance  db.m1.large  sqlserver-se  250  sa  creating  3  ****  n  10.50.2789
2. SECGROUP  default  active
3. PARAMGRP  default.sqlserver-se-10.5  in-sync
```

## RDS API<a name="USER_CreateMicrosoftSQLServerInstance.API"></a>

To create a Microsoft SQL Server DB instance by using the Amazon RDS API, call the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) operation with the parameters below\. For information about each setting, see [Settings for Microsoft SQL Server DB Instances](#USER_CreateMicrosoftSQLServerInstance.Settings)\. 
+ `AllocatedStorage`
+ `BackupRetentionPeriod`
+ `DBInstanceClass`
+ `DBInstanceIdentifier`
+ `DBSecurityGroups`
+ `DBSubnetGroup`
+ `Engine`
+ `MasterUsername`
+ `MasterUserPassword`

**Example**  

```
 1. https://rds.amazonaws.com/
 2.     ?Action=CreateDBInstance
 3.     &AllocatedStorage=250
 4.     &BackupRetentionPeriod=3
 5.     &DBInstanceClass=db.m1.large
 6.     &DBInstanceIdentifier=mydbinstance
 7.     &DBSecurityGroups.member.1=mysecuritygroup
 8.     &DBSubnetGroup=mydbsubnetgroup
 9.     &Engine=sqlserver-se
10.     &MasterUserPassword=masteruserpassword
11.     &MasterUsername=masterawsuser
12.     &SignatureMethod=HmacSHA256
13.     &SignatureVersion=4
14.     &Version=2014-10-31
15.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
16.     &X-Amz-Credential=AKIADQKE4SARGYLE/20140305/us-west-1/rds/aws4_request
17.     &X-Amz-Date=20140305T185838Z
18.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
19.     &X-Amz-Signature=b441901545441d3c7a48f63b5b1522c5b2b37c137500c93c45e209d4b3a064a3
```

## Settings for Microsoft SQL Server DB Instances<a name="USER_CreateMicrosoftSQLServerInstance.Settings"></a>

The following table contains details about settings that you choose when you create a SQL Server DB instance\. 


****  

| Setting | Setting Description | 
| --- | --- | 
|  Allocated storage  |  The amount of storage to allocate for your DB instance \(in gigabytes\)\. In some cases, allocating a higher amount of storage for your DB instance than the size of your database can improve I/O performance\.  For more information, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.   | 
|  Auto minor version upgrade  |  Not supported on Amazon RDS for SQL Server | 
|  Availability zone  |  The Availability Zone for your DB instance\. Use the default value of **No Preference** unless you want to specify an Availability Zone\.  For more information, see [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)\.   | 
|  Backup retention period  |  The number of days that you want automatic backups of your DB instance to be retained\. For any nontrivial DB instance, set this value to **1** or greater\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   | 
|  Backup window  |  The time period during which Amazon RDS automatically takes a backup of your DB instance\. Unless you have a specific time that you want to have your database backed up, use the default of **No Preference**\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   | 
|  Collation  |  A server\-level collation for your DB instance\.  For more information, see [Server\-Level Collation for Microsoft SQL Server](Appendix.SQLServer.CommonDBATasks.Collation.md#Appendix.SQLServer.CommonDBATasks.Collation.Server)\. | 
|  Copy tags to snapshots  |  This option copies any DB instance tags to a DB snapshot when you create a snapshot\.  For more information, see [Tagging Amazon RDS Resources](USER_Tagging.md)\.   | 
|  Database port  |  The port that you want to access the DB instance through\. SQL Server installations default to port 1433\. If you use a DB security group with your DB instance, this port value must be the same one that you provided when creating the DB security group\.   | 
|  Database authentication  |  The database authentication option you want to use\. Choose **Password authentication** to authenticate database users with database passwords only\. Choose **Password and Kerberos authentication** to authenticate database users with database passwords and Kerberos authentication through an AWS Managed Microsoft AD created with AWS Directory Service\. Next, choose the directory or choose **Create a new Directory**\. For more information, see [Using Kerberos Authentication with Amazon RDS for Oracle](oracle-kerberos.md)\.  | 
|  DB engine version  |  The version of Microsoft SQL Server that you want to use\.  | 
|  DB instance class  |  The configuration for your DB instance\. For example, a **db\.m1\.small** instance class has 1\.7 GiB memory, 1 ECU \(1 virtual core with 1 ECU\), 64\-bit platform, and moderate I/O capacity\.  If possible, choose an instance class large enough that a typical query working set can be held in memory\. When working sets are held in memory the system can avoid writing to disk, and this improves performance\.  For more information, see [DB Instance Classes](Concepts.DBInstanceClass.md) and [DB Instance Class Support for Microsoft SQL Server](CHAP_SQLServer.md#SQLServer.Concepts.General.InstanceClasses)\.   | 
|  DB instance identifier  |  The name for your DB instance\. Name your DB instances in the same way that you name your on\-premises servers\. Your DB instance identifier can contain up to 63 alphanumeric characters, and must be unique for your account in the AWS Region you chose\. You can add some intelligence to the name, such as including the AWS Region and DB engine you chose, for example **sqlsv\-instance1**\.   | 
|  DB parameter group  |  A parameter group for your DB instance\. You can choose the default parameter group or you can create a custom parameter group\.  For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.   | 
| Deletion protection | **Enable deletion protection** to prevent your DB instance from being deleted\. If you create a production DB instance with the AWS Management Console, deletion protection is enabled by default\. For more information, see [Deleting a DB Instance](USER_DeleteInstance.md)\. | 
|  Encryption  |  **Enable Encryption** to enable encryption at rest for this DB instance\.  For more information, see [Encrypting Amazon RDS Resources](Overview.Encryption.md)\.   | 
|  Enhanced Monitoring  |  **Enable enhanced monitoring** to enable gathering metrics in real time for the operating system that your DB instance runs on\.   For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.   | 
|  License model  |  The license model that you want to use\. Choose **license\-included** to use the general license agreement for Microsoft SQL Server\.    | 
|  Maintenance window  |  The 30\-minute window in which pending modifications to your DB instance are applied\. If the time period doesn't matter, choose **No Preference**\.  For more information, see [The Amazon RDS Maintenance Window](USER_UpgradeDBInstance.Maintenance.md#Concepts.DBMaintenance)\.   | 
|  Master password  |  The password for your master user account\. The password must contain 8–128 printable ASCII characters \(excluding `/`,`"`, a space, and `@`\)\.   | 
|  Master username  |  The name that you use as the master user name to log on to your DB instance with all database privileges\. The master user name is a SQL Server Authentication login that is a member of the `processadmin`, `public`, and `setupadmin` fixed server roles\. For this name, these constraints apply: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateMicrosoftSQLServerInstance.html) For more information, see [Microsoft SQL Server Security](CHAP_SQLServer.md#SQLServer.Concepts.General.FeatureSupport.UnsupportedRoles)\.   | 
|  Multi\-AZ deployment  |  **Create a standby instance** to create a passive secondary replica of your DB instance in another Availability Zone for failover support\. We recommend Multi\-AZ for production workloads to maintain high availability\. For development and testing, you can choose **Do not create a standby instance**\.  For more information, see [Multi\-AZ Deployments for Microsoft SQL Server](USER_SQLServerMultiAZ.md)\.   | 
|  Option group  |  An option group for your DB instance\. You can choose the default option group or you can create a custom option group\.  For more information, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\.   | 
| Performance Insights |  **Enable Performance Insights** to monitor your DB instance load so that you can analyze and troubleshoot your database performance\. Choose a retention period to determine how much rolling data history to keep\. The default of seven days is in the free tier\. Long\-term retention \(two years\) is priced per vCPU per month\. Choose a master key to use to protect the key used to encrypt this database volume\. Choose from the master keys in your account, or enter the key from a different account\. For more information, see [Using Amazon RDS Performance Insights](USER_PerfInsights.md)\.  | 
|  Publicly accessible  |  **Yes** to give the DB instance a public IP address, meaning that it's accessible outside the VPC\. To be publicly accessible, the DB instance also has to be in a public subnet in the VPC\. **No** to make the DB instance accessible only from inside the VPC\. For more information, see [Hiding a DB Instance in a VPC from the Internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\.   | 
|  Storage autoscaling  |  **Enable storage autoscaling** to enable Amazon RDS to automatically increase storage when needed to avoid having your DB instance run out of storage space\. Use **Maximum storage threshold** to set the upper limit for Amazon RDS to automatically increase storage for your DB instance\. The default is 1,000 GiB\. For more information, see [Managing Capacity Automatically with Amazon RDS Storage Autoscaling](USER_PIOPS.StorageTypes.md#USER_PIOPS.Autoscaling)\.   | 
|  Storage type  |  The storage type for your DB instance\.  For more information, see [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage)\.   | 
|  Subnet group  |  This setting depends on the platform that you are on\. If you are a new customer to AWS, choose **default**, which is the default DB subnet group that was created for your account\. If you are creating a DB instance on the earlier E2\-Classic platform, you might want your DB instance in a specific VPC\. In this case, choose the DB subnet group that you created for that VPC\.   | 
|  Time zone  |  The time zone for your DB instance\. If you don't choose a time zone, your DB instance uses the default time zone\.  For more information, see [Local Time Zone for Microsoft SQL Server DB Instances](CHAP_SQLServer.md#SQLServer.Concepts.General.TimeZone)\.   | 
|  Virtual Private Cloud \(VPC\)  |  This setting depends on the platform that you are on\. If you are a new customer to AWS, choose the default VPC shown\. If you are creating a DB instance on the earlier E2\-Classic platform that doesn't use a VPC, choose **Not in VPC**\.  For more information, see [Amazon Virtual Private Cloud VPCs and Amazon RDS](USER_VPC.md)\.   | 
|  VPC security group  | If you are a new customer to AWS, Create new to create a new VPC security group\. Otherwise, Choose existing, then choose from security groups that you previously created\.When you choose **Create new** in the RDS console, a new security group is created\. This new security group has an inbound rule that allows access to the DB instance from the IP address detected in your browser\.For more information, see [Working with DB Security Groups \(EC2\-Classic Platform\)](USER_WorkingWithSecurityGroups.md)\.  | 