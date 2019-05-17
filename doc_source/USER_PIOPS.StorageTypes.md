# Working with Storage for Amazon RDS DB Instances<a name="USER_PIOPS.StorageTypes"></a>

To specify how you want your data stored in Amazon RDS, you select a storage type and provide a storage size when you create or modify a DB instance\. Later, you can increase the amount or change the type of storage by modifying the DB instance\. For more information about which storage type to use for your workload, see [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage)\.

**Topics**
+ [Increasing DB Instance Storage Capacity](#USER_PIOPS.ModifyingExisting)
+ [Changing Your Storage Type](#USER_PIOPS.Modify)
+ [Modifying Provisioned IOPS SSD storage settings](#User_PIOPS.Increase)

## Increasing DB Instance Storage Capacity<a name="USER_PIOPS.ModifyingExisting"></a>

If you need space for additional data, you can scale up the storage of an existing DB instance\. To do so, you can use the Amazon RDS Management Console, the Amazon RDS API, or the AWS Command Line Interface \(AWS CLI\)\. If you are using General Purpose SSD or Provisioned IOPS SSD storage, you can increase your storage to a maximum of 16 TiB\. Scaling storage for Amazon RDS for SQL Server database instance, is supported only for General Purpose SSD or Provisioned IOPS SSD storage types\.  

We recommend that you create an Amazon CloudWatch alarm to monitor the amount of free storage for your DB instance so you can respond when necessary\. For more information on setting CloudWatch alarms, see [Using Amazon RDS Event Notification](USER_Events.md)\. 

In most cases, scaling storage doesn't require any outage and doesn't degrade performance of the server\. After you modify the storage size for a DB instance, the status of the DB instance is **Storage\-optimization**\. The DB instance is fully operational after a storage modification\. However, you can't make further storage modifications for either six \(6\) hours or while the DB instance status is **Storage\-optimization**, whichever is longer\. 

If you have a SQL Server DB instance and haven't modified the storage configuration since November 2017, you might experience a short outage of a few minutes when you modify your DB instance to increase the allocated storage\. After the outage, the DB instance is online but in the **Storage\-optimization** state\. Performance might be degraded during storage optimization\. 

**Note**  
You can't reduce the amount of storage for a DB instance after it has been allocated\.

### AWS Management Console<a name="USER_PIOPS.ModifyingExisting.console"></a>

**To increase storage for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to modify\.

1. Choose **Modify**\.

1. Enter a new value for **Allocated Storage**\. It must be greater than the current value\.   
![\[Modify the amount of storage for a DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/scale-gs2.png)
**Note**  
When you increase **Allocated Storage**, it must be by at least 10 percent\. If you try to increase by less than 10 percent, you see an error\.

1. Choose **Continue** to move to the next screen\.

1. To immediately start conversion of the DB instance to use the new storage type, choose **Apply immediately** in the **Scheduling of modifications** section\. Alternatively, you can choose **Apply during the next scheduled maintenance window**\. 

1. When the settings are as you want them, choose **Modify DB instance**\.

### CLI<a name="w4aac15c73b9c15b3"></a>

To increase the storage for a DB instance, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. Set the following parameters:
+ `--allocated-storage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `--apply-immediately` – Use `--apply-immediately` to initiate conversion immediately, or `--no-apply-immediately` \(the default\) to apply the conversion during the next maintenance window\. An immediate outage occurs when the conversion is applied\. For more information about storage, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.

### API<a name="w4aac15c73b9c15b5"></a>

To increase storage for a DB instance, use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html) action\. Set the following parameters:
+ `AllocatedStorage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `ApplyImmediately` – Set this option to `True` if you want to initiate conversion immediately\. If this option is `False` \(the default\), the scaling is applied during the next maintenance window\. An immediate outage occurs when the conversion is applied\.

   For more information about storage, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.

## Changing Your Storage Type<a name="USER_PIOPS.Modify"></a>

You can change the type of storage for your DB instance by using the AWS Management Console, the Amazon RDS API, or the AWS Command Line Interface \(AWS CLI\)\. 

When you convert from one storage type to another, you might experience an outage at the beginning of the conversion process\. To learn the factors that might cause an outage in this case, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. The duration of the migration depends on several factors such as database load, storage size, storage type, and amount of IOPS provisioned \(if any\)\. The typical migration time is a few minutes\. The DB instance is available for use during the migration\.

However, when you are migrating to or from magnetic storage, the migration time can take up to several days in some cases\. During the migration to or from magnetic storage, the DB instance is available for use, but might experience performance degradation\. 

Storage conversions from Provisioned IOPS SSD or magnetic storage to General Purpose SSD storage can potentially deplete the I/O credits allocated for General Purpose SSD storage\. This is especially true on smaller volumes\. After the initial I/O burst credits for the volume are depleted, the remaining data is converted at the base performance rate of 3 IOPS per GiB of allocated General Purpose SSD storage\. This approach can result in significantly longer conversion times\. 

### AWS Management Console<a name="USER_PIOPS.Modify.con"></a>

**To change the storage type for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.
**Note**  
To filter the list of DB instances, for **Filter databases** enter a text string for Amazon RDS to use to filter the results\. Only DB instances whose names contain the string appear\.

1. Choose the DB instance that you want to modify\.

1. Choose **Modify**\.

1. On the **Modify DB Instance page**, choose the type of storage from the **Storage type** list\. If you are modifying your DB instance to use Provisioned IOPS SSD storage type, then also provide a Provisioned IOPS value\.   
![\[Console Tags tab\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/piops1-mod.png)

1. Choose **Continue**\.

1. To apply the changes to the DB instance immediately, choose **Apply immediately** in the **Scheduling of modifications** section\. Alternatively, you can choose **Apply during the next scheduled maintenance window**\.

   An immediate outage occurs when the storage type changes\. For more information about storage, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.

1. Review the parameters to be changed, and choose **Modify DB instance** to complete the modification\.

### CLI<a name="w4aac15c73c11c11b3"></a>

To change the type of storage for a DB instance, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. Set the following parameters:
+ `--storage-type` – Set to `io1` for Provisioned IOPS\.
+ `--apply-immediately` – Use `--apply-immediately` to initiate conversion immediately\. Use `--no-apply-immediately` \(the default\) to apply the conversion during the next maintenance window\.

### API<a name="w4aac15c73c11c11b5"></a>

To change the type of storage for a DB instance, use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html) action\. Set the following parameters:
+ `StorageType` – Set to `io1` for Provisioned IOPS\.
+ `ApplyImmediately` – Set this option to `True` if you want to initiate conversion immediately\. If this option is `False` \(the default\), the conversion is applied during the next maintenance window\.

## Modifying Provisioned IOPS SSD storage settings<a name="User_PIOPS.Increase"></a>

You can modify the settings for a DB instance that uses Provisioned IOPS SSD Storage by using the AWS Management Console, the Amazon RDS API, or the AWS CLI\. Specify the storage type, allocated storage, and the amount of Provisioned IOPS that you require\. Choose the amount of provisioned IOPS and storage depending on your database engine and instance type\. For more information, see [Provisioned IOPS SSD Storage](CHAP_Storage.md#USER_PIOPS)\. 

Although you can reduce the amount of IOPS provisioned for your instance, you can't reduce the amount of General Purpose SSD or magnetic storage allocated\. 

### AWS Management Console<a name="User_PIOPS.Increase.con"></a>

**To change the Provisioned IOPS settings for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.
**Note**  
To filter the list of DB instances, for **Filter databases** enter a text string for Amazon RDS to use to filter the results\. Only DB instances whose names contain the string appear\.

1. Choose the DB instance with Provisioned IOPS that you want to modify\.

1. Choose **Modify**\.

1. On the **Modify DB Instance page**, choose Provisioned IOPS for **Storage type** and then provide a Provisioned IOPS value\.   
![\[Console Tags tab\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/piops2-new.png)

   If the value you specify for either ** Allocated storage** or **Provisioned IOPS** is outside the limits supported by the other parameter, a warning message is displayed\. This message gives the range of values required for the other parameter\. 

1. Choose **Continue**\.

1. To apply the changes to the DB instance immediately, choose **Apply immediately** in the **Scheduling of modifications** section\. Alternatively, you can choose **Apply during the next scheduled maintenance window**\.

   An immediate outage occurs when the storage type changes\. For more information about storage, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.

1. Review the parameters to be changed, and choose **Modify DB instance** to complete the modification\.

   The new value for allocated storage or for Provisioned IOPS appears in the **Status** column\. 

### CLI<a name="w4aac15c73c13b7b3"></a>

To change the Provisioned IOPS setting for a DB instance, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. Set the following parameters:
+ `--storage-type` – Set to `io1` for Provisioned IOPS\.
+ `--allocated-storage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `--iops` – The new amount of Provisioned IOPS for the DB instance, expressed in I/O operations per second\.
+ `--apply-immediately` – Use `--apply-immediately` to initiate conversion immediately\. Use `--no-apply-immediately` \(the default\) to apply the conversion during the next maintenance window\.

### API<a name="w4aac15c73c13b7b5"></a>

To change the Provisioned IOPS settings for a DB instance, use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html) action\. Set the following parameters:
+ `StorageType` – Set to `io1` for Provisioned IOPS\.
+ `AllocatedStorage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `Iops` – The new IOPS rate for the DB instance, expressed in I/O operations per second\.
+ `ApplyImmediately` – Set this option to `True` if you want to initiate conversion immediately\. If this option is `False` \(the default\), the modification is applied during the next maintenance window\.