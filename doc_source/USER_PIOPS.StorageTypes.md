# Working with storage for Amazon RDS DB instances<a name="USER_PIOPS.StorageTypes"></a>

To specify how you want your data stored in Amazon RDS, choose a storage type and provide a storage size when you create or modify a DB instance\. Later, you can increase the amount or change the type of storage by modifying the DB instance\. For more information about which storage type to use for your workload, see [Amazon RDS storage types](CHAP_Storage.md#Concepts.Storage)\.

**Topics**
+ [Increasing DB instance storage capacity](#USER_PIOPS.ModifyingExisting)
+ [Managing capacity automatically with Amazon RDS storage autoscaling](#USER_PIOPS.Autoscaling)
+ [Modifying settings for Provisioned IOPS SSD storage](#User_PIOPS.Increase)
+ [I/O\-intensive storage modifications](#USER_PIOPS.IOIntensive)
+ [Modifying settings for General Purpose SSD \(gp3\) storage](#USER_PIOPS.gp3)

## Increasing DB instance storage capacity<a name="USER_PIOPS.ModifyingExisting"></a>

If you need space for additional data, you can scale up the storage of an existing DB instance\. To do so, you can use the Amazon RDS Management Console, the Amazon RDS API, or the AWS Command Line Interface \(AWS CLI\)\. For information about storage limits, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.

**Note**  
Scaling storage for Amazon RDS for Microsoft SQL Server DB instances is supported only for General Purpose SSD or Provisioned IOPS SSD storage types\.

To monitor the amount of free storage for your DB instance so you can respond when necessary, we recommend that you create an Amazon CloudWatch alarm\. For more information on setting CloudWatch alarms, see [Using CloudWatch alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/AlarmThatSendsEmail.html)\.

Scaling storage usually doesn't cause any outage or performance degradation of the DB instance\. After you modify the storage size for a DB instance, the status of the DB instance is **storage\-optimization**\.

**Note**  
Storage optimization can take several hours\. You can't make further storage modifications for either six \(6\) hours or until storage optimization has completed on the instance, whichever is longer\.

However, a special case is if you have a SQL Server DB instance and haven't modified the storage configuration since November 2017\. In this case, you might experience a short outage of a few minutes when you modify your DB instance to increase the allocated storage\. After the outage, the DB instance is online but in the `storage-optimization` state\. Performance might be degraded during storage optimization\. 

**Note**  
You can't reduce the amount of storage for a DB instance after storage has been allocated\. When you increase the allocated storage, it must be by at least 10 percent\. If you try to increase the value by less than 10 percent, you get an error\.

### Console<a name="USER_PIOPS.ModifyingExisting.console"></a>

**To increase storage for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to modify\.

1. Choose **Modify**\.

1. Enter a new value for **Allocated storage**\. It must be greater than the current value\.   
![\[Modify the amount of storage for a DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/scale-gs2.png)

1. Choose **Continue** to move to the next screen\.

1. Choose **Apply immediately** in the **Scheduling of modifications** section to apply the storage changes to the DB instance immediately\.

   Or choose **Apply during the next scheduled maintenance window** to apply the changes during the next maintenance window\.

1. When the settings are as you want them, choose **Modify DB instance**\.

### AWS CLI<a name="USER_PIOPS.ModifyingExisting.cli"></a>

To increase the storage for a DB instance, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)\. Set the following parameters:
+ `--allocated-storage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `--apply-immediately` – Use `--apply-immediately` to apply the storage changes immediately\.

  Or use `--no-apply-immediately` \(the default\) to apply the changes during the next maintenance window\. An immediate outage occurs when the changes are applied\.

For more information about storage, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.

### RDS API<a name="USER_PIOPS.ModifyingExisting.api"></a>

To increase storage for a DB instance, use the Amazon RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html)\. Set the following parameters:
+ `AllocatedStorage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `ApplyImmediately` – Set this option to `True` to apply the storage changes immediately\. Set this option to `False` \(the default\) to apply the changes during the next maintenance window\. An immediate outage occurs when the changes are applied\.

For more information about storage, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.

## Managing capacity automatically with Amazon RDS storage autoscaling<a name="USER_PIOPS.Autoscaling"></a>

If your workload is unpredictable, you can enable storage autoscaling for an Amazon RDS DB instance\. To do so, you can use the Amazon RDS console, the Amazon RDS API, or the AWS CLI\. 

For example, you might use this feature for a new mobile gaming application that users are adopting rapidly\. In this case, a rapidly increasing workload might exceed the available database storage\. To avoid having to manually scale up database storage, you can use Amazon RDS storage autoscaling\. 

With storage autoscaling enabled, when Amazon RDS detects that you are running out of free database space it automatically scales up your storage\. Amazon RDS starts a storage modification for an autoscaling\-enabled DB instance when these factors apply:
+ Free available space is less than 10 percent of the allocated storage\.
+ The low\-storage condition lasts at least five minutes\.
+ At least six hours have passed since the last storage modification, or storage optimization has completed on the instance, whichever is longer\.

The additional storage is in increments of whichever of the following is greater:
+ 5 GiB
+ 10 percent of currently allocated storage
+ Storage growth prediction for 7 hours based on the `FreeStorageSpace` metrics change in the past hour\. For more information on metrics, see [Monitoring with Amazon CloudWatch](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MonitoringOverview.html#monitoring-cloudwatch)\.

The maximum storage threshold is the limit that you set for autoscaling the DB instance\. It has the following constraints:
+ You must set the maximum storage threshold to at least 10% more than the current allocated storage\. We recommend setting it to at least 26% more to avoid receiving an [event notification](USER_Events.Messages.md#RDS-EVENT-0225) that the storage size is approaching the maximum storage threshold\.

  For example, if you have DB instance with 1000 GiB of allocated storage, then set the maximum storage threshold to at least 1100 GiB\. If you don't, you get an error such as Invalid max storage size for *engine\_name*\. However, we recommend that you set the maximum storage threshold to at least 1260 GiB to avoid the event notification\.
+ For a DB instance that uses Provisioned IOPS storage, the ratio of IOPS to maximum storage threshold \(in GiB\) must be from 1–50 on RDS for SQL Server, and 0\.5–50 on other RDS DB engines\.
+ You can't set the maximum storage threshold for autoscaling\-enabled instances to a value greater than the maximum allocated storage for the database engine and DB instance class\.

  For example, SQL Server Standard Edition on db\.m5\.xlarge has a default allocated storage for the instance of 20 GiB \(the minimum\) and a maximum allocated storage of 16,384 GiB\. The default maximum storage threshold for autoscaling is 1,000 GiB\. If you use this default, the instance doesn't autoscale above 1,000 GiB\. This is true even though the maximum allocated storage for the instance is 16,384 GiB\.

**Note**  
We recommend that you carefully choose the maximum storage threshold based on usage patterns and customer needs\. If there are any aberrations in the usage patterns, the maximum storage threshold can prevent scaling storage to an unexpectedly high value when autoscaling predicts a very high threshold\. After a DB instance has been autoscaled, its allocated storage can't be reduced\.

**Topics**
+ [Limitations](#autoscaling-limitations)
+ [Enabling storage autoscaling for a new DB instance](#USER_PIOPS.EnablingAutoscaling)
+ [Changing the storage autoscaling settings for a DB instance](#USER_PIOPS.ModifyingAutoscaling)
+ [Turning off storage autoscaling for a DB instance](#USER_PIOPS.DisablingAutoscaling)

### Limitations<a name="autoscaling-limitations"></a>

The following limitations apply to storage autoscaling:
+ Autoscaling doesn't occur if the maximum storage threshold would be equaled or exceeded by the storage increment\.
+ When autoscaling, RDS predicts the storage size for subsequent autoscaling operations\. If a subsequent operation is predicted to exceed the maximum storage threshold, then RDS autoscales to the maximum storage threshold\.
+ Autoscaling can't completely prevent storage\-full situations for large data loads\. This is because further storage modifications can't be made for either six \(6\) hours or until storage optimization has completed on the instance, whichever is longer\.

   If you perform a large data load, and autoscaling doesn't provide enough space, the database might remain in the storage\-full state for several hours\. This can harm the database\.
+ If you start a storage scaling operation at the same time that Amazon RDS starts an autoscaling operation, your storage modification takes precedence\. The autoscaling operation is canceled\.
+ Autoscaling can't be used with magnetic storage\.
+ Autoscaling can't be used with the following previous\-generation instance classes that have less than 6 TiB of orderable storage: db\.m3\.large, db\.m3\.xlarge, and db\.m3\.2xlarge\.
+ Autoscaling operations aren't logged by AWS CloudTrail\. For more information on CloudTrail, see [Monitoring Amazon RDS API calls in AWS CloudTrail](logging-using-cloudtrail.md)\.

Although automatic scaling helps you to increase storage on your Amazon RDS DB instance dynamically, you should still configure the initial storage for your DB instance to an appropriate size for your typical workload\.

### Enabling storage autoscaling for a new DB instance<a name="USER_PIOPS.EnablingAutoscaling"></a>

When you create a new Amazon RDS DB instance, you can choose whether to enable storage autoscaling\. You can also set an upper limit on the storage that Amazon RDS can allocate for the DB instance\.

**Note**  
When you clone an Amazon RDS DB instance that has storage autoscaling enabled, that setting isn't automatically inherited by the cloned instance\. The new DB instance has the same amount of allocated storage as the original instance\. You can turn storage autoscaling on again for the new instance if the cloned instance continues to increase its storage requirements\.

#### Console<a name="USER_PIOPS.EnablingAutoscaling.console"></a>

**To enable storage autoscaling for a new DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the upper\-right corner of the Amazon RDS console, choose the AWS Region where you want to create the DB instance\. 

1.  In the navigation pane, choose **Databases**\. 

1.  Choose **Create database**\. On the **Select engine** page, choose your database engine and specify your DB instance information as described in [Getting started with Amazon RDS](CHAP_GettingStarted.md)\. 

1. In the **Storage autoscaling** section, set the **Maximum storage threshold** value for the DB instance\. 

1. Specify the rest of your DB instance information as described in [Getting started with Amazon RDS](CHAP_GettingStarted.md)\.

#### AWS CLI<a name="USER_PIOPS.EnablingAutoscaling.cli"></a>

To enable storage autoscaling for a new DB instance, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)\. Set the following parameter:
+  `--max-allocated-storage` – Turns on storage autoscaling and sets the upper limit on storage size, in gibibytes\.

 To verify that Amazon RDS storage autoscaling is available for your DB instance, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-valid-db-instance-modifications.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-valid-db-instance-modifications.html) command\. To check based on the instance class before creating an instance, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-orderable-db-instance-options.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-orderable-db-instance-options.html) command\. Check the following field in the return value: 
+  `SupportsStorageAutoscaling` – Indicates whether the DB instance or instance class supports storage autoscaling\. 

For more information about storage, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.

#### RDS API<a name="USER_PIOPS.EnablingAutoscaling.api"></a>

To enable storage autoscaling for a new DB instance, use the Amazon RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html)\. Set the following parameter:
+  `MaxAllocatedStorage` – Turns on Amazon RDS storage autoscaling and sets the upper limit on storage size, in gibibytes\. 

 To verify that Amazon RDS storage autoscaling is available for your DB instance, use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeValidDbInstanceModifications.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeValidDbInstanceModifications.html) operation for an existing instance, or the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeOrderableDBInstanceOptions.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeOrderableDBInstanceOptions.html) operation before creating an instance\. Check the following field in the return value: 
+  `SupportsStorageAutoscaling` – Indicates whether the DB instance supports storage autoscaling\. 

For more information about storage, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.

### Changing the storage autoscaling settings for a DB instance<a name="USER_PIOPS.ModifyingAutoscaling"></a>

You can turn storage autoscaling on for an existing Amazon RDS DB instance\. You can also change the upper limit on the storage that Amazon RDS can allocate for the DB instance\. 

#### Console<a name="USER_PIOPS.ModifyingAutoscaling.console"></a>

**To change the storage autoscaling settings for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to modify, and choose **Modify**\. The **Modify DB instance** page appears\. 

1.  Change the storage limit in the **Autoscaling** section\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

1.  When all the changes are as you want them, choose **Continue** and check your modifications\. 

1.  On the confirmation page, review your changes\. If they're correct, choose **Modify DB Instance** to save your changes\. If they aren't correct, choose **Back** to edit your changes or **Cancel** to cancel your changes\.

   Changing the storage autoscaling limit occurs immediately\. This setting ignores the **Apply immediately** setting\.

#### AWS CLI<a name="USER_PIOPS.ModifyingAutoscaling.cli"></a>

To change the storage autoscaling settings for a DB instance, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)\. Set the following parameter: 
+  `--max-allocated-storage` – Sets the upper limit on storage size, in gibibytes\. If the value is greater than the `--allocated-storage` parameter, storage autoscaling is turned on\. If the value is the same as the `--allocated-storage` parameter, storage autoscaling is turned off\. 

 To verify that Amazon RDS storage autoscaling is available for your DB instance, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-valid-db-instance-modifications.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-valid-db-instance-modifications.html) command\. To check based on the instance class before creating an instance, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-orderable-db-instance-options.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-orderable-db-instance-options.html) command\. Check the following field in the return value: 
+  `SupportsStorageAutoscaling` – Indicates whether the DB instance supports storage autoscaling\. 

For more information about storage, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.

#### RDS API<a name="USER_PIOPS.ModifyingAutoscaling.api"></a>

 To change the storage autoscaling settings for a DB instance, use the Amazon RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html)\. Set the following parameter: 
+  `MaxAllocatedStorage` – Sets the upper limit on storage size, in gibibytes\. 

 To verify that Amazon RDS storage autoscaling is available for your DB instance, use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeValidDbInstanceModifications.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeValidDbInstanceModifications.html) operation for an existing instance, or the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeOrderableDBInstanceOptions.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeOrderableDBInstanceOptions.html) operation before creating an instance\. Check the following field in the return value: 
