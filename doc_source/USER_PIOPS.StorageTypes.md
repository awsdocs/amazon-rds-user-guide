# Working with Storage for Amazon RDS DB Instances<a name="USER_PIOPS.StorageTypes"></a>

To specify how you want your data stored in Amazon RDS, choose a storage type and provide a storage size when you create or modify a DB instance\. Later, you can increase the amount or change the type of storage by modifying the DB instance\. For more information about which storage type to use for your workload, see [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage)\.

**Topics**
+ [Increasing DB Instance Storage Capacity](#USER_PIOPS.ModifyingExisting)
+ [Managing Capacity Automatically with Amazon RDS Storage Autoscaling](#USER_PIOPS.Autoscaling)
+ [Modifying SSD Storage Settings for Provisioned IOPS](#User_PIOPS.Increase)

## Increasing DB Instance Storage Capacity<a name="USER_PIOPS.ModifyingExisting"></a>

If you need space for additional data, you can scale up the storage of an existing DB instance\. To do so, you can use the Amazon RDS Management Console, the Amazon RDS API, or the AWS Command Line Interface \(AWS CLI\)\. If you are using General Purpose SSD or Provisioned IOPS SSD storage, you can increase your storage to a maximum of 16 TiB\. Scaling storage for Amazon RDS for Microsoft SQL Server DB instances is supported only for General Purpose SSD or Provisioned IOPS SSD storage types\.  

To monitor the amount of free storage for your DB instance so you can respond when necessary, we recommend that you create an Amazon CloudWatch alarm\. For more information on setting CloudWatch alarms, see [Using Amazon RDS Event Notification](USER_Events.md)\. 

In most cases, scaling storage doesn't require any outage and doesn't degrade performance of the server\. After you modify the storage size for a DB instance, the status of the DB instance is **Storage\-optimization**\. The DB instance is fully operational after a storage modification\. However, you can't make further storage modifications either for six hours or while the DB instance status is `storage-optimization`, whichever is longer\. 

However, a special case is if you have a SQL Server DB instance and haven't modified the storage configuration since November 2017\. In this case, you might experience a short outage of a few minutes when you modify your DB instance to increase the allocated storage\. After the outage, the DB instance is online but in the `Storage-optimization` state\. Performance might be degraded during storage optimization\. 

**Note**  
You can't reduce the amount of storage for a DB instance after storage has been allocated\.

### AWS Management Console<a name="USER_PIOPS.ModifyingExisting.console"></a>

**To increase storage for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to modify\.

1. Choose **Modify**\.

1. Enter a new value for **Allocated Storage**\. It must be greater than the current value\.   
![\[Modify the amount of storage for a DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/scale-gs2.png)
**Note**  
When you increase **Allocated Storage**, it must be by at least 10 percent\. If you try to increase the value by less than 10 percent, you see an error\.

1. Choose **Continue** to move to the next screen\.

1. Choose **Apply immediately** in the **Scheduling of modifications** section to apply the storage changes to the DB instance immediately\. Or choose **Apply during the next scheduled maintenance window** to apply the changes during the next maintenance window\.

1. When the settings are as you want them, choose **Modify DB instance**\.

### CLI<a name="USER_PIOPS.ModifyingExisting.cli"></a>

To increase the storage for a DB instance, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)\. Set the following parameters:
+ `--allocated-storage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `--apply-immediately` – Use `--apply-immediately` to change to the new storage type immediately\. Or use `--no-apply-immediately` \(the default\) to apply storage changes during the next maintenance window\. An immediate outage occurs when the changes are applied\.

For more information about storage, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.

### RDS API<a name="USER_PIOPS.ModifyingExisting.api"></a>

To increase storage for a DB instance, use the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html)\. Set the following parameters:
+ `AllocatedStorage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `ApplyImmediately` – Set this option to `True` to apply scaling changes immediately\. Set this option to `False` \(the default\) to apply scaling changes during the next maintenance window\. An immediate outage occurs when the changes are applied\.

For more information about storage, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.

## Managing Capacity Automatically with Amazon RDS Storage Autoscaling<a name="USER_PIOPS.Autoscaling"></a>

If your workload is cyclical or unpredictable, you can enable storage autoscaling for an Amazon RDS DB instance\. To do so, you can use the Amazon RDS console, the Amazon RDS API, or the AWS CLI\. 

For example, you might use this feature for a new mobile gaming application that users are adopting rapidly\. In this case, a rapidly increasing workload might exceed the available database storage\. To avoid having to manually scale up database storage, you can use Amazon RDS storage autoscaling\. 

With storage autoscaling enabled, when RDS detects that you are running out of free database space it automatically scales up your storage\. RDS starts a storage modification for an autoscale\-enabled DB instance when these factors apply:
+ Free available space is less than 10 percent of the allocated storage\.
+ The low\-storage condition lasts at least five minutes\. 

Currently, the additional storage is at least 5 GiB in size and at most 10 percent of the currently allocated storage\. You can't modify the allocated storage to a value greater than the maximum allocated storage for autoscale\-enabled instances\.

If you start a storage scaling operation at the same time that Amazon RDS starts an autoscale operation, your storage modification takes precedence\. In this case, the autoscale operation is canceled\. 

 Although automatic scaling helps you to increase storage on your RDS instance dynamically, you should still configure the initial storage for your DB instance to an appropriate size for your typical workload\. 

### Enabling Storage Autoscaling for a New DB Instance<a name="USER_PIOPS.EnablingAutoscaling"></a>

When you create a new Amazon RDS DB instance, you can choose whether to enable storage autoscaling\. You can also set an upper limit on the storage that Amazon RDS can allocate for the DB instance\. 

**Note**  
 When you clone an Amazon RDS DB instance that has storage autoscaling enabled, that setting isn't automatically inherited by the cloned instance\. The new DB instance has the same amount of allocated storage as the original instance\. You can turn storage autoscaling on again for the new instance if the cloned instance continues to increase its storage requirements\. 

#### AWS Management Console<a name="USER_PIOPS.EnablingAutoscaling.console"></a>

**To enable storage autoscaling for a new DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the upper\-right corner of the Amazon RDS console, choose the AWS Region where you want to create the DB instance\. 

1.  In the navigation pane, choose **Databases**\. 

1.  Choose **Create database**\. On the **Select engine** page, choose your database engine and specify your DB instance information as described in [Getting Started with Amazon RDS](CHAP_GettingStarted.md)\. 

1. In the **Storage Autoscaling** section, set the **Maximum Storage Limit** value for the DB instance\. 

1. Specify the rest of your DB instance information as described in [Getting Started with Amazon RDS](CHAP_GettingStarted.md)\. 

#### AWS CLI<a name="USER_PIOPS.EnablingAutoscaling.cli"></a>

To enable storage autoscaling for a new DB instance, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)\. Set the following parameter: 
+  `--max-allocated-storage` – Turns on storage autoscaling and sets the upper limit on storage size, in gibibytes\. 

 To verify that Amazon RDS storage autoscaling is available for your DB instance, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-valid-db-instance-modifications.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-valid-db-instance-modifications.html) command\. To check based on the instance class before creating an instance, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-orderable-db-instance-options.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-orderable-db-instance-options.html) command\. Check the following field in the return value: 
