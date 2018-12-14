# Creating a DB Instance Running the Microsoft SQL Server Database Engine<a name="USER_CreateMicrosoftSQLServerInstance"></a>

The basic building block of Amazon RDS is the DB instance\. Your Amazon RDS DB instance is similar to your on\-premises Microsoft SQL Server\. After you create your SQL Server DB instance, you can add one or more custom databases to it\. 

**Important**  
You must complete the tasks in the [Setting Up for Amazon RDS](CHAP_SettingUp.md) section before you can create or connect to a DB instance\. 

For an example that walks you through the process of creating and connecting to a sample DB instance, see [Creating a Microsoft SQL Server DB Instance and Connecting to a DB Instance](CHAP_GettingStarted.CreatingConnecting.SQLServer.md)\. 

## AWS Management Console<a name="USER_CreateMicrosoftSQLServerInstance.CON"></a>

**To launch a SQL Server DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top right corner of the Amazon RDS console, choose the region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Instances**\.

   If the navigation pane is closed, choose the menu icon at the top left to open it\.

1. Choose **Create database** to open the **Select engine** page\.

1. Choose the **Microsoft SQL Server** icon\.   
![\[Engine selection\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQLSvr-Launch01.png)

1. Choose the SQL Server DB engine edition that you want to use\. The SQL Server editions that are available vary by region\. 

1. For some editions, the **Use Case** step asks if you are planning to use the DB instance you are creating for production\. If you are, choose **Production**\. If you choose **Production**, the following are all preselected in a later step:
   + **Multi\-AZ** failover option 
   + **Provisioned IOPS** storage option
   + **Enable deletion protection** option

   We recommend these features for any production environment\. 

1. Choose **Next** to continue\. The **Specify DB Details** page appears\. 

   On the **Specify DB Details** page, specify your DB instance information\. For information about each setting, see [Settings for Microsoft SQL Server DB Instances](#USER_CreateMicrosoftSQLServerInstance.Settings)\.   
![\[DB instance details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQLSvr-Launch02.png)

1. Choose **Next** to continue\. The **Configure Advanced Settings** page appears\. 

   On the **Configure Advanced Settings** page, provide additional information that Amazon RDS needs to launch the DB instance\. For information about each setting, see [Settings for Microsoft SQL Server DB Instances](#USER_CreateMicrosoftSQLServerInstance.Settings)\.   
![\[Additional Configuration panel\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQLSvr-Launch03.png)

1. Choose **Launch DB Instance**\. 

1. On the final page of the wizard, choose **Close**\. 

On the RDS console, the new DB instance appears in the list of DB instances\. The DB instance has a status of **creating** until the DB instance is ready to use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\. 

![\[My DB instances list\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQLSvr-Launch05.png)

## CLI<a name="USER_CreateMicrosoftSQLServerInstance.CLI"></a>

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

## API<a name="USER_CreateMicrosoftSQLServerInstance.API"></a>

To create a Microsoft SQL Server DB instance by using the Amazon RDS API, call the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) action with the parameters below\. For information about each setting, see [Settings for Microsoft SQL Server DB Instances](#USER_CreateMicrosoftSQLServerInstance.Settings)\. 
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
|  Allocated Storage  |  The amount of storage to allocate for your DB instance \(in gigabytes\)\. In some cases, allocating a higher amount of storage for your DB instance than the size of your database can improve I/O performance\.  For more information, see [DB instance storage](CHAP_Storage.md)\.   | 
|  Auto minor version upgrade  |  Choose **Enable auto minor version upgrade** to enable your DB instance to receive preferred minor DB engine version upgrades automatically when they become available\. Amazon RDS performs automatic minor version upgrades in the maintenance window\.  | 
|  Availability Zone  |  The availability zone for your DB instance\. Use the default value of **No Preference** unless you want to specify an Availability Zone\.  For more information, see [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)\.   | 
|  Backup Retention Period  |  The number of days that you want automatic backups of your DB instance to be retained\. For any non\-trivial DB instance, you should set this value to **1** or greater\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   | 
|  Backup Window  |  The time period during which Amazon RDS automatically takes a backup of your DB instance\. Unless you have a specific time that you want to have your database backup, use the default of **No Preference**\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   | 
|  Copy Tags To Snapshots  |  Select this option to copy any DB instance tags to a DB snapshot when you create a snapshot\.  For more information, see [Tagging Amazon RDS Resources](USER_Tagging.md)\.   | 
|  Database Port  |  The port that you want to access the DB instance through\. SQL Server installations default to port 1433\. If you use a DB security group with your DB instance, this must be the same port value you provided when creating the DB security group\.     | 
|  DB Engine Version  |  The version of Microsoft SQL Server that you want to use\.  | 
|  DB Instance Class  |  The configuration for your DB instance\. For example, a **db\.m1\.small** instance class equates to 1\.7 GiB memory, 1 ECU \(1 virtual core with 1 ECU\), 64\-bit platform, and moderate I/O capacity\.  If possible, choose an instance class large enough that a typical query working set can be held in memory\. When working sets are held in memory the system can avoid writing to disk, and this improves performance\.  For more information, see [DB Instance Class](Concepts.DBInstanceClass.md) and [DB Instance Class Support for Microsoft SQL Server](CHAP_SQLServer.md#SQLServer.Concepts.General.InstanceClasses)\.   | 
|  DB Instance Identifier  |  The name for your DB instance\. Name your DB instances in the same way that you would name your on\-premises servers\. Your DB instance identifier can contain up to 63 alphanumeric characters, and must be unique for your account in the region you chose\. You can add some intelligence to the name, such as including the region and DB engine you chose, for example **sqlsv\-instance1**\.   | 
|  DB Parameter Group  |  A parameter group for your DB instance\. You can choose the default parameter group or you can create a custom parameter group\.  For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.   | 
| Enable deletion protection | Enable deletion protection to prevent your DB instance from being deleted\. If you create a production DB instance with the AWS Management Console, deletion protection is enabled by default\. For more information, see [Deleting a DB Instance](USER_DeleteInstance.md)\.  | 
|  Enable Encryption  |  **Yes** to enable encryption at rest for this DB instance\.  For more information, see [Encrypting Amazon RDS Resources](Overview.Encryption.md)\.   | 
|  Enable Enhanced Monitoring  |  **Yes** to gather metrics in real time for the operating system that your DB instance runs on\.  For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.   | 
|  License Model  |  The license model that you want to use\. Choose **license\-included** to use the general license agreement for Microsoft SQL Server\.    | 
|  Maintenance Window  |  The 30 minute window in which pending modifications to your DB instance are applied\. If the time period doesn't matter, choose **No Preference**\.  For more information, see [The Amazon RDS Maintenance Window](USER_UpgradeDBInstance.Maintenance.md#Concepts.DBMaintenance)\.   | 
|  Master Username  |  The name that you use as the master user name to log on to your DB Instance with all database privileges\. The master user name is a SQL Server Authentication login that is a member of the `processadmin`, `public`, and `setupadmin` fixed server roles\.  For more information, see [Microsoft SQL Server Security](CHAP_SQLServer.md#SQLServer.Concepts.General.FeatureSupport.UnsupportedRoles)\.   | 
|  Master User Password  |  The password for your master user account\. The password must contain from 8 to 128 printable ASCII characters \(excluding /,", a space, and @\)\.   | 
|  Multi\-AZ Deployment  |  **Yes** to create a passive secondary replica of your DB instance in another Availability Zone for failover support\. We recommend Multi\-AZ for production workloads to maintain high availability\. For development and testing, you can choose **No**\.  For more information, see [Multi\-AZ Deployments for Microsoft SQL Server](USER_SQLServerMultiAZ.md)\.   | 
|  Option Group  |  An option group for your DB instance\. You can choose the default option group or you can create a custom option group\.  For more information, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\.   | 
|  Publicly Accessible  |  **Yes** to give your DB instance a public IP address\. This means that it is accessible outside the VPC \(the DB instance also needs to be in a public subnet in the VPC\)\. Choose **No** if you want the DB instance to only be accessible from inside the VPC\.  For more information, see [Hiding a DB Instance in a VPC from the Internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\.   | 
|  Storage Type  |  The storage type for your DB instance\.  For more information, see [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage)\.   | 
|  Subnet Group  |  This setting depends on the platform you are on\. If you are a new customer to AWS, choose **default**, which is the default DB subnet group that was created for your account\. If you are creating a DB instance on the previous E2\-Classic platform and you want your DB instance in a specific VPC, choose the DB subnet group you created for that VPC\.   | 
|  Time Zone  |  The time zone for your DB instance\. If you don't choose a time zone, your DB instance uses the default time zone\.  For more information, see [Local Time Zone for Microsoft SQL Server DB Instances](CHAP_SQLServer.md#SQLServer.Concepts.General.TimeZone)\.   | 
|  VPC  |  This setting depends on the platform you are on\. If you are a new customer to AWS, choose the default VPC shown\. If you are creating a DB instance on the previous E2\-Classic platform that does not use a VPC, choose **Not in VPC**\.  For more information, see [Amazon Virtual Private Cloud \(VPCs\) and Amazon RDS](USER_VPC.md)\.   | 
|  VPC Security Group  |  If you are a new customer to AWS, choose the default VPC\. Otherwise, choose the VPC security group you previously created\.  When you choose **Create new VPC security group** in the RDS console, a new security group is created with an inbound rule that allows access to the DB instance from the IP address detected in your browser\. For more information, see [Working with DB Security Groups \(EC2\-Classic Platform\)](USER_WorkingWithSecurityGroups.md)\.   | 

## Related Topics<a name="USER_CreateMicrosoftSQLServerInstance.Related"></a>
+ [Tutorial: Create an Amazon VPC for Use with an Amazon RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)
+ [Connecting to a DB Instance Running the Microsoft SQL Server Database Engine](USER_ConnectToMicrosoftSQLServerInstance.md)
+ [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)
+ [Deleting a DB Instance](USER_DeleteInstance.md)