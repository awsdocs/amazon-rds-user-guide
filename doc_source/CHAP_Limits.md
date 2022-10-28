# Quotas and constraints for Amazon RDS<a name="CHAP_Limits"></a>

Following, you can find a description of the resource quotas and naming constraints for Amazon RDS\.

**Topics**
+ [Quotas in Amazon RDS](#RDS_Limits.Limits)
+ [Naming constraints in Amazon RDS](#RDS_Limits.Constraints)
+ [Maximum number of database connections](#RDS_Limits.MaxConnections)
+ [File size limits in Amazon RDS](#RDS_Limits.FileSize)

## Quotas in Amazon RDS<a name="RDS_Limits.Limits"></a>

Each AWS account has quotas, for each AWS Region, on the number of Amazon RDS resources that can be created\. After a quota for a resource has been reached, additional calls to create that resource fail with an exception\.

The following table lists the resources and their quotas per AWS Region\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)

**Note**  
By default, you can have up to a total of 40 DB instances\. RDS DB instances, Aurora DB instances, Amazon Neptune instances, and Amazon DocumentDB instances apply to this quota\.  
The following limitations apply to the Amazon RDS DB instances:  
10 for each SQL Server edition \(Enterprise, Standard, Web, and Express\) under the "license\-included" model
10 for Oracle under the "license\-included" model
40 for MySQL, MariaDB, or PostgreSQL
40 for Oracle under the "bring\-your\-own\-license" \(BYOL\) licensing model
If your application requires more DB instances, you can request additional DB instances by opening the [Service Quotas console](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/dashboard)\. In the navigation pane, choose **AWS services**\. Choose **Amazon Relational Database Service \(Amazon RDS\)**, choose a quota, and follow the directions to request a quota increase\. For more information, see [Requesting a quota increase](https://docs.aws.amazon.com/servicequotas/latest/userguide/request-increase.html) in the *Service Quotas User Guide*\.  
For RDS for Oracle and RDS for SQL Server, the read replica limit is 5 per source database for each Region\.  
Backups managed by AWS Backup are considered manual DB snapshots, but don't count toward the manual snapshot quota\. For information about AWS Backup, see the [https://docs.aws.amazon.com/aws-backup/latest/devguide](https://docs.aws.amazon.com/aws-backup/latest/devguide)\.

If you use any RDS API operations and exceed the default quota for the number of calls per second, the Amazon RDS API issues an error like the following one\. 

ClientError: An error occurred \(ThrottlingException\) when calling the *API\_name* operation: Rate exceeded\. 

Here, reduce the number of calls per second\. The quota is meant to cover most use cases\. If higher limits are needed, request a quota increase by contacting AWS Support\. Open the [AWS Support Center](https://console.aws.amazon.com/support/home#/) page, sign in if necessary, and choose **Create case**\. Choose **Service limit increase**\. Complete and submit the form\.

**Note**  
This quota can't be changed in the Amazon RDS Service Quotas console\.

## Naming constraints in Amazon RDS<a name="RDS_Limits.Constraints"></a>

The following table describes naming constraints in Amazon RDS\. 


****  

| Resource or item | Constraints | 
| --- | --- | 
| DB instance identifier |  Identifiers have these naming constraints: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  | 
|  Database name  |  Database name constraints differ for each database engine \. For more information, see the available settings when creating each DB instance\.  This approach doesn't apply to SQL Server\. For SQL Server, you create your databases after you create your DB instance\.   | 
|  Master user name  |  Master user name constraints differ for each database engine\. For more information, see the available settings when creating each DB instance\.  | 
|  Master password  |  The password for the database master user can include any printable ASCII character except `/`, `'`, `"`, `@`, or a space\. The password has the following number of printable ASCII characters depending on the DB engine:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)   | 
| DB parameter group name |  These names have these constraints: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  | 
|  DB subnet group name  |  These names have these constraints: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  | 

## Maximum number of database connections<a name="RDS_Limits.MaxConnections"></a>

The maximum number of simultaneous database connections varies by the DB engine type and the memory allocation for the DB instance class\. The maximum number of connections is generally set in the parameter group associated with the DB instance\. The exception is Microsoft SQL Server, where it is set in the server properties for the DB instance in SQL Server Management Studio \(SSMS\)\.

`DBInstanceClassMemory` is in bytes\. For details about how this value is calculated, see [Specifying DB parameters](USER_ParamValuesRef.md)\. Because of memory reserved for the operating system and RDS management processes, this memory size is smaller than the value in gibibytes \(GiB\) shown in [Hardware specifications for DB instance classes](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Summary)\.

If your applications frequently open and close connections, or keep a large number of long\-lived connections open, we recommend that you use Amazon RDS Proxy\. RDS Proxy is a fully managed, highly available database proxy that uses connection pooling to share database connections securely and efficiently\. To learn more about RDS Proxy, see [Using Amazon RDS Proxy](rds-proxy.md)\.

**Note**  
For Oracle, you set the maximum number of user processes and user and system sessions\.


**Maximum database connections**  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html)

In some instances, the total RAM is 16 GiB, or 17,179,869,184 bytes\. In these instances, the variable `DBInstanceClassMemory` automatically subtracts the amounts reserved to the operating system and the RDS processes that manage the instance\. The remainder of the subtraction is then divided by 12,582,880\. This results in the maximum connections number being around 1,300, depending on instance type, instance size, and DB engine\.

Database connections consume memory\. Setting one of these parameters too high can cause a low memory condition that might cause a DB instance to be placed in the **incompatible\-parameters** status\. For more information, see [Diagnosing and resolving incompatible parameters status for a memory limit](CHAP_Troubleshooting.md#CHAP_Troubleshooting.incompatible-parameters-memory)\.

**Note**  
You might see fewer than the maximum number of DB connections\. This is to avoid potential out\-of\-memory issues\.

## File size limits in Amazon RDS<a name="RDS_Limits.FileSize"></a>

File size limits apply to certain Amazon RDS DB instances\. For more information, see the following engine\-specific limits:
+ [MariaDB file size limits in Amazon RDS](CHAP_MariaDB.Limitations.md#RDS_Limits.FileSize.MariaDB)
+ [MySQL file size limits in Amazon RDS](MySQL.KnownIssuesAndLimitations.md#MySQL.Concepts.Limits.FileSize)
+ [Oracle file size limits in Amazon RDS](Oracle.Concepts.limitations.md#Oracle.Concepts.file-size-limits)