+  `SupportsStorageAutoscaling` – Indicates whether the DB instance or instance class supports storage autoscaling\. 

For more information about storage, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.

#### RDS API<a name="USER_PIOPS.EnablingAutoscaling.api"></a>

To enable storage autoscaling for a new DB instance, use the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_CreateDBInstance.html)\. Set the following parameter: 
+  `MaxAllocatedStorage` – Turns on Amazon RDS storage autoscaling and sets the upper limit on storage size, in gibibytes\. 

 To verify that Amazon RDS storage autoscaling is available for your DB instance, use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_DescribeValidDbInstanceModifications.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_DescribeValidDbInstanceModifications.html) operation for an existing instance, or the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_DescribeOrderableDBInstanceOptions.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_DescribeOrderableDBInstanceOptions.html) operation before creating an instance\. Check the following field in the return value: 
+  `SupportsStorageAutoscaling` – Indicates whether the DB instance supports storage autoscaling\. 

For more information about storage, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.

### Changing the Storage Autoscaling Settings for a DB Instance<a name="USER_PIOPS.ModifyingAutoscaling"></a>

You can turn storage autoscaling on for an existing Amazon RDS DB instance\. You can also change the upper limit on the storage that RDS can allocate for the DB instance\. 

#### AWS Management Console<a name="USER_PIOPS.ModifyingAutoscaling.console"></a>

**To change the storage autoscaling settings for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to modify, and choose **Modify**\. The **Modify DB instance** page appears\. 

1.  Change the storage limit in the **Autoscaling** section\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

1.  When all the changes are as you want them, choose **Continue** and check your modifications\. 

1. Choose **Apply immediately** to apply the changes immediately\. Choose **Apply during the next scheduled maintenance window** to apply the changes during the next maintenance window\. Choosing **Apply immediately** can cause an outage in some cases\. For more information, see [Using the Apply Immediately Parameter](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\.

1.  On the confirmation page, review your changes\. If they're correct, choose **Modify DB Instance** to save your changes\. If they aren't correct, choose **Back** to edit your changes or **Cancel** to cancel your changes\. 

#### AWS CLI<a name="USER_PIOPS.ModifyingAutoscaling.cli"></a>

To change the storage autoscaling settings for a DB instance, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)\. Set the following parameter: 
+  `--max-allocated-storage` – Sets the upper limit on storage size, in gibibytes\. If the value is greater than the `--allocated-storage` parameter, storage autoscaling is turned on\. If the value is the same as the `--allocated-storage` parameter, storage autoscaling is turned off\. 

 To verify that Amazon RDS storage autoscaling is available for your DB instance, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-valid-db-instance-modifications.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-valid-db-instance-modifications.html) command\. To check based on the instance class before creating an instance, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-orderable-db-instance-options.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-orderable-db-instance-options.html) command\. Check the following field in the return value: 