+  `SupportsStorageAutoscaling` – Indicates whether the DB instance supports storage autoscaling\. 

For more information about storage, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.

### Turning off storage autoscaling for a DB instance<a name="USER_PIOPS.DisablingAutoscaling"></a>

 If you no longer need Amazon RDS to automatically increase the storage for an Amazon RDS DB instance, you can turn off storage autoscaling\. After you do, you can still manually increase the amount of storage for your DB instance\. 

#### Console<a name="USER_PIOPS.DisablingAutoscaling.console"></a>

**To turn off storage autoscaling for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to modify and choose **Modify**\. The **Modify DB instance** page appears\. 

1.  Clear the **Enable storage autoscaling** check box in the **Storage autoscaling** section\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

1.  When all the changes are as you want them, choose **Continue** and check the modifications\.

1. On the confirmation page, review your changes\. If they're correct, choose **Modify DB Instance** to save your changes\. If they aren't correct, choose **Back** to edit your changes or **Cancel** to cancel your changes\.

Changing the storage autoscaling limit occurs immediately\. This setting ignores the **Apply immediately** setting\.

#### AWS CLI<a name="USER_PIOPS.DisablingAutoscaling.cli"></a>

 To turn off storage autoscaling for a DB instance, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) and the following parameter: 
+  `--max-allocated-storage` – Specify a value equal to the `--allocated-storage` setting to prevent further Amazon RDS storage autoscaling for the specified DB instance\. 

