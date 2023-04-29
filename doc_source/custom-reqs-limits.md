# Availability and requirements for Amazon RDS Custom for Oracle<a name="custom-reqs-limits"></a>

In this topic, you can find a summary of the Amazon RDS Custom for Oracle feature availability and requirements for quick reference\.

**Topics**
+ [Region and version availability](#custom-reqs-limits.RegionVersionAvailability)
+ [DB instance class support for RDS Custom for Oracle](#custom-reqs-limits.instances)
+ [General requirements for RDS Custom for Oracle](#custom-reqs-limits.reqs)
+ [General limitations for RDS Custom for Oracle](#custom-reqs-limits.limits)

## Region and version availability<a name="custom-reqs-limits.RegionVersionAvailability"></a>

Feature availability and support vary across specific versions of each database engine, and across AWS Regions\. For more information on version and Region availability of Amazon RDS Custom for Oracle, see [RDS Custom](Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.md)\. 

## DB instance class support for RDS Custom for Oracle<a name="custom-reqs-limits.instances"></a>

RDS Custom for Oracle supports the following DB instance classes:
+ db\.m5\.large–db\.m5\.24xlarge
+ db\.r5\.large–db\.r5\.24xlarge

## General requirements for RDS Custom for Oracle<a name="custom-reqs-limits.reqs"></a>

Make sure to follow these requirements for Amazon RDS Custom for Oracle:
+ Use [Oracle Software Delivery Cloud](https://edelivery.oracle.com/) to download Oracle installation and patch files\. For more information, see [Prerequisites for creating an RDS Custom for Oracle DB instance](custom-setup-orcl.md#custom-setup-orcl.review)\.
+ Use the DB instance classes shown in [DB instance class support for RDS Custom for Oracle](#custom-reqs-limits.instances)\. The DB instances must run Oracle Linux 7 Update 6\.
+ Specify the gp2, gp3, or io1 solid state drives for storage\. The maximum storage limit is 64 TiB\.
+ Make sure that you have an AWS KMS key to create an RDS Custom DB instance\. For more information, see [Step 1: Create or reuse a symmetric encryption AWS KMS key](custom-setup-orcl.md#custom-setup-orcl.cmk)\.
+ Use only the approved database installation and patch files\. For more information, see [Step 2: Download your database installation files and patches from Oracle Software Delivery Cloud](custom-cev.preparing.md#custom-cev.preparing.download)\.
+ Create an AWS Identity and Access Management \(IAM\) role and instance profile\. For more information, see [Step 3: Configure IAM and your Amazon VPC](custom-setup-orcl.md#custom-setup-orcl.iam-vpc)\.
+ Make sure to supply a networking configuration that RDS Custom can use to access other AWS services\. For specific requirements, see [Step 3: Configure IAM and your Amazon VPC](custom-setup-orcl.md#custom-setup-orcl.iam-vpc)\.
+ Make sure that the combined number of RDS Custom and Amazon RDS DB instances doesn't exceed your quota limit\. For example, if your quota for Amazon RDS is 40 DB instances, you can have 20 RDS Custom for Oracle DB instances and 20 Amazon RDS DB instances\.

## General limitations for RDS Custom for Oracle<a name="custom-reqs-limits.limits"></a>

The following limitations apply to RDS Custom for Oracle:
+ You can't provide your own AMI\.
+ You can specify only the default AMI or an AMI that has been previously used by a CEV\.
+ You can't modify a CEV to use a different AMI\.
+ Not all options are supported\. For example, when you create or modify an RDS Custom for Oracle DB instance, you can't do the following:
  + Change the number of CPU cores and threads per core on the DB instance class\.
  + Turn on storage autoscaling\.
  + Create a Multi\-AZ deployment\.
**Note**  
For an alternative HA solution, see the AWS blog article [Build high availability for Amazon RDS Custom for Oracle using read replicas](http://aws.amazon.com/blogs/database/build-high-availability-for-amazon-rds-custom-for-oracle-using-read-replicas/)\.
  + Set backup retention to `0`\.
  + Configure Kerberos authentication\.
  + Specify your own DB parameter group or option group\.
  + Turn on Performance Insights\.
  + Turn on automatic minor version upgrade\.
+ You can't specify a DB instance storage size greater than the maximum of 64 TiB\.
+ You can't create multiple Oracle databases on a single RDS Custom for Oracle DB instance\.
+ You can’t stop your RDS Custom for Oracle DB instance or its underlying Amazon EC2 instance\. Billing for an RDS Custom for Oracle DB instance can't be stopped\.
+ You can't use automatic shared memory management\. RDS Custom for Oracle supports only automatic memory management\. For more information, see [Automatic Memory Management](https://docs.oracle.com/en/database/oracle/oracle-database/19/admin/managing-memory.html#GUID-04EFED7D-D1F1-43C3-B78F-0FF9AFAC02B0) in the *Oracle Database Administrator’s Guide*\.
+ Make sure not to change the `DB_UNIQUE_NAME` for the primary DB instance\. Changing the name causes any restore operation to become stuck\.

For limitations specific to modifying an RDS Custom for Oracle DB instance, see [Modifying your RDS Custom for Oracle DB instance](custom-managing.md#custom-managing.modifying)\. For replication limitations, see [General limitations for RDS Custom for Oracle replication](custom-rr.md#custom-rr.limitations)\.