+  `SupportsStorageAutoscaling` – Indicates whether the DB instance supports storage autoscaling\. 

For more information about storage, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.

#### RDS API<a name="USER_PIOPS.ModifyingAutoscaling.api"></a>

 To change the storage autoscaling settings for a DB instance, use the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html)\. Set the following parameter: 
+  `MaxAllocatedStorage` – Sets the upper limit on storage size, in gibibytes\. 

 To verify that Amazon RDS storage autoscaling is available for your DB instance, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_DescribeValidDbInstanceModifications.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_DescribeValidDbInstanceModifications.html) operation for an existing instance, or the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_DescribeOrderableDBInstanceOptions.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_DescribeOrderableDBInstanceOptions.html) operation before creating an instance\. Check the following field in the return value: 
+  `SupportsStorageAutoscaling` – Indicates whether the DB instance supports storage autoscaling\. 

For more information about storage, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.

### Turning Off Storage Autoscaling for a DB Instance<a name="USER_PIOPS.DisablingAutoscaling"></a>

 If you no longer need Amazon RDS to automatically increase the storage for an RDS DB instance, you can turn off storage autoscaling\. After you do, you can still manually increase the amount of storage for your DB instance\. 

#### AWS Management Console<a name="USER_PIOPS.DisablingAutoscaling.console"></a>

**To turn off storage autoscaling for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to modify and choose **Modify**\. The **Modify DB instance** page appears\. 

1.  Set the storage limit in the autoscaling section to equal the provisioned storage\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

1.  When all the changes are as you want them, choose **Continue** and check the modifications\. 

1. Choose **Apply immediately** to apply the changes immediately\. Choose **Apply during the next scheduled maintenance window** to apply the changes during the next maintenance window\. Choosing **Apply immediately** can cause an outage in some cases; for more information, see [Using the Apply Immediately Parameter](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\. 

1.  On the confirmation page, review your changes\. If they're correct, choose **Modify DB Instance** to save your changes\. If they aren't correct, choose **Back** to edit your changes or **Cancel** to cancel your changes\. 

#### AWS CLI<a name="USER_PIOPS.DisablingAutoscaling.cli"></a>

 To turn off storage autoscaling for a DB instance, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and the following parameter: 
+  `--max-allocated-storage` – Specify a value equal to the `--allocated-storage` setting to prevent further Amazon RDS storage autoscaling for the specified DB instance\. 

For more information about storage, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.

#### RDS API<a name="USER_PIOPS.DisablingAutoscaling.api"></a>

 To turn off storage autoscaling for a DB instance, use the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_ModifyDBInstance.html)\. Set the following parameter:
+  `MaxAllocatedStorage` – Specify a value equal to the `AllocatedStorage` setting to prevent further Amazon RDS storage autoscaling for the specified DB instance\. 

For more information about storage, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.

## Modifying SSD Storage Settings for Provisioned IOPS<a name="User_PIOPS.Increase"></a>

You can modify the settings for a DB instance that uses Provisioned IOPS SSD storage by using the Amazon RDS console, AWS CLI, or Amazon RDS API\. Specify the storage type, allocated storage, and the amount of Provisioned IOPS that you require\. You can choose from a range between 1,000 IOPS and 100 GiB of storage up to 80,000 IOPS and 64 TiB \(64,000 GiB\) of storage\. The range depends on your database engine and instance type\. 

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

   If the value you specify for either** Allocated storage** or **Provisioned IOPS** is outside the limits supported by the other parameter, a warning message is displayed\. This message gives the range of values required for the other parameter\. 

1. Choose **Continue**\.

1. To apply the changes to the DB instance immediately, choose **Apply immediately** in the **Scheduling of modifications** section\. Or choose **Apply during the next scheduled maintenance window** to apply the changes during the next maintenance window\.

   An immediate outage occurs when the storage type changes\. For more information about storage, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.

1. Review the parameters to be changed, and choose **Modify DB instance** to complete the modification\.

   The new value for allocated storage or for Provisioned IOPS appears in the **Status** column\. 

### AWS CLI<a name="User_PIOPS.Increase.cli"></a>

To change the Provisioned IOPS setting for a DB instance, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)\. Set the following parameters:
+ `--storage-type` – Set to `io1` for Provisioned IOPS\.
+ `--allocated-storage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `--iops` – The new amount of Provisioned IOPS for the DB instance, expressed in I/O operations per second\.
+ `--apply-immediately` – Use `--apply-immediately` to apply changes immediately\. Use `--no-apply-immediately` \(the default\) to apply changes during the next maintenance window\.

### RDS API<a name="User_PIOPS.Increase.api"></a>

To change the Provisioned IOPS settings for a DB instance, use the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html)\. Set the following parameters:
+ `StorageType` – Set to `io1` for Provisioned IOPS\.
+ `AllocatedStorage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `Iops` – The new IOPS rate for the DB instance, expressed in I/O operations per second\.
+ `ApplyImmediately` – Set this option to `True` to apply changes immediately\. Set this option to `False` \(the default\) to apply changes during the next maintenance window\.