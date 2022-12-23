# Storing temporary data in an RDS for Oracle instance store<a name="CHAP_Oracle.advanced-features.instance-store"></a>

Use an instance store for the temporary tablespaces and the Database Smart Flash Cache \(the flash cache\) on supported RDS for Oracle DB instance classes\.

**Topics**
+ [Overview of the RDS for Oracle instance store](#CHAP_Oracle.advanced-features.instance-store.overview)
+ [Turning on an RDS for Oracle instance store](#CHAP_Oracle.advanced-features.instance-store.Enable)
+ [Configuring an RDS for Oracle instance store](#CHAP_Oracle.advanced-features.instance-store.configuring)
+ [Considerations when changing the DB instance type](#CHAP_Oracle.advanced-features.instance-store.modifying)
+ [Working with an instance store on an Oracle read replica](#CHAP_Oracle.advanced-features.instance-store.replicas)
+ [Configuring a temporary tablespace group on an instance store and Amazon EBS](#CHAP_Oracle.advanced-features.instance-store.temp-ebs)
+ [Removing an RDS for Oracle instance store](#CHAP_Oracle.advanced-features.instance-store.Disable)

## Overview of the RDS for Oracle instance store<a name="CHAP_Oracle.advanced-features.instance-store.overview"></a>

An *instance store* provides temporary block\-level storage for an RDS for Oracle DB instance\. You can use an instance store for temporary storage of information that changes frequently\.

An instance store is based on Non\-Volatile Memory Express \(NVMe\) devices that are physically attached to the host computer\. The storage is optimized for low latency, random I/O performance, and sequential read throughput\.

The size of the instance store varies by DB instance type\. For more information about the instance store, see [Amazon EC2 instance store](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html) in the *Amazon Elastic Compute Cloud User Guide for Linux Instances*\.

**Topics**
+ [Types of data in the RDS for Oracle instance store](#CHAP_Oracle.advanced-features.instance-store.overview.uses)
+ [Benefits of the RDS for Oracle instance store](#CHAP_Oracle.advanced-features.instance-store.overview.benefits)
+ [Supported instance classes for the RDS for Oracle instance store](#CHAP_Oracle.advanced-features.instance-store.overview.instance-classes)
+ [Supported engine versions for the RDS for Oracle instance store](#CHAP_Oracle.advanced-features.instance-store.overview.db-versions)
+ [Supported AWS Regions for the RDS for Oracle instance store](#CHAP_Oracle.advanced-features.instance-store.overview.regions)
+ [Cost of the RDS for Oracle instance store](#CHAP_Oracle.advanced-features.instance-store.overview.cost)

### Types of data in the RDS for Oracle instance store<a name="CHAP_Oracle.advanced-features.instance-store.overview.uses"></a>

You can place the following types of RDS for Oracle temporary data in an instance store:

A temporary tablespace  
Oracle Database uses temporary tablespaces to store intermediate query results that don't fit in memory\. Larger queries can generate large amounts of intermediate data that needs to be cached temporarily, but doesn't need to persist\. In particular, a temporary tablespace is useful for sorts, hash aggregations, and joins\. If your RDS for Oracle DB instance uses the Enterprise Edition or Standard Edition 2, you can place a temporary tablespace in an instance store\.

The flash cache  
The flash cache improves the performance of single\-block random reads in the conventional path\. A best practice is to size the cache to accommodate most of your active data set\. If your RDS for Oracle DB instance uses the Enterprise Edition, you can place the flash cache in an instance store\.

By default, an instance store is configured for a temporary tablespace but not for the flash cache\. You can't place Oracle data files and database log files in an instance store\.

### Benefits of the RDS for Oracle instance store<a name="CHAP_Oracle.advanced-features.instance-store.overview.benefits"></a>

You might consider using an instance store to store temporary files and caches that you can afford to lose\. If you want to improve DB performance, or if an increasing workload is causing performance problems for your Amazon EBS storage, consider scaling to an instance class that supports an instance store\.

By placing your temporary tablespace and flash cache on an instance store, you get the following benefits:
+ Lower read latencies
+ Higher throughput
+ Reduced load on your Amazon EBS volumes
+ Lower storage and snapshot costs because of reduced Amazon EBS load
+ Less need to provision high IOPS, possibly lowering your overall cost

 By placing your temporary tablespace on the instance store, you deliver an immediate performance boost to queries that use temporary space\. When you place the flash cache on the instance store, cached block reads typically have much lower latency than Amazon EBS reads\. The flash cache needs to be "warmed up" before it delivers performance benefits\. The cache warms up by itself because the database writes blocks to the flash cache as they age out of the database buffer cache\.

**Note**  
In some cases, the flash cache causes performance overhead because of cache management\. Before you turn on the flash cache in a production environment, we recommend that you analyze your workload and test the cache in a test environment\.

### Supported instance classes for the RDS for Oracle instance store<a name="CHAP_Oracle.advanced-features.instance-store.overview.instance-classes"></a>

Amazon RDS supports the instance store for the following DB instance classes:
+ db\.m5d
+ db\.r5d
+ db\.x2idn
+ db\.x2iedn

RDS for Oracle supports the preceding DB instance classes for the BYOL licensing model only\. For more information, see [Supported RDS for Oracle instance classes](Oracle.Concepts.InstanceClasses.md#Oracle.Concepts.InstanceClasses.Supported) and [Bring Your Own License \(BYOL\)](Oracle.Concepts.Licensing.md#Oracle.Concepts.Licensing.BYOL)\.

To see the total instance storage for the supported DB instance types, run the following command in the AWS CLI\. 

**Example**  

```
aws ec2 describe-instance-types \
  --filters "Name=instance-type,Values=*5d.*large*" \
  --query "InstanceTypes[?contains(InstanceType,'m5d')||contains(InstanceType,'r5d')][InstanceType, InstanceStorageInfo.TotalSizeInGB]" \
  --output table
```

The preceding command returns the raw device size for the instance store\. RDS for Oracle uses a small portion of this space for configuration\. The space in the instance store that is available for temporary tablespaces or the flash cache is slightly smaller\.

### Supported engine versions for the RDS for Oracle instance store<a name="CHAP_Oracle.advanced-features.instance-store.overview.db-versions"></a>

The instance store is supported for the following RDS for Oracle engine versions: 
+ 21\.0\.0\.0\.ru\-2022\-01\.rur\-2022\-01\.r1 or higher Oracle Database 21c versions
+ 19\.0\.0\.0\.ru\-2021\-10\.rur\-2021\-10\.r1 or higher Oracle Database 19c versions

### Supported AWS Regions for the RDS for Oracle instance store<a name="CHAP_Oracle.advanced-features.instance-store.overview.regions"></a>

The instance store is available in all AWS Regions where one or more of these instance types are supported\. For more information on the db\.m5d and db\.r5d instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\. For more information on the instance classes supported by Amazon RDS for Oracle, see [RDS for Oracle instance classes](Oracle.Concepts.InstanceClasses.md)\.

### Cost of the RDS for Oracle instance store<a name="CHAP_Oracle.advanced-features.instance-store.overview.cost"></a>

The cost of the instance store is built into the cost of the instance\-store turned on instances\. You don't incur additional costs by enabling an instance store on an RDS for Oracle DB instance\. For more information about instance\-store turned on instances, see [Supported instance classes for the RDS for Oracle instance store](#CHAP_Oracle.advanced-features.instance-store.overview.instance-classes)\.

## Turning on an RDS for Oracle instance store<a name="CHAP_Oracle.advanced-features.instance-store.Enable"></a>

To turn on the instance store for RDS for Oracle temporary data, do one of the following:
+ Create an RDS for Oracle DB instance using a supported instance class\. For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.
+ Modify an existing RDS for Oracle DB instance to use a supported instance class\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

## Configuring an RDS for Oracle instance store<a name="CHAP_Oracle.advanced-features.instance-store.configuring"></a>

By default, 100% of instance store space is allocated to the temporary tablespace\. To configure the instance store to allocate space to the flash cache and temporary tablespace, set the following parameters in the parameter group for your instance:

**db\_flash\_cache\_size=\{DBInstanceStore\*\{0,2,4,6,8,10\}/10\}**  
This parameter specifies the amount of storage space allocated for the flash cache\. This parameter is valid only for Oracle Database Enterprise Edition\. The default value is `{DBInstanceStore*0/10}`\. If you set a nonzero value for `db_flash_cache_size`, your RDS for Oracle instance enables the flash cache after you restart the instance\.

**rds\.instance\_store\_temp\_size=\{DBInstanceStore\*\{0,2,4,6,8,10\}/10\}**  
This parameter specifies the amount of storage space allocated for the temporary tablespace\. The default value is `{DBInstanceStore*10/10}`\. This parameter is modifiable for Oracle Database Enterprise Edition and read\-only for Standard Edition 2\. If you set a nonzero value for `rds.instance_store_temp_size`, Amazon RDS allocates space in the instance store for the temporary tablespace\.  
You can set the `db_flash_cache_size` and `rds.instance_store_temp_size` parameters for DB instances that don't use an instance store\. In this case, both settings evaluate to `0`, which turns off the feature\. In this case, you can use the same parameter group for different instance sizes and for instances that don't use an instance store\. If you modify these parameters, make sure to reboot the associated instances so that the changes can take effect\.  
If you allocate space for a temporary tablespace, Amazon RDS doesn't create the temporary tablespace automatically\. To learn how to create the temporary tablespace on the instance store, see [Creating a temporary tablespace on the instance store](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.creating-tts-instance-store)\.

The combined value of the preceding parameters must not exceed 10/10, or 100%\. The following table illustrates valid and invalid parameter settings\.


| db\_flash\_cache\_size setting | rds\.instance\_store\_temp\_size setting | Explanation | 
| --- | --- | --- | 
|  db\_flash\_cache\_size=\{DBInstanceStore\*0/10\}  |  rds\.instance\_store\_temp\_size=\{DBInstanceStore\*10/10\}  |  This is a valid configuration for all editions of Oracle Database\. Amazon RDS allocates 100% of instance store space to the temporary tablespace\. This is the default\.  | 
|  db\_flash\_cache\_size=\{DBInstanceStore\*10/10\}  |  rds\.instance\_store\_temp\_size=\{DBInstanceStore\*0/10\}  |  This is a valid configuration for Oracle Database Enterprise Edition only\. Amazon RDS allocates 100% of instance store space to the flash cache\.  | 
|  db\_flash\_cache\_size=\{DBInstanceStore\*2/10\}  |  rds\.instance\_store\_temp\_size=\{DBInstanceStore\*8/10\}  |  This is a valid configuration for Oracle Database Enterprise Edition only\. Amazon RDS allocates 20% of instance store space to the flash cache, and 80% of instance store space to the temporary tablespace\.  | 
|  db\_flash\_cache\_size=\{DBInstanceStore\*6/10\}  |  rds\.instance\_store\_temp\_size=\{DBInstanceStore\*4/10\}  |  This is a valid configuration for Oracle Database Enterprise Edition only\. Amazon RDS allocates 60% of instance store space to the flash cache, and 40% of instance store space to the temporary tablespace\.  | 
|  db\_flash\_cache\_size=\{DBInstanceStore\*2/10\}  |  rds\.instance\_store\_temp\_size=\{DBInstanceStore\*4/10\}  | This is a valid configuration for Oracle Database Enterprise Edition only\. Amazon RDS allocates 20% of instance store space to the flash cache, and 40% of instance store space to the temporary tablespace\. | 
|  db\_flash\_cache\_size=\{DBInstanceStore\*8/10\}  |  rds\.instance\_store\_temp\_size=\{DBInstanceStore\*8/10\}  |  This is an invalid configuration because the combined percentage of instance store space exceeds 100%\. In such cases, Amazon RDS fails the attempt\.  | 

## Considerations when changing the DB instance type<a name="CHAP_Oracle.advanced-features.instance-store.modifying"></a>

If you change your DB instance type, it can affect the configuration of the flash cache or the temporary tablespace on the instance store\. Consider the following modifications and their effects:

You scale up or scale down the DB instance that supports the instance store\.  
The following values increase or decrease proportionally to the new size of the instance store:  
+ The new size of the flash cache\.
+ The space allocated to the temporary tablespaces that reside in the instance store\.
For example, the setting `db_flash_cache_size={DBInstanceStore*6/10}` on a db\.m5d\.4xlarge instance provides around 340 GB of flash cache space\. If you scale up the instance type to db\.m5d\.8xlarge, the flash cache space increases to around 680 GB\.

You modify a DB instance that doesn't use an instance store to an instance that does use an instance store\.  
If `db_flash_cache_size` is set to a value larger than `0`, the flash cache is configured\. If `rds.instance_store_temp_size` is set to a value larger than `0`, the instance store space is allocated for use by a temporary tablespace\. RDS for Oracle doesn't move tempfiles to the instance store automatically\. For information about using the allocated space, see [Creating a temporary tablespace on the instance store](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.creating-tts-instance-store) or [Adding a tempfile to the instance store on a read replica](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.adding-tempfile-replica)\.

You modify a DB instance that uses an instance store to an instance that doesn't use an instance store\.  
In this case, RDS for Oracle removes the flash cache\. RDS re\-creates the tempfile that is currently located on the instance store on an Amazon EBS volume\. The maximum size of the new tempfile is the former size of the `rds.instance_store_temp_size` parameter\.

## Working with an instance store on an Oracle read replica<a name="CHAP_Oracle.advanced-features.instance-store.replicas"></a>

Read replicas support the flash cache and temporary tablespaces on an instance store\. While the flash cache works the same way as on the primary DB instance, note the following differences for temporary tablespaces:
+ You can't create a temporary tablespace on a read replica\. If you create a new temporary tablespace on the primary instance, RDS for Oracle replicates the tablespace information without tempfiles\. To add a new tempfile, use either of the following techniques:
  + Use the Amazon RDS procedure `rdsadmin.rdsadmin_util.add_inst_store_tempfile`\. RDS for Oracle creates a tempfile in the instance store on your read replica, and adds it to the specified temporary tablespace\.
  + Run the `ALTER TABLESPACE … ADD TEMPFILE` command\. RDS for Oracle places the tempfile on Amazon EBS storage\.
**Note**  
The tempfile sizes and storage types can be different on the primary DB instance and the read replica\.
+ You can manage the default temporary tablespace setting only on the primary DB instance\. RDS for Oracle replicates the setting to all read replicas\.
+ You can configure the temporary tablespace groups only on the primary DB instance\. RDS for Oracle replicates the setting to all read replicas\.

## Configuring a temporary tablespace group on an instance store and Amazon EBS<a name="CHAP_Oracle.advanced-features.instance-store.temp-ebs"></a>

You can configure a temporary tablespace group to include temporary tablespaces on both an instance store and Amazon EBS\. This technique is useful when you want more temporary storage than is allowed by the maximum setting of `rds.instance_store_temp_size`\.

When you configure a temporary tablespace group on both an instance store and Amazon EBS, the two tablespaces have significantly different performance characteristics\. Oracle Database chooses the tablespace to serve queries based on an internal algorithm\. Therefore, similar queries can vary in performance\.

Typically, you create a temporary tablespace in the instance store as follows:

1. Create a temporary tablespace in the instance store\.

1. Set the new tablespace as the database default temporary tablespace\.

If the tablespace size in the instance store is insufficient, you can create additional temporary storage as follows:

1. Assign the temporary tablespace in the instance store to a temporary tablespace group\.

1. Create a new temporary tablespace in Amazon EBS if one doesn't exist\.

1. Assign the temporary tablespace in Amazon EBS to the same tablespace group that includes the instance store tablespace\.

1. Set the tablespace group as the default temporary tablespace\.

The following example assumes that the size of the temporary tablespace in the instance store doesn't meet your application requirements\. The example creates the temporary tablespace `temp_in_inst_store` in the instance store, assigns it to tablespace group `temp_group`, adds the existing Amazon EBS tablespace named `temp_in_ebs` to this group, and sets this group as the default temporary tablespace\.

```
SQL> EXEC rdsadmin.rdsadmin_util.create_inst_store_tmp_tblspace('temp_in_inst_store');

PL/SQL procedure successfully completed.

SQL> ALTER TABLESPACE temp_in_inst_store TABLESPACE GROUP temp_group;

Tablespace altered.

SQL> ALTER TABLESPACE temp_in_ebs TABLESPACE GROUP temp_group;

Tablespace altered.

SQL> EXEC rdsadmin.rdsadmin_util.alter_default_temp_tablespace('temp_group');

PL/SQL procedure successfully completed.

SQL> SELECT * FROM DBA_TABLESPACE_GROUPS;

GROUP_NAME                     TABLESPACE_NAME
------------------------------ ------------------------------
TEMP_GROUP                     TEMP_IN_EBS
TEMP_GROUP                     TEMP_IN_INST_STORE

SQL> SELECT PROPERTY_VALUE FROM DATABASE_PROPERTIES WHERE PROPERTY_NAME='DEFAULT_TEMP_TABLESPACE';

PROPERTY_VALUE
--------------
TEMP_GROUP
```

## Removing an RDS for Oracle instance store<a name="CHAP_Oracle.advanced-features.instance-store.Disable"></a>

To remove the instance store, modify your RDS for Oracle DB instance to use an instance type that doesn't support instance store, such as db\.m5 or db\.r5\.