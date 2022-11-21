# Turning on HugePages for an RDS for Oracle instance<a name="Oracle.Concepts.HugePages"></a>

Amazon RDS for Oracle supports Linux kernel HugePages for increased database scalability\. HugePages results in smaller page tables and less CPU time spent on memory management, increasing the performance of large database instances\. For more information, see [Overview of HugePages](https://docs.oracle.com/database/121/UNXAR/appi_vlm.htm#UNXAR400) in the Oracle documentation\. 

You can use HugePages with all supported versions and editions of RDS for Oracle\.

 The `use_large_pages` parameter controls whether HugePages are turned on for a DB instance\. The possible settings for this parameter are `ONLY`, `FALSE`, and `{DBInstanceClassHugePagesDefault}`\. The `use_large_pages` parameter is set to `{DBInstanceClassHugePagesDefault}` in the default DB parameter group for Oracle\. 

To control whether HugePages are turned on for a DB instance automatically, you can use the `DBInstanceClassHugePagesDefault` formula variable in parameter groups\. The value is determined as follows:
+ For the DB instance classes mentioned in the table following, `DBInstanceClassHugePagesDefault` always evaluates to `FALSE` by default, and `use_large_pages` evaluates to `FALSE`\. You can turn on HugePages manually for these DB instance classes if the DB instance class has at least 14 GiB of memory\.
+ For DB instance classes not mentioned in the table following, if the DB instance class has less than 14 GiB of memory, `DBInstanceClassHugePagesDefault` always evaluates to `FALSE`\. Also, `use_large_pages` evaluates to `FALSE`\.
+ For DB instance classes not mentioned in the table following, if the instance class has at least 14 GiB of memory and less than 100 GiB of memory, `DBInstanceClassHugePagesDefault` evaluates to `TRUE` by default\. Also, `use_large_pages` evaluates to `ONLY`\. You can turn off HugePages manually by setting `use_large_pages` to `FALSE`\.
+ For DB instance classes not mentioned in the table following, if the instance class has at least 100 GiB of memory, `DBInstanceClassHugePagesDefault` always evaluates to `TRUE`\. Also, `use_large_pages` evaluates to `ONLY` and HugePages can't be disabled\.

HugePages are not turned on by default for the following DB instance classes\. 


****  

| DB instance class family | DB instance classes with HugePages not turned on by default | 
| --- | --- | 
|  db\.m5  |  db\.m5\.large  | 
|  db\.m4  |  db\.m4\.large, db\.m4\.xlarge, db\.m4\.2xlarge, db\.m4\.4xlarge, db\.m4\.10xlarge  | 
|  db\.t3  |  db\.t3\.micro, db\.t3\.small, db\.t3\.medium, db\.t3\.large  | 

For more information about DB instance classes, see [Hardware specifications for DB instance classes](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Summary)\. 

To turn on HugePages for new or existing DB instances manually, set the `use_large_pages` parameter to `ONLY`\. You can't use HugePages with Oracle Automatic Memory Management \(AMM\)\. If you set the parameter `use_large_pages` to `ONLY`, then you must also set both `memory_target` and `memory_max_target` to `0`\. For more information about setting DB parameters for your DB instance, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. 

You can also set the `sga_target`, `sga_max_size`, and `pga_aggregate_target` parameters\. When you set system global area \(SGA\) and program global area \(PGA\) memory parameters, add the values together\. Subtract this total from your available instance memory \(`DBInstanceClassMemory`\) to determine the free memory beyond the HugePages allocation\. You must leave free memory of at least 2 GiB, or 10 percent of the total available instance memory, whichever is smaller\. 

After you configure your parameters, you must reboot your DB instance for the changes to take effect\. For more information, see [Rebooting a DB instance](USER_RebootInstance.md)\. 

**Note**  
The Oracle DB instance defers changes to SGA\-related initialization parameters until you reboot the instance without failover\. In the Amazon RDS console, choose **Reboot** but *do not* choose **Reboot with failover**\. In the AWS CLI, call the `reboot-db-instance` command with the `--no-force-failover` parameter\. The DB instance does not process the SGA\-related parameters during failover or during other maintenance operations that cause the instance to restart\.

The following is a sample parameter configuration for HugePages that enables HugePages manually\. You should set the values to meet your needs\. 

```
1. memory_target            = 0
2. memory_max_target        = 0
3. pga_aggregate_target     = {DBInstanceClassMemory*1/8}
4. sga_target               = {DBInstanceClassMemory*3/4}
5. sga_max_size             = {DBInstanceClassMemory*3/4}
6. use_large_pages          = ONLY
```

Assume the following parameters values are set in a parameter group\.

```
1. memory_target            = IF({DBInstanceClassHugePagesDefault}, 0, {DBInstanceClassMemory*3/4})
2. memory_max_target        = IF({DBInstanceClassHugePagesDefault}, 0, {DBInstanceClassMemory*3/4})
3. pga_aggregate_target     = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*1/8}, 0)
4. sga_target               = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*3/4}, 0)
5. sga_max_size             = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*3/4}, 0)
6. use_large_pages          = {DBInstanceClassHugePagesDefault}
```

The parameter group is used by a db\.r4 DB instance class with less than 100 GiB of memory\. With these parameter settings and `use_large_pages` set to `{DBInstanceClassHugePagesDefault}`, HugePages are turned on for the db\.r4 instance\.

Consider another example with following parameters values set in a parameter group\.

```
1. memory_target           = IF({DBInstanceClassHugePagesDefault}, 0, {DBInstanceClassMemory*3/4})
2. memory_max_target       = IF({DBInstanceClassHugePagesDefault}, 0, {DBInstanceClassMemory*3/4})
3. pga_aggregate_target    = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*1/8}, 0)
4. sga_target              = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*3/4}, 0)
5. sga_max_size            = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*3/4}, 0)
6. use_large_pages         = FALSE
```

The parameter group is used by a db\.r4 DB instance class and a db\.r5 DB instance class, both with less than 100 GiB of memory\. With these parameter settings, HugePages are turned off on the db\.r4 and db\.r5 instance\.

**Note**  
If this parameter group is used by a db\.r4 DB instance class or db\.r5 DB instance class with at least 100 GiB of memory, the `FALSE` setting for `use_large_pages` is overridden and set to `ONLY`\. In this case, a customer notification regarding the override is sent\.

After HugePages are active on your DB instance, you can view HugePages information by enabling enhanced monitoring\. For more information, see [Monitoring OS metrics with Enhanced Monitoring](USER_Monitoring.OS.md)\. 