For more information about storage, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.

#### RDS API<a name="USER_PIOPS.DisablingAutoscaling.api"></a>

 To turn off storage autoscaling for a DB instance, use the Amazon RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html)\. Set the following parameter:
+  `MaxAllocatedStorage` – Specify a value equal to the `AllocatedStorage` setting to prevent further Amazon RDS storage autoscaling for the specified DB instance\. 

For more information about storage, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.

## Modifying settings for Provisioned IOPS SSD storage<a name="User_PIOPS.Increase"></a>

You can modify the settings for a DB instance that uses Provisioned IOPS SSD storage by using the Amazon RDS console, AWS CLI, or Amazon RDS API\. Specify the storage type, allocated storage, and the amount of Provisioned IOPS that you require\. The range depends on your database engine and instance type\.

Although you can reduce the amount of IOPS provisioned for your instance, you can't reduce the storage size\.

In most cases, scaling storage doesn't require any outage and doesn't degrade performance of the server\. After you modify the storage IOPS for a DB instance, the status of the DB instance is **storage\-optimization**\.

**Note**  
Storage optimization can take several hours\. You can't make further storage modifications for either six \(6\) hours or until storage optimization has completed on the instance, whichever is longer\.

For information on the ranges of allocated storage and Provisioned IOPS available for each database engine, see [Provisioned IOPS SSD storage](CHAP_Storage.md#USER_PIOPS)\.

### Console<a name="User_PIOPS.Increase.con"></a>

**To change the Provisioned IOPS settings for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

   To filter the list of DB instances, for **Filter databases** enter a text string for Amazon RDS to use to filter the results\. Only DB instances whose names contain the string appear\.

1. Choose the DB instance with Provisioned IOPS that you want to modify\.

1. Choose **Modify**\.

1. On the **Modify DB instance** page, choose **Provisioned IOPS SSD \(io1\)** for **Storage type**\.

1. For **Provisioned IOPS**, enter a value\.

   If the value that you specify for either **Allocated storage** or **Provisioned IOPS** is outside the limits supported by the other parameter, a warning message is displayed\. This message gives the range of values required for the other parameter\. 

1. Choose **Continue**\.

1. Choose **Apply immediately** in the **Scheduling of modifications** section to apply the changes to the DB instance immediately\. Or choose **Apply during the next scheduled maintenance window** to apply the changes during the next maintenance window\.

1. Review the parameters to be changed, and choose **Modify DB instance** to complete the modification\.

   The new value for allocated storage or for Provisioned IOPS appears in the **Status** column\. 

### AWS CLI<a name="User_PIOPS.Increase.cli"></a>

To change the Provisioned IOPS setting for a DB instance, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)\. Set the following parameters:
+ `--storage-type` – Set to `io1` for Provisioned IOPS\.
+ `--allocated-storage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `--iops` – The new amount of Provisioned IOPS for the DB instance, expressed in I/O operations per second\.
+ `--apply-immediately` – Use `--apply-immediately` to apply changes immediately\. Use `--no-apply-immediately` \(the default\) to apply changes during the next maintenance window\.

### RDS API<a name="User_PIOPS.Increase.api"></a>

To change the Provisioned IOPS settings for a DB instance, use the Amazon RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html)\. Set the following parameters:
+ `StorageType` – Set to `io1` for Provisioned IOPS\.
+ `AllocatedStorage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `Iops` – The new IOPS rate for the DB instance, expressed in I/O operations per second\.
+ `ApplyImmediately` – Set this option to `True` to apply changes immediately\. Set this option to `False` \(the default\) to apply changes during the next maintenance window\.

