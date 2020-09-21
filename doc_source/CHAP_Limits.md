# Quotas and Constraints for Amazon RDS<a name="CHAP_Limits"></a>

Following, you can find a description of the resource quotas and naming constraints for Amazon RDS\.

**Topics**
+ [Quotas in Amazon RDS](#RDS_Limits.Limits)
+ [Naming Constraints in Amazon RDS](#RDS_Limits.Constraints)
+ [Maximum Number of Database Connections](#RDS_Limits.MaxConnections)
+ [File Size Limits in Amazon RDS](#RDS_Limits.FileSize)

## Quotas in Amazon RDS<a name="RDS_Limits.Limits"></a>

Each AWS account has quotas, for each AWS Region, on the number of Amazon RDS resources that can be created\. After a quota for a resource has been reached, additional calls to create that resource fail with an exception\.

The following table lists the resources and their quotas per AWS Region\.


| Resource | Default Quota | 
| --- | --- | 
| Authorizations per DB security group | 20 | 
| Burst balance \(for instances <1 TiB\) | 3000 IOPS | 
| Cross\-region snapshot copy requests | 5 | 
| DB instances | 40 | 
| DB security groups | 25 | 
| DB subnet groups | 50 | 
| Event subscriptions | 20 | 
| IAM roles per DB instance | 5 | 
| Manual snapshots | 100 | 
| Option groups | 20 | 
| Parameter groups | 50 | 
| Proxies | 20 | 
| Read replicas per primary | 5 | 
| Reserved DB instances | 40 | 
| Rules per security group | 20 | 
| Rules per virtual private cloud \(VPC\) security group | 50 inbound, 50 outbound | 
| Subnets per subnet group | 20 | 
| Tags per resource | 50 | 
| Total storage for all DB instances | 100 TB | 
| VPC security groups | 5 | 

**Note**  
By default, you can have up to a total of 40 DB instances\. RDS DB instances, Aurora DB instances, Amazon Neptune instances, and Amazon DocumentDB instances apply to this quota\.  
The following limitations apply to the Amazon RDS DB instances:  
10 for each SQL Server edition \(Enterprise, Standard, Web, and Express\) under the "license\-included" model
10 for Oracle under the "license\-included" model
40 for MySQL, MariaDB, or PostgreSQL
40 for Oracle under the "bring\-your\-own\-license" \(BYOL\) licensing model
If your application requires more DB instances, you can request additional DB instances by opening the [Service Quotas console](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/dashboard)\. In the navigation pane, choose **AWS services**\. Choose **Amazon Relational Database Service \(Amazon RDS\)**, choose a quota, and follow the directions to request a quota increase\. For more information, see [Requesting a Quota Increase](https://docs.aws.amazon.com/servicequotas/latest/userguide/request-increase.html) in the *Service Quotas User Guide*\.  
Backups managed by AWS Backup are considered manual snapshots for the manual snapshots quota\. For information about AWS Backup, see the [https://docs.aws.amazon.com/aws-backup/latest/devguide](https://docs.aws.amazon.com/aws-backup/latest/devguide)\.

## Naming Constraints in Amazon RDS<a name="RDS_Limits.Constraints"></a>

The following table describes naming constraints in Amazon RDS\. 


****  

| Resource or Item | Constraints | 
| --- | --- | 
| DB instance identifier |  Identifiers have these naming constraints: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  | 
|  Database name  |  Database name constraints differ for each database engine \. For more information, see the available settings when creating each DB instance\.  This approach doesn't apply to SQL Server\. For SQL Server, you create your databases after you create your DB instance\.   | 
|  Master user name  |  Master user name constraints differ for each database engine\. For more information, see the available settings when creating each DB instance\.  | 
|  Master password  |  The password for the database master user can include any printable ASCII character except `/`, `"`, `@`, or a space\. Master password length constraints differ for each database engine\. For more information, see the available settings when creating each DB instance\.  | 
| DB parameter group name |  These names have these constraints: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  | 
|  DB subnet group name  |  These names have these constraints: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  | 

## Maximum Number of Database Connections<a name="RDS_Limits.MaxConnections"></a>

The maximum number of simultaneous database connections varies by the DB engine type and the memory allocation for the DB instance class\. The maximum number of connections is set in the parameter group associated with the DB instance, except for Microsoft SQL Server, where it is set in the server properties for the DB instance in SQL Server Management Studio \(SSMS\)\.

**Note**  
For Oracle, you set the maximum number of user processes and user and system sessions\.


**Maximum Database Connections**  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)

**Note**  
You might see fewer than the maximum number of DB connections\. This is to avoid potential out\-of\-memory issues\.

## File Size Limits in Amazon RDS<a name="RDS_Limits.FileSize"></a>

File size limits apply to certain Amazon RDS DB instances\. For more information, see the following engine\-specific limits:
+ [MariaDB File Size Limits in Amazon RDS](CHAP_MariaDB.md#RDS_Limits.FileSize.MariaDB)
+ [MySQL File Size Limits in Amazon RDS](MySQL.KnownIssuesAndLimitations.md#MySQL.Concepts.Limits.FileSize)
+ [Oracle File Size Limits in Amazon RDS](CHAP_Oracle.md#Oracle.Concepts.file-size-limits)