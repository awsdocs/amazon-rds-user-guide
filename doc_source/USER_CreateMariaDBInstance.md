# Creating a DB Instance Running the MariaDB Database Engine<a name="USER_CreateMariaDBInstance"></a>

The basic building block of Amazon RDS is the DB instance\. The DB instance is where you create your MariaDB databases\. 

**Important**  
You must complete the tasks in the [Setting Up for Amazon RDS](CHAP_SettingUp.md) section before you can create or connect to a DB instance\. 

For an example that walks you through the process of creating and connecting to a sample DB instance, see [Creating a MariaDB DB Instance and Connecting to a Database on a MariaDB DB Instance](CHAP_GettingStarted.CreatingConnecting.MariaDB.md)\. 

## AWS Management Console<a name="USER_CreateMariaDBInstance.CON"></a>

**To launch a MariaDB DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top right corner of the AWS Management Console, choose the region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Instances**\. 

1. Choose **Launch DB instance** to start the **Launch DB Instance Wizard**\. 

    The wizard opens on the **Select engine** page\.  
![\[Select Engine\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MariaDB-Launch01.png)

1. Choose **MariaDB**, and then choose **Next**\. 

1. The **Choose use case** page asks if you are planning to use the DB instance you are creating for production\. If you are, choose **Production \- MariaDB**\. If you choose **Production \- MariaDB**, the failover option **Multi\-AZ** and the **Provisioned IOPS** storage option are preselected in the following step\. We recommend these features for any production environment\. 

1. Choose **Next** to continue\. The **Specify DB details** page appears\. 

   On the **Specify DB details** page, specify your DB instance information\. For information about each setting, see [Settings for MariaDB DB Instances](#USER_CreateMariaDBInstance.Settings)\.   
![\[Specify DB details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MariaDB-Launch02a.png)

1. Choose **Next** to continue\. 

   On the **Configure advanced settings** page, provide additional information that Amazon RDS needs to launch the DB instance\. For information about each setting, see [Settings for MySQL DB Instances](USER_CreateInstance.md#USER_CreateInstance.Settings)\. 

1. Choose **Launch DB instance**\. 

1.  On the final page of the wizard, choose **View DB instance details**\. 

On the RDS console, the details for the new DB instance appear\. The DB instance has a status of **creating** until the DB instance is created and ready for use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and storage allocated, it could take several minutes for the new instance to be available\. 

![\[My DB instances details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MariaDB-Launch06.png)

## CLI<a name="USER_CreateMariaDBInstance.CLI"></a>

To create a MariaDB DB instance by using the AWS CLI, call the [create\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command with the parameters below\. For information about each setting, see [Settings for MariaDB DB Instances](#USER_CreateMariaDBInstance.Settings)\. 

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
The following command creates a MariaDB instance named *mydbinstance*\.  
For Linux, OS X, or Unix:  

```
1. aws rds create-db-instance \
2.     --db-instance-identifier mydbinstance \
3.     --db-instance-class db.m1.small \
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
3.     --db-instance-class db.m1.small ^
4.     --engine mariadb ^
5.     --allocated-storage 20 ^
6.     --master-username masteruser ^
7.     --master-user-password masteruserpassword ^
8.     --backup-retention-period 3
```
This command should produce output similar to the following:  

```
1. DBINSTANCE  mydbinstance  db.m1.small  mariadb  20  sa  creating  3  ****  n  10.0.17
2. SECGROUP  default  active
3. PARAMGRP  default.mariadb10.0  in-sync
```

## API<a name="USER_CreateMariaDBInstance.API"></a>

To create a MariaDB DB instance by using the Amazon RDS API, call the [CreateDBInstance](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) action with the parameters below\. For information about each setting, see [Settings for MariaDB DB Instances](#USER_CreateMariaDBInstance.Settings)\. 

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
 1. https://rds.us-west-2.amazonaws.com/
 2.     ?Action=CreateDBInstance
 3.     &AllocatedStorage=20
 4.     &BackupRetentionPeriod=3
 5.     &DBInstanceClass=db.m3.medium
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
|  Allocated storage  |  The amount of storage to allocate for your DB instance \(in gigabytes\)\. In some cases, allocating a higher amount of storage for your DB instance than the size of your database can improve I/O performance\.  For more information, see [Storage for Amazon RDS](CHAP_Storage.md)\.   | 
|  Auto minor version upgrade  |  **Enable auto minor version upgrade** to enable your DB instance to receive minor DB engine version upgrades automatically when they become available\.   | 
|  Availability zone  |  The availability zone for your DB instance\. Use the default value of **No Preference** unless you want to specify an Availability Zone\.  For more information, see [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)\.   | 
|  Backup retention period  |  The number of days that you want automatic backups of your DB instance to be retained\. For any non\-trivial DB instance, you should set this value to **1** or greater\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   | 
|  Backup window  |  The time period during which Amazon RDS automatically takes a backup of your DB instance\. Unless you have a specific time that you want to have your database backup, use the default of **No Preference**\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   | 
|  Copy Tags To Snapshots  |  Select this option to copy any DB instance tags to a DB snapshot when you create a snapshot\.  For more information, see [Tagging Amazon RDS Resources](USER_Tagging.md)\.   | 
|  Database name  |  The name for the database on your DB instance\. The name must contain 1 to 64 alpha\-numeric characters\. If you do not provide a name, Amazon RDS does not create a database on the DB instance you are creating\.  To create additional databases on your DB instance, connect to your DB instance and use the SQL command CREATE DATABASE\. For more information, see [Connecting to a DB Instance Running the MariaDB Database Engine](USER_ConnectToMariaDBInstance.md)\.   | 
|  Database port  |  The port that you want to access the DB instance through\. MariaDB installations default to port 3306\. If you use a DB security group with your DB instance, this must be the same port value you provided when creating the DB security group\.  The firewalls at some companies block connections to the default MariaDB port\. If your company firewall blocks the default port, choose another port for your DB instance\.   | 
|  DB engine version  |  The version of MariaDB that you want to use\.  | 
|  DB instance class  |  The configuration for your DB instance\.  If possible, choose an instance class large enough that a typical query working set can be held in memory\. When working sets are held in memory the system can avoid writing to disk, and this improves performance\.  For more information, see [DB Instance Class](Concepts.DBInstanceClass.md)\.   | 
|  DB instance identifier  |  The name for your DB instance\. Your DB instance identifier can contain up to 63 alphanumeric characters, and must be unique for your account in the region you chose\. You can add some intelligence to the name, such as including the region you chose, for example **mariadb\-instance1**\.   | 
|  DB parameter group  |  A parameter group for your DB instance\. You can choose the default parameter group or you can create a custom parameter group\.  For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.   | 
|  Encryption  |  **Enable Encryption** to enable encryption at rest for this DB instance\.  For more information, see [Encrypting Amazon RDS Resources](Overview.Encryption.md)\.   | 
|  Enhanced monitoring  |  **Enable enhanced monitoring** to gather metrics in real time for the operating system that your DB instance runs on\.  For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.   | 
|  License model  |  MariaDB has only one license model, **general\-public\-license** the general license agreement for MariaDB\.   | 
| **Log exports** |  Select the types of MariaDB database log files to generate\. For more information, see [MariaDB Database Log Files](USER_LogAccess.Concepts.MariaDB.md)\.   | 
|  Maintenance window  |  The 30 minute window in which pending modifications to your DB instance are applied\. If the time period doesn't matter, choose **No Preference**\.  For more information, see [The Amazon RDS Maintenance Window](USER_UpgradeDBInstance.Maintenance.md#Concepts.DBMaintenance)\.   | 
|  Master username  |  The name that you use as the master user name to log on to your DB Instance\.  For more information, and a list of the default privileges for the master user, see [MariaDB Security on Amazon RDS](CHAP_MariaDB.md#MariaDB.Concepts.UsersAndPrivileges)\.   | 
|  Master password  |  The password for your master user account\. The password must contain from 8 to 41 printable ASCII characters \(excluding /,", a space, and @\)\.   | 
|  Multi\-AZ deployment  |  **Create replica in different zone** to create a standby mirror of your DB instance in another Availability Zone for failover support\. We recommend Multi\-AZ for production workloads to maintain high availability\. For development and testing, you can choose **No**\.  For more information, see [High Availability \(Multi\-AZ\)](Concepts.MultiAZ.md)\.   | 
|  Option group  |  An option group for your DB instance\. You can choose the default option group or you can create a custom option group\.  For more information, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\.   | 
|  Public accessibility  |  **Yes** to give your DB instance a public IP address\. This means that it is accessible outside the VPC \(the DB instance also needs to be in a public subnet in the VPC\)\. Choose **No** if you want the DB instance to only be accessible from inside the VPC\.  For more information, see [Hiding a DB Instance in a VPC from the Internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\.   | 
|  Storage type  |  The storage type for your DB instance\.  For more information, see [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage)\.   | 
|  Subnet group  |  This setting depends on the platform you are on\. If you are a new customer to AWS, choose **default**, which is the default DB subnet group that was created for your account\. If you are creating a DB instance on the previous E2\-Classic platform and you want your DB instance in a specific VPC, choose the DB subnet group you created for that VPC\.   | 
|  Virtual Private Cloud \(VPC\)  |  This setting depends on the platform you are on\. If you are a new customer to AWS, choose the default VPC shown\. If you are creating a DB instance on the previous E2\-Classic platform that does not use a VPC, choose **Not in VPC**\.  For more information, see [Amazon Virtual Private Cloud \(VPCs\) and Amazon RDS](USER_VPC.md)\.   | 
|  VPC security groups  |  If you are a new customer to AWS, choose **Create new VPC security group**\. Otherwise, choose **Select existing VPC security groups**, and select security groups you previously created\.  For more information, see [Working with DB Security Groups \(EC2\-Classic Platform\)](USER_WorkingWithSecurityGroups.md)\.   | 

## Related Topics<a name="USER_CreateMariaDBInstance.Related"></a>

+ [Tutorial: Create an Amazon VPC for Use with an Amazon RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)

+ [Connecting to a DB Instance Running the MariaDB Database Engine](USER_ConnectToMariaDBInstance.md)

+ [Modifying a DB Instance Running the MariaDB Database Engine](USER_ModifyInstance.MariaDB.md)

+ [Deleting a DB Instance](USER_DeleteInstance.md)