## I/O\-intensive storage modifications<a name="USER_PIOPS.IOIntensive"></a>

Amazon RDS DB instances use Amazon Elastic Block Store \(EBS\) volumes for database and log storage\. Depending on the amount of storage requested, RDS \(except for RDS for SQL Server\) automatically *stripes* across multiple Amazon EBS volumes to enhance performance\. RDS DB instances with SSD storage types are backed by either one or four striped Amazon EBS volumes in a RAID 0 configuration\. By design, storage modification operations for an RDS DB instance have minimal impact on ongoing database operations\.

In most cases, storage scaling modifications are completely offloaded to the Amazon EBS layer and are transparent to the database\. This process is typically completed within a few minutes\. However, some older RDS storage volumes require a different process for modifying the size, Provisioned IOPS, or storage type\. This involves making a full copy of the data using a potentially I/O\-intensive operation\.

Storage modification uses an I/O\-intensive operation if any of the following factors apply:
+ The source storage type is magnetic\. Magnetic storage doesn't support elastic volume modification\.
+ The RDS DB instance isn't on a one\- or four\-volume Amazon EBS layout\. You can view the number of Amazon EBS volumes in use on your RDS DB instances by using Enhanced Monitoring metrics\. For more information, see [Viewing OS metrics in the RDS console](USER_Monitoring.OS.Viewing.md)\.
+ The target size of the modification request increases the allocated storage above 400 GiB for RDS for MariaDB, MySQL, and PostgreSQL instances, and 200 GiB for RDS for Oracle\. Storage autoscaling operations have the same effect when they increase the allocated storage size of your DB instance above these thresholds\.

