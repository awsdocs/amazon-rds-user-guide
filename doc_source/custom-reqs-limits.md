# Requirements and limitations for Amazon RDS Custom for Oracle<a name="custom-reqs-limits"></a>

Following, you can find a summary of the Amazon RDS Custom for Oracle requirements and limitations for quick reference\. Requirements and limitations also appear in the relevant sections\.

**Topics**
+ [General requirements for RDS Custom for Oracle](#custom-reqs-limits.reqs)
+ [DB instance class support for RDS Custom](#custom-reqs-limits.instances)
+ [AWS Region support for RDS Custom for Oracle](#custom-reqs-limits.regions)
+ [Limitations for RDS Custom for Oracle](#custom-reqs-limits.limits)

## General requirements for RDS Custom for Oracle<a name="custom-reqs-limits.reqs"></a>

Make sure to follow these requirements for Amazon RDS Custom for Oracle:
+ Use [Oracle Software Delivery Cloud](https://edelivery.oracle.com/) to download Oracle installation and patch files\. For more information, see [Prerequisites for creating an RDS Custom for Oracle instance](custom-setup-orcl.md#custom-setup-orcl.review)\.
+ Use the Enterprise Edition of one of the following versions:
  + Oracle Database 12\.1 with the January 2021 or later RU/RUR
  + Oracle Database 19c with the January 2021 or later RU/RUR
+ Use the DB instance classes shown in [DB instance class support for RDS Custom](#custom-reqs-limits.instances)\. The instances must run Oracle Linux 7 Update 6\. The only storage types supported are solid state drives \(SSD\) of types gp2 and io1\. The maximum storage limit is 64 TiB\.
+ Make sure that you have an AWS KMS key to create an RDS Custom DB instance\. For more information, see [Make sure that you have a symmetric encryption AWS KMS key](custom-setup-orcl.md#custom-setup-orcl.cmk)\.
+ Use only the approved database installation and patch files\. For more information, see [Downloading your database installation files and patches from Oracle](custom-cev.md#custom-cev.preparing.download)\.
+ Create an AWS Identity and Access Management \(IAM\) role and instance profile\. For more information, see [Configuring IAM and your VPC](custom-setup-orcl.md#custom-setup-orcl.iam-vpc)\.
+ Make sure to supply a networking configuration that RDS Custom can use to access other AWS services\. For specific requirements, see [Configuring IAM and your VPC](custom-setup-orcl.md#custom-setup-orcl.iam-vpc)\.
+ Make sure that the combined number of RDS Custom and Amazon RDS DB instances doesn't exceed your quota limit\. For example, if your quota for Amazon RDS is 40 DB instances, you can have 20 RDS Custom for Oracle DB instances and 20 Amazon RDS DB instances\.

## DB instance class support for RDS Custom<a name="custom-reqs-limits.instances"></a>

RDS Custom for Oracle supports the following DB instance classes:
+ db\.m5\.large–db\.m5\.24xlarge
+ db\.r5\.large–db\.r5\.24xlarge

## AWS Region support for RDS Custom for Oracle<a name="custom-reqs-limits.regions"></a>

RDS Custom is supported in the following AWS Regions:
+ Asia Pacific \(Tokyo\)
+ Asia Pacific \(Singapore\)
+ Asia Pacific \(Sydney\)
+ Europe \(Frankfurt\)
+ Europe \(Stockholm\)
+ Europe \(Ireland\)
+ US East \(N\. Virginia\)
+ US East \(Ohio\)
+ US West \(Oregon\)

## Limitations for RDS Custom for Oracle<a name="custom-reqs-limits.limits"></a>

The following limitations apply to RDS Custom for Oracle:
+ You can't provide your own AMI\.
+ Not all options are supported\. For example, when you create an RDS Custom for Oracle DB instance, you can't do the following:
  + Change the number of CPU cores and threads per core on the DB instance class\.
  + Turn on storage autoscaling\.
  + Set backup retention to `0`\.
  + Configure Kerberos authentication\.
  + Specify your own DB parameter group, option group, or character set\.
  + Turn on Performance Insights\.
  + Turn on automatic minor version upgrade\.
+ You can't change the DB instance class\. For example, you can't change a db\.m5\.xlarge instance to db\.m5\.2xlarge\. However, you can restore a DB snapshot to a different DB instance class\.
+ The maximum DB instance storage is 64 TiB\.
+ You can't use the Oracle Multitenant architecture\.
+ Only one database is supported on an RDS Custom for Oracle instance\.