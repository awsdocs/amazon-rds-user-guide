# Requirements and limitations for Amazon RDS Custom for SQL Server<a name="custom-reqs-limits-MS"></a>

Following, you can find a summary of the Amazon RDS Custom for SQL Server requirements and limitations for quick reference\. Requirements and limitations also appear in the relevant sections\.

**Topics**
+ [General requirements for RDS Custom for SQL Server](#custom-reqs-limits.reqsMS)
+ [DB instance class support for RDS Custom for SQL Server](#custom-reqs-limits.instancesMS)
+ [AWS Region support for RDS Custom for SQL Server](#custom-reqs-limits.regionsMS)
+ [Limitations for RDS Custom for SQL Server](#custom-reqs-limits.limitsMS)

## General requirements for RDS Custom for SQL Server<a name="custom-reqs-limits.reqsMS"></a>

Make sure to follow these requirements for Amazon RDS Custom for SQL Server:
+ Use SQL Server 2019 Enterprise, Standard, or Web Edition\.
+ Use the instance classes shown in [DB instance class support for RDS Custom for SQL Server](#custom-reqs-limits.instancesMS)\. The only storage types supported are solid state drives \(SSD\) of types gp2 and io1\. The maximum storage limit is 16 TiB\.
+ Make sure that you have a symmetric encryption AWS KMS key to create an RDS Custom DB instance\. For more information, see [Make sure that you have a symmetric encryption AWS KMS key](custom-setup-sqlserver.md#custom-setup-sqlserver.cmk)\.
+ Make sure that you create an AWS Identity and Access Management \(IAM\) role and instance profile\. For more information, see [Creating your IAM role and instance profile manually](custom-setup-sqlserver.md#custom-setup-sqlserver.iam)\.
+ Make sure to supply a networking configuration that RDS Custom can use to access other AWS services\. For specific requirements, see [Configure networking, instance profile, and encryption](custom-setup-sqlserver.md#custom-setup-sqlserver.iam-vpc)\.
+ The combined number of RDS Custom and Amazon RDS DB instances can't exceed your quota limit\. For example, if your quota is 40 DB instances, you can have 20 RDS Custom for SQL Server DB instances and 20 Amazon RDS DB instances\.

## DB instance class support for RDS Custom for SQL Server<a name="custom-reqs-limits.instancesMS"></a>

RDS Custom for SQL Server supports the DB instance classes shown in the following table\.


| SQL Server edition | RDS Custom support | 
| --- | --- | 
|  Enterprise Edition  |   db\.r5\.xlarge–db\.r5\.24xlarge db\.m5\.xlarge–db\.m5\.24xlarge  | 
|  Standard Edition  |   db\.r5\.large–db\.r5\.24xlarge db\.m5\.large–db\.m5\.24xlarge  | 
|  Web Edition  |   db\.r5\.large–db\.r5\.4xlarge db\.m5\.large–db\.m5\.4xlarge  | 

## AWS Region support for RDS Custom for SQL Server<a name="custom-reqs-limits.regionsMS"></a>

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

## Limitations for RDS Custom for SQL Server<a name="custom-reqs-limits.limitsMS"></a>

The following limitations apply to RDS Custom for SQL Server:
+ You can't provide your own custom DB engine version\.
+ You can't provide your own AMI\.
+ You can't create read replicas in Amazon RDS for RDS Custom for SQL Server DB instances\. However, you can configure high availability manually using Always On Availability Groups\. For more information, see [Working with high availability features for RDS Custom for SQL Server](custom-managing-sqlserver.md#custom-managing.AO)\.
+ You can't modify the storage size after creating the DB instance\. Migrate to a new DB instance with additional storage allocated to it\.
+ You can't modify the time zone of an existing RDS Custom for SQL Server DB instance\.
+ Changes to the Microsoft Windows operating system or C: drive don't persist when you replace an Amazon EC2 instance\. However, you can redo those changes using automation\.
+ Not all options are supported\. For example, when you create an RDS Custom for SQL Server DB instance, you can't do the following:
  + Change the number of CPU cores and threads per core on the DB instance class\.
  + Turn on storage autoscaling\.
  + Create a Multi\-AZ deployment\. However, you can configure high availability manually using Always On Availability Groups\. For more information, see [Working with high availability features for RDS Custom for SQL Server](custom-managing-sqlserver.md#custom-managing.AO)\.
  + Configure Kerberos authentication using the AWS Management Console\. However, you can configure Windows Authentication manually and use Kerberos\.
  + Specify your own DB parameter group, option group, or character set\.
  + Turn on Performance Insights\.
  + Turn on automatic minor version upgrade\.
+ The maximum DB instance storage is 16 TiB\.