If your storage modification involves an I/O\-intensive operation, it consumes I/O resources and increases the load on your DB instance\. Storage modifications with I/O\-intensive operations involving General Purpose SSD \(gp2\) storage can deplete your I/O credit balance, resulting in longer conversion times\.

We recommend as a best practice to schedule these storage modification requests outside of peak hours to help reduce the time required to complete the storage modification operation\. Alternatively, you can create a read replica of the DB instance and perform the storage modification on the read replica\. Then promote the read replica to be the primary DB instance\. For more information, see [Working with read replicas](USER_ReadRepl.md)\.

For more information, see [Why is an Amazon RDS DB instance stuck in the modifying state when I try to increase the allocated storage?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-stuck-modifying/)

## Modifying settings for General Purpose SSD \(gp3\) storage<a name="USER_PIOPS.gp3"></a>

You can modify the settings for a DB instance that uses General Purpose SSD \(gp3\) storage by using the Amazon RDS console, AWS CLI, or Amazon RDS API\. Specify the storage type, allocated storage, amount of Provisioned IOPS, and storage throughput that you require\. Although you can reduce the amount of IOPS provisioned for your instance, you can't reduce the storage size\.

In most cases, scaling storage doesn't require any outage\. After you modify the storage IOPS for a DB instance, the status of the DB instance is **storage\-optimization**\. You can expect elevated latencies, but still within the single\-digit millisecond range, during storage optimization\. The DB instance is fully operational after a storage modification\.

