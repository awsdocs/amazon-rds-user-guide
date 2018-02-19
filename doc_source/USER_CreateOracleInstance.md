# Creating a DB Instance Running the Oracle Database Engine<a name="USER_CreateOracleInstance"></a>

 The basic building block of Amazon RDS is the DB instance\. This is the environment in which you run your Oracle databases\. 

**Important**  
You must complete the tasks in the [Setting Up for Amazon RDS](CHAP_SettingUp.md) section before you can create or connect to a DB instance\. 

For an example that walks you through the process of creating and connecting to a sample DB instance, see [Creating an Oracle DB Instance and Connecting to a Database on an Oracle DB Instance](CHAP_GettingStarted.CreatingConnecting.Oracle.md)\. 

## AWS Management Console<a name="USER_CreateOracleInstance.CON"></a>

**To launch an Oracle DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top right corner of the AWS Management Console, choose the region in which you want to create the DB instance\. 

1. In the navigation pane, choose **Instances**\. 

1. Choose **Launch DB Instance** to start the **Launch DB Instance Wizard**\. 

    The wizard opens on the **Select engine** page\. The Oracle editions that are available vary by region\.   
![\[Engine selection\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/OracleLaunchEE.png)

1. In the **Select engine** window, choose the **Select** button for the Oracle DB engine you want to use and then choose **Next**\. 

1. The next step asks if you are planning to use the DB instance you are creating for production\. If you are, choose **Production**\. When you choose **Production**, the failover option **Multi\-AZ deployment** and the **Provisioned IOPS** storage option will be preselected in the following step\. 

1. Choose **Next** to continue\. The **Specify DB details** page appears\. 

   On the **Specify DB details** page, specify your DB instance information\. For information about each setting, see [Settings for Oracle DB Instances](#USER_CreateOracleInstance.Settings)\.   
![\[DB instance details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Oracle-Launch02.png)

1. Choose **Next** to continue\. The **Configure advanced settings** page appears\. 

   On the **Configure advanced settings** page, provide additional information that RDS needs to launch the DB instance\. For information about each setting, see [Settings for Oracle DB Instances](#USER_CreateOracleInstance.Settings)\. 

1. Choose **Launch DB instance**\. 

1.  On the final page of the wizard, choose **View DB instance details**\. 

On the RDS console, the details for the new DB instance appear\. The DB instance has a status of **creating** until the DB instance is created and ready for use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and storage allocated, it could take several minutes for the new instance to be available\. 

![\[My DB instances list\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Oracle-Launch05.png)

## CLI<a name="USER_CreateOracleInstance.CLI"></a>

To create an Oracle DB instance by using the AWS CLI, call the [create\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command with the parameters below\. For information about each setting, see [Settings for Oracle DB Instances](#USER_CreateOracleInstance.Settings)\. 

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
The following command will launch the example DB instance\.  
For Linux, OS X, or Unix:  

```
 1. aws rds create-db-instance \
 2.     --engine oracle-se1 \
 3.     --db-instance-identifier mydbinstance \
 4.     --allocated-storage 20 \ 
 5.     --db-instance-class db.m1.small \
 6.     --db-security-groups mydbsecuritygroup \
 7.     --db-subnet-group mydbsubnetgroup \
 8.     --master-username masterawsuser \
 9.     --master-user-password masteruserpassword \
10.     --backup-retention-period 3
```
For Windows:  

```
 1. aws rds create-db-instance ^
 2.     --engine oracle-se1 ^
 3.     --db-instance-identifier mydbinstance ^
 4.     --allocated-storage 20 ^
 5.     --db-instance-class db.m1.small ^
 6.     --db-security-groups mydbsecuritygroup ^
 7.     --db-subnet-group mydbsubnetgroup ^
 8.     --master-username masterawsuser ^
 9.     --master-user-password masteruserpassword ^
10.     --backup-retention-period 3
```
This command should produce output similar to the following:   

```
1. DBINSTANCE  mydbinstance  db.m1.small  oracle-se1  20  sa  creating  3  ****  n  11.2.0.4.v1
2. SECGROUP  default  active
3. PARAMGRP  default.oracle-se1-11.2  in-sync
```

## API<a name="USER_CreateOracleInstance.API"></a>

To create an Oracle DB instance by using the Amazon RDS API, call the [CreateDBInstance](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) action with the parameters below\. For information about each setting, see [Settings for Oracle DB Instances](#USER_CreateOracleInstance.Settings)\. 

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
 9.     &Engine=oracle-se1
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

## Settings for Oracle DB Instances<a name="USER_CreateOracleInstance.Settings"></a>

The following table contains details about settings that you choose when you create an Oracle DB instance\. 


****  

| Setting | Setting Description | 
| --- | --- | 
|  Allocated storage  |  The amount of storage to allocate your DB instance \(in gigabytes\)\. In some cases, allocating a higher amount of storage for your DB instance than the size of your database can improve I/O performance\.  For more information, see [Storage for Amazon RDS](CHAP_Storage.md)\.   | 
|  Auto minor version upgrade  |  Amazon RDS does not support automatic minor version upgrades for DB instances running Oracle\. You must modify your DB instance manually to perform a minor version upgrade\.  Some options, such as Oracle Locator, Oracle Multimedia, and Oracle Spatial, require that you enable automatic minor version upgrades\. Upgrades for DB instances that use these options are installed during your scheduled maintenance window, and an outage occurs during the upgrade\. You can't disable automatic minor version upgrades at the same time as you modify the option group to remove such an option\.     | 
|  Availability zone  |  The availability zone for your DB instance\. Use the default of **No Preference** unless you need to specify a particular Availability Zone\.  For more information, see [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)\.   | 
|  Backup retention period  |  The number of days that you want automatic backups of your DB instance to be retained\. For any non\-trivial instance, you should set this value to **1** or greater\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   | 
|  Backup window  |  The time period during which Amazon RDS automatically takes a backup of your DB instance\. Unless you have a specific time that you want to have your database backup, use the default of **No Preference**\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   | 
|  Character set name  |  The character set for your DB instance\. The default value of **AL32UTF8** is for the Unicode 5\.0 UTF\-8 Universal character set\. You cannot change the character set after the DB instance is created\.   For more information, see [Oracle Character Sets Supported in Amazon RDS](Appendix.OracleCharacterSets.md)\.   | 
|  Copy tags to snapshots  |  Select this option to copy any DB instance tags to a DB snapshot when you create a snapshot\.  For more information, see [Tagging Amazon RDS Resources](USER_Tagging.md)\.   | 
|  Database name  |  The name for the database on your DB instance\. The name must begin with a letter and contain up to 8 alpha\-numeric characters\. You can't specify the string NULL, or any other reserved word, for the database name\. If you do not provide a name, Amazon RDS does not create a database on the DB instance you are creating\.   | 
|  Database port  |  The port that you want to access the DB instance through\. Oracle installations default to port 1521\.   | 
|  DB engine version  |  The version of Oracle that you want to use\.  | 
|  DB instance class  |  The DB instance class that you want to use\.  For more information, see [DB Instance Class](Concepts.DBInstanceClass.md) and [DB Instance Class Support for Oracle](CHAP_Oracle.md#Oracle.Concepts.InstanceClasses)\.   | 
|  DB instance identifier  |  The name for your DB instance\. The name must be unique for your account and region\. You can add some intelligence to the name, such as including the region and DB engine you chose, for example **oracle\-instance1**\.   | 
|  DB parameter group  |  A parameter group for your DB instance\. You can choose the default parameter group or you can create a custom parameter group\.  For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.   | 
|  Encryption  |  **Enable Encryption** to enable encryption at rest for this DB instance\.  For more information, see [Encrypting Amazon RDS Resources](Overview.Encryption.md)\.   | 
|  Enhanced monitoring  |  **Enable enhanced monitoring** to gather metrics in real time for the operating system that your DB instance runs on\.  For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.   | 
|  License model  |  The license model that you want to use\. Choose **license\-included** to use the general license agreement for Oracle\. Choose **bring\-your\-own\-license** to use your existing Oracle license\.  For more information, see [Oracle Licensing](CHAP_Oracle.md#Oracle.Concepts.Licensing)\.   | 
|  Maintenance window  |  The 30 minute window in which pending modifications to your DB instance are applied\. If the time period doesn't matter, choose **No Preference**\.  For more information, see [The Amazon RDS Maintenance Window](USER_UpgradeDBInstance.Maintenance.md#Concepts.DBMaintenance)\.   | 
|  Master username  |  The name that you use as the master user name to log on to your DB instance with all database privileges\. This user account is used to log into the DB instance and is granted DBA privileges\.  For more information, see [Oracle Security](CHAP_Oracle.md#Oracle.Concepts.RestrictedDBAPrivileges)\.   | 
|  Master password  |  The password for your master user account\. The password must contain from 8 to 30 printable ASCII characters \(excluding /,", and @\)\.   | 
|  Multi\-AZ deployment  |  **Create replica in different zone** to create a standby replica of your DB instance in another availability zone for failover support\. We recommend Multi\-AZ for production workloads to maintain high availability\. For development and testing, you can choose **No**\.  For more information, see [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)\.   | 
|  Option group  |  An option group for your DB instance\. You can choose the default option group or you can create a custom option group\.  For more information, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\.   | 
|  Public accessibility  |  **Yes** to give your DB instance a public IP address\. This means that it is accessible outside the VPC \(the DB instance also needs to be in a public subnet in the VPC\)\. Choose **No** if you want the DB instance to only be accessible from inside the VPC\.  For more information, see [Hiding a DB Instance in a VPC from the Internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\.   | 
|  Storage type  |  The storage type for your DB instance\.  For more information, see [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage)\.   | 
|  Subnet group  |  This setting depends on the platform you are on\. If you are a new customer to AWS, choose **default**, which is the default DB subnet group that was created for your account\. If you are creating a DB instance on the previous E2\-Classic platform and you want your DB instance in a specific VPC, choose the DB subnet group you created for that VPC\.   | 
|  Virtual Private Cloud \(VPC\)  |  This setting depends on the platform you are on\. If you are a new customer to AWS, choose the default VPC\. If you are creating a DB instance on the previous E2\-Classic platform, choose **Not in VPC**\.  For more information, see [Amazon Virtual Private Cloud \(VPCs\) and Amazon RDS](USER_VPC.md)\.   | 
|  VPC security groups  |  If you are a new customer to AWS, choose **Create new VPC security group**\. Otherwise, choose **Select existing VPC security groups**, and select security groups you previously created\.  For more information, see [Working with DB Security Groups \(EC2\-Classic Platform\)](USER_WorkingWithSecurityGroups.md)\.   | 

## Related Topics<a name="USER_CreateOracleInstance.Related"></a>

+ [Tutorial: Create an Amazon VPC for Use with an Amazon RDS DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)

+ [Connecting to a DB Instance Running the Oracle Database Engine](USER_ConnectToOracleInstance.md)

+ [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)

+ [Deleting a DB Instance](USER_DeleteInstance.md)