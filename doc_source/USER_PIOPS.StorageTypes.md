# Working with Storage Types<a name="USER_PIOPS.StorageTypes"></a>

To specify how you want your data stored in Amazon RDS, you select a storage type and provide a storage size \(in gibibytes\) when you create or modify a DB instance\. You can change the type of storage your instance uses by modifying the DB instance, but changing the storage type results in a short outage for the instance\. However, increasing the allocated storage doesn't result in an outage\. For more information about Amazon RDS storage types, see [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage)\.

You can't reduce the amount of storage once it has been allocated\. The only way to reduce the amount of storage allocated to a DB instance is to dump the data out of the DB instance and create a new DB instance with less storage space\. You then load the data into the new DB instance\.

When estimating your storage needs, consider that Amazon RDS allocates a minimum amount of storage for file system structures\. This reserved space can be up to 3 percent of the allocated storage for a DB instance, though in most cases the reserved space is far less\. We recommend that you set up an Amazon CloudWatch alarm for your DB instance's free storage space and react when necessary\. For information on setting CloudWatch alarms, see the [CloudWatch Getting Started Guide](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/GettingStarted.html)\. 

**Topics**
+ [Modifying a DB Instance to Use a Different Storage Type](#USER_PIOPS.ModifyingExisting)
+ [Modifying IOPS and Storage Settings for a DB Instance That Uses Provisioned IOPS Storage](#USER_PIOPS.Modify)
+ [Creating a DB Instance That Uses Provisioned IOPS Storage](#USER_PIOPS.Creating)
+ [Creating a MySQL or MariaDB Read Replica That Uses Provisioned IOPS Storage](#USER_PIOPS.CreatingRR)

## Modifying a DB Instance to Use a Different Storage Type<a name="USER_PIOPS.ModifyingExisting"></a>

You can use the Amazon RDS Management Console, the Amazon RDS API, or the AWS Command Line Interface \(AWS CLI\) to modify a DB instance to use Standard \(Magnetic\), General Purpose \(SSD\), or Provisioned IOPS storage\. You must specify either a value for allocated storage or specify both allocated storage and IOPS values\. You might need to modify the amount of allocated storage in order to maintain the required ratio between IOPS and storage\. For more information about the required ratio between IOPS and storage, see [Using Provisioned IOPS Storage with Multi\-AZ, Read Replicas, Snapshots, VPC, and DB Instance Classes](CHAP_Storage.md#Overview.ProvisionedIOPS-support)\. 

An immediate outage occurs when you convert from one storage type to another\. The data for that DB instance is migrated to a new volume\. The duration of the migration depends on several factors such as database load, storage size, storage type, and amount of IOPS provisioned \(if any\)\. The typical migration time is a few minutes, and the DB instance is available for use during the migration\. However, when you are migrating to or from magnetic storage, the migration time usually takes longer, up to several days in some cases\. During the migration to or from magnetic storage, the DB instance is available for use, but might experience performance degradation\. 

For DB instances in a single Availability Zone, the DB instance is unavailable for a few minutes when the conversion is initiated\. For Multi\-AZ deployments, the time the DB instance is unavailable is limited to the time it takes for a failover operation to complete, which typically takes less than two minutes\. Although your DB instance is available for reads and writes during the conversion, you might experience degraded performance until the conversion process is complete\. This process can take several hours\. 

### AWS Management Console<a name="USER_PIOPS.ModifyingExisting.console"></a>

**To modify a DB instance to use a different storage type**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose the DB instance that you want to modify\.

1. For **Instance actions**, choose **Modify**\.

1. For **Storage type**, choose a value for the DB instance, and type a value for **Allocated Storage**\. If you are modifying your DB instance to use the Provisioned IOPS storage type, then also provide a **Provisioned IOPS** value\. For more information, see [Modifying IOPS and Storage Settings for a DB Instance That Uses Provisioned IOPS Storage](#USER_PIOPS.Modify)\.  
![\[Modify the storage type of a DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/modify-storage-type.png)

1. Choose**Continue** to move to the next screen\.

1. To immediately initiate conversion of the DB instance to use the new storage type, select the **Apply immediately** check box in the **Scheduling of modifications** section\. If you want the changes to be applied in the next maintenance window, choose that option\. An immediate outage occurs when the conversion is applied\. For more information about storage, see [Storage for Amazon RDS](CHAP_Storage.md)\.

1. When the settings are as you want them, choose **Modify DB instance**\.

### CLI<a name="w3ab1c23c31c10c11"></a>

To modify a DB instance to use a different storage type, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. Set the following parameters:
+ `--allocated-storage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `--storage-type` – The new storage type for the DB instance\. You can specify `gp2` for general purpose \(SSD\), `io1` for Provisioned IOPS\), or `standard` for magnetic storage\.
+ `--apply-immediately` – Use `--apply-immediately` to initiate conversion immediately, or `--no-apply-immediately` \(the default\) to apply the conversion during the next maintenance window\. An immediate outage occurs when the conversion is applied\. For more information about storage, see [Storage for Amazon RDS](CHAP_Storage.md)\.

### API<a name="w3ab1c23c31c10c13"></a>

Use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html) action\. Set the following parameters:
+ `AllocatedStorage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `StorageType` – The new storage type for the DB instance\. You can specify `gp2` for general purpose \(SSD\), `io1` for Provisioned IOPS\), or `standard` for magnetic storage\.
+ `ApplyImmediately` – Set to `True` if you want to initiate conversion immediately\. If `False` \(the default\), the conversion is applied during the next maintenance window\. An immediate outage occurs when the conversion is applied\. For more information about storage, see [Storage for Amazon RDS](CHAP_Storage.md)\.

## Modifying IOPS and Storage Settings for a DB Instance That Uses Provisioned IOPS Storage<a name="USER_PIOPS.Modify"></a>

You can modify the settings for an Oracle, PostgreSQL, MySQL, or MariaDB DB instance that uses Provisioned IOPS storage by using the AWS Management Console, the Amazon RDS API, or the AWS Command Line Interface \(AWS CLI\)\. You must specify the storage type, allocated storage, and the amount of Provisioned IOPS that you require\. You can choose from 1000 IOPS and 100 GiB of storage up to 40,000 IOPS and 16 TiB \(16384 GiB\) of storage, depending on your database engine\. You cannot reduce the amount of allocated storage from the value currently allocated for the DB instance\. For more information, see [Using Provisioned IOPS Storage with Multi\-AZ, Read Replicas, Snapshots, VPC, and DB Instance Classes](CHAP_Storage.md#Overview.ProvisionedIOPS-support)\. 

### AWS Management Console<a name="w3ab1c23c31c12b5"></a>

**To modify the Provisioned IOPS settings for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.
**Note**  
To filter the list of DB instances, for **Filter instances**, type a text string for Amazon RDS to use to filter the results\. Only DB instances whose names contain the string appear\.

1. Choose the DB instance with Provisioned IOPS storage that you want to modify\.

1. For **Instance actions**, choose **Modify**\.

1.  On the **Modify DB Instance** page, choose **Provisioned IOPS \(SSD\)** for **Storage type**, and then set the amount of Provisioned IOPS you want for **Provisioned IOPS**\.   
![\[Console Tags tab\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/piops1-mod.png)

   If the value you specify for either **Allocated storage** or **Provisioned IOPS** is outside the limits supported by the other parameter, a warning message is displayed indicating the range of values required for the other parameter\.

1. Choose **Continue**\.

1. To apply the changes to the DB instance immediately, select the **Apply immediately** check box in the **Scheduling of modifications** section\. Alternatively, you can choose **Apply during the next scheduled maintenance window**\. 

1. Review the parameters to be changed, and choose **Modify DB instance** to complete the modification\.

   The new value for allocated storage or for provisioned IOPS appears in the **Status** column\.  
![\[Pending Values column\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/piops3-mod.png)

### CLI<a name="w3ab1c23c31c12b7"></a>

To modify the Provisioned IOPS settings for a DB instance, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. Set the following parameters:
+ `--storage-type` – Set to `io1` for Provisioned IOPS\.
+ `--allocated-storage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `--iops` – The new amount of Provisioned IOPS for the DB instance, expressed in I/O operations per second\.
+ `--apply-immediately` – Use `--apply-immediately` to initiate conversion immediately\. Use `--no-apply-immediately` \(the default\) to apply the conversion during the next maintenance window\.

### API<a name="w3ab1c23c31c12b9"></a>

To modify the Provisioned IOPS settings for a DB instance, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html) action\. Set the following parameters:
+ `StorageType` – Set to `io1` for Provisioned IOPS\.
+ `AllocatedStorage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `Iops` – The new IOPS rate for the DB instance, expressed in I/O operations per second\.
+ `ApplyImmediately` – Set to `True` if you want to initiate conversion immediately\. If `False` \(the default\), the conversion is applied during the next maintenance window\.

## Creating a DB Instance That Uses Provisioned IOPS Storage<a name="USER_PIOPS.Creating"></a>

You can create a DB instance that uses Provisioned IOPS by setting several parameters when you launch the DB instance\. You can use the AWS Management Console, the Amazon RDS API, or the AWS Command Line Interface \(AWS CLI\)\. For more information about the settings you should use when creating a DB instance, see [Creating a DB Instance Running the MySQL Database Engine](USER_CreateInstance.md), [Creating a DB Instance Running the MariaDB Database Engine](USER_CreateMariaDBInstance.md), [Creating a DB Instance Running the Oracle Database Engine](USER_CreateOracleInstance.md), or [Creating a DB Instance Running the Microsoft SQL Server Database Engine](USER_CreateMicrosoftSQLServerInstance.md)\.

### AWS Management Console<a name="w3ab1c23c31c14b5"></a>

**To create a new DB instance that uses Provisioned IOPS storage**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. From the Amazon RDS console, choose **Instances**\.

1. Choose **Launch DB instance**\.

1.  In the Launch RDS DB Instance wizard, on the **Engine Selection** page, choose the DB engine that you want\. 

1. Choose **Next**\.

1. On the **Choose use case** page, choose either the **Production** or **Dev/Test** environment\.

1. Choose **Next**\.

1. On the **Specify DB details** page, choose **Provisioned IOPS \(SSD\)** for **Storage type**\. 

1. Specify values for **Allocated Storage** and **Provisioned IOPS**\. For information about the allowed ranges and ratios, see [Provisioned IOPS Storage](CHAP_Storage.md#USER_PIOPS)\.   
![\[piops2\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/piops2-new.png)

1. Choose **Next**\.

1. Add the details on the **Configure advanced settings** page\.

1. When the settings are as you want them, choose **Launch DB instance**\.

### CLI<a name="w3ab1c23c31c14b7"></a>

To create a new DB instance that uses Provisioned IOPS storage, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command\. Specify the required parameters and include values for the following parameters that apply to Provisioned IOPS storage:
+ `--storage-type` – Set to `io1` for Provisioned IOPS\.
+ `--allocated-storage` \- Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `--iops` \- The new IOPS rate for the DB instance, expressed in I/O operations per second\.

### API<a name="w3ab1c23c31c14b9"></a>

To create a new DB instance that uses Provisioned IOPS storage, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_CreateDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_CreateDBInstance.html) action\. Specify the required parameters and include values for the following parameters that apply to Provisioned IOPS storage:
+ `StorageType` – Set to `io1` for Provisioned IOPS\.
+ `AllocatedStorage` \- Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `Iops` \- The new IOPS rate for the DB instance, expressed in I/O operations per second\.

## Creating a MySQL or MariaDB Read Replica That Uses Provisioned IOPS Storage<a name="USER_PIOPS.CreatingRR"></a>

You can create a MySQL or MariaDB Read Replica that uses Provisioned IOPS storage\. You can create a Read Replica that uses Provisioned IOPS storage by using a source DB instance that uses either standard storage or Provisioned IOPS storage\.

### AWS Management Console<a name="w3ab1c23c31c16b5"></a>

For a complete description on how to create a Read Replica, see [Creating a Read Replica](USER_ReadRepl.md#USER_ReadRepl.Create)\.

**To create a Read Replica DB instance that uses Provisioned IOPS storage**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose ** Instances**\.

1. Choose the MySQL or MariaDB DB instance with Provisioned IOPS storage that you want to use as the source for the Read Replica, and choose **Instance actions**, **Create read replica**\.
**Important**  
The DB instance that you are creating a Read Replica for must have allocated storage within the range of storage for MySQL and MariaDB PIOPS \(100 GiB–16 TiB\)\. If the allocated storage for that DB instance is not within that range, then the **Provisioned IOPS** storage type isn't available as an option when creating the Read Replica\. Instead, you can set only the **GP2** or **Standard** storage types\. You can modify the allocated storage for the source DB instance to be within the range of storage for MySQL and MariaDB PIOPS before creating a Read Replica\. For more information on the PIOPS range of storage, see [Provisioned IOPS Storage](CHAP_Storage.md#USER_PIOPS)\. For information on modifying a MySQL DB instance, see [Modifying a DB Instance Running the MySQL Database Engine](USER_ModifyInstance.MySQL.md)\. For information on modifying a MariaDB DB instance, see [Modifying a DB Instance Running the MariaDB Database Engine](USER_ModifyInstance.MariaDB.md)\.

1. On the **Create read replica DB instance** page, type a DB instance identifier for the Read Replica\. 

1. Choose **Yes, Create read replica**\.

### CLI<a name="w3ab1c23c31c16b7"></a>

To create a Read Replica DB instance that uses Provisioned IOPS, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) command\. Specify the required parameters and include values for the following parameters that apply to Provisioned IOPS storage:
+ `--allocated-storage` \- Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `--iops` \- The new IOPS rate for the DB instance, expressed in I/O operations per second\.

### API<a name="w3ab1c23c31c16b9"></a>

To create a Read Replica DB instance that uses Provisioned IOPS, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_CreateDBInstanceReadReplica.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_CreateDBInstanceReadReplica.html) action\. Specify the required parameters and include values for the following parameters that apply to Provisioned IOPS storage:
+ `AllocatedStorage` \- Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `Iops` \- The new IOPS rate for the DB instance, expressed in I/O operations per second\.