**Note**  
You can't make further storage modifications until six \(6\) hours after storage optimization has completed on the instance\.

For information on the ranges of allocated storage, Provisioned IOPS, and storage throughput available for each database engine, see [gp3 storage](CHAP_Storage.md#gp3-storage)\.

### Console<a name="USER_PIOPS.gp3.Console"></a>

**To change the storage performance settings for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

   To filter the list of DB instances, for **Filter databases** enter a text string for Amazon RDS to use to filter the results\. Only DB instances whose names contain the string appear\.

1. Choose the DB instance with gp3 storage that you want to modify\.

1. Choose **Modify**\.

1. On the **Modify DB Instance page**, choose **General Purpose SSD \(gp3\)** for **Storage type**, then do the following:

   1. For **Provisioned IOPS**, choose a value\.

      If the value that you specify for either **Allocated storage** or **Provisioned IOPS** is outside the limits supported by the other parameter, a warning message appears\. This message gives the range of values required for the other parameter\.

   1. For **Storage throughput**, choose a value\.

      If the value that you specify for either **Provisioned IOPS** or **Storage throughput** is outside the limits supported by the other parameter, a warning message appears\. This message gives the range of values required for the other parameter\.

1. Choose **Continue**\.

1. Choose **Apply immediately** in the **Scheduling of modifications** section to apply the changes to the DB instance immediately\. Or choose **Apply during the next scheduled maintenance window** to apply the changes during the next maintenance window\.

1. Review the parameters to be changed, and choose **Modify DB instance** to complete the modification\.

   The new value for Provisioned IOPS appears in the **Status** column\.

### AWS CLI<a name="USER_PIOPS.gp3.CLI"></a>

To change the storage performance settings for a DB instance, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)\. Set the following parameters:
+ `--storage-type` – Set to `gp3` for General Purpose SSD \(gp3\)\.
+ `--allocated-storage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `--iops` – The new amount of Provisioned IOPS for the DB instance, expressed in I/O operations per second\.
+ `--storage-throughput` – The new storage throughput for the DB instance, expressed in MiBps\.
+ `--apply-immediately` – Use `--apply-immediately` to apply changes immediately\. Use `--no-apply-immediately` \(the default\) to apply changes during the next maintenance window\.

### RDS API<a name="USER_PIOPS.gp3.API"></a>

To change the storage performance settings for a DB instance, use the Amazon RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html)\. Set the following parameters:
+ `StorageType` – Set to `gp3` for General Purpose SSD \(gp3\)\.
+ `AllocatedStorage` – Amount of storage to be allocated for the DB instance, in gibibytes\.
+ `Iops` – The new IOPS rate for the DB instance, expressed in I/O operations per second\.
+ `StorageThroughput` – The new storage throughput for the DB instance, expressed in MiBps\.
+ `ApplyImmediately` – Set this option to `True` to apply changes immediately\. Set this option to `False` \(the default\) to apply changes during the next maintenance window\.