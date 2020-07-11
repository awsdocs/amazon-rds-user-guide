# SQL Server Audit<a name="Appendix.SQLServer.Options.Audit"></a>

In Amazon RDS, you can audit Microsoft SQL Server databases by using the built\-in SQL Server auditing mechanism\. You can create audits and audit specifications in the same way that you create them for on\-premises database servers\. 

RDS uploads the completed audit logs to your S3 bucket, using the IAM role that you provide\. If you enable retention, RDS keeps your audit logs on your DB instance for the configured period of time\.

For more information, see [SQL Server Audit \(Database Engine\)](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) in the Microsoft SQL Server documentation\.

**Topics**
+ [Support for SQL Server Audit](#Appendix.SQLServer.Options.Audit.Support)
+ [Adding SQL Server Audit to the DB Instance Options](#Appendix.SQLServer.Options.Audit.Adding)
+ [Using SQL Server Audit](#Appendix.SQLServer.Options.Audit.CreateAuditsAndSpecifications)
+ [Viewing Audit Logs](#Appendix.SQLServer.Options.Audit.AuditRecords)
+ [Using SQL Server Audit with Multi\-AZ Instances](#Appendix.SQLServer.Options.Audit.Multi-AZ)
+ [Configuring an S3 Bucket](#Appendix.SQLServer.Options.Audit.S3bucket)
+ [Manually Creating an IAM Role for SQL Server Audit](#Appendix.SQLServer.Options.Audit.IAM)

## Support for SQL Server Audit<a name="Appendix.SQLServer.Options.Audit.Support"></a>

In Amazon RDS, starting with SQL Server 2012, all editions of SQL Server support server\-level audits, and the Enterprise edition also supports database\-level audits\. Starting with SQL Server 2016 \(13\.x\) SP1, all editions support both server\-level and database\-level audits\. For more information, see [SQL Server Audit \(Database Engine\)](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) in the SQL Server documentation\.

RDS supports configuring the following option settings for SQL Server Audit\. 


| Option Setting | Valid Values | Description | 
| --- | --- | --- | 
| IAM\_ROLE\_ARN | A valid Amazon Resource Name \(ARN\) in the format arn:aws:iam::account\-id:role/role\-name\. | The ARN of the IAM role that grants access to the S3 bucket where you want to store your audit logs\. For more information, see [Amazon Resource Names \(ARNs\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-iam) in the AWS General Reference\. | 
| S3\_BUCKET\_ARN | A valid ARN in the format arn:aws:s3:::bucket\-name or arn:aws:s3:::bucket\-name/key\-prefix | The ARN for the S3 bucket where you want to store your audit logs\. | 
| ENABLE\_COMPRESSION | true or false | Controls audit log compression\. By default, compression is enabled \(set to true\)\. | 
| RETENTION\_TIME | 0 to 840 | The retention time \(in hours\) that SQL Server audit records are kept on your RDS instance\. By default, retention is disabled\. | 

RDS supports SQL Server Audit in all AWS Regions except Middle East \(Bahrain\)\.

## Adding SQL Server Audit to the DB Instance Options<a name="Appendix.SQLServer.Options.Audit.Adding"></a>

Enabling SQL Server Audit requires two steps: enabling the option on the DB instance, and enabling the feature inside SQL Server\. The process for adding the SQL Server Audit option to a DB instance is as follows: 

1. Create a new option group, or copy or modify an existing option group\. 

1. Add and configure all required options\.

1. Associate the option group with the DB instance\.

After you add the SQL Server Audit option, you don't need to restart your DB instance\. As soon as the option group is active, you can create audits and store audit logs in your S3 bucket\. 

**To add and configure SQL Server Audit on a DB instance's option group**

1. Choose one of the following:
   + Use an existing option group\.
   + Create a custom DB option group and use that option group\. For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **SQLSERVER\_AUDIT** option to the option group, and configure the option settings\. For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\. 
   + For **IAM role**, if you already have an IAM role with the required policies, you can choose that role\. To create a new IAM role, choose **Create a New Role**\. For information about the required policies, see [Manually Creating an IAM Role for SQL Server Audit](#Appendix.SQLServer.Options.Audit.IAM)\. 
   + For **Select S3 destination**, if you already have an S3 bucket that you want to use, choose it\. To create an S3 bucket, choose **Create a New S3 Bucket**\. 
   + For **Enable Compression**, leave this option checked to compress audit files\. Compression is enabled by default\. To disable compression, clear **Enable Compression**\. 
   + For **Audit log retention**, to keep audit records on the DB instance, choose this option\. Specify a retention time in hours\. The maximum retention time is 35 days\.

1. Apply the option group to a new or existing DB instance\. Choose one of the following:
   + If you are creating a new DB instance, apply the option group when you launch the instance\. 
   + On an existing DB instance, apply the option group by modifying the instance and then attaching the new option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

### Modifying the SQL Server Audit Option<a name="Appendix.SQLServer.Options.Audit.Modifying"></a>

After you enable the SQL Server Audit option, you can modify the settings\. For information about how to modify option settings, see [Modifying an Option Setting](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.ModifyOption)\.

### Removing SQL Server Audit from the DB Instance Options<a name="Appendix.SQLServer.Options.Audit.Removing"></a>

You can turn off the SQL Server Audit feature by disabling audits and then deleting the option\. 

**To remove auditing**

1. Disable all of the audit settings inside SQL Server\. To learn where audits are running, query the SQL Server security catalog views\. For more information, see [Security Catalog Views](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql) in the Microsoft SQL Server documentation\. 

1. Delete the SQL Server Audit option from the DB instance\. Choose one of the following: 
   + Delete the SQL Server Audit option from the option group that the DB instance uses\. This change affects all DB instances that use the same option group\. For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\.
   + Modify the DB instance, and then choose an option group without the SQL Server Audit option\. This change affects only the DB instance that you modify\. You can specify the default \(empty\) option group, or a different custom option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

1. After you delete the SQL Server Audit option from the DB instance, you don't need to restart the instance\. Remove unneeded audit files from your S3 bucket\.

## Using SQL Server Audit<a name="Appendix.SQLServer.Options.Audit.CreateAuditsAndSpecifications"></a>

You can control server audits, server audit specifications, and database audit specifications the same way that you control them for on\-premises database servers\.

### Creating Audits<a name="Appendix.SQLServer.Options.Audit.CreateAudits"></a>

You create server audits in the same way that you create them for on\-premises database servers\. For information about how to create server audits, see [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) in the Microsoft SQL Server documentation\.

To avoid errors, adhere to the following limitations:
+ Don't exceed the maximum number of supported server audits per instance of 50\. 
+ Instruct SQL Server to write data to a binary file\.
+ Don't use `RDS_` as a prefix in the server audit name\.
+ For `FILEPATH`, specify `D:\rdsdbdata\SQLAudit`\.
+ For `MAXSIZE`, specify a size between 2 MB and 50 MB\.
+ Don't configure `MAX_ROLLOVER_FILES` or `MAX_FILES`\.
+ Don't configure SQL Server to shut down the DB instance if it fails to write the audit record\.

### Creating Audit Specifications<a name="Appendix.SQLServer.Options.Audit.CreateSpecifications"></a>

You create server audit specifications and database audit specifications the same way that you create them for on\-premises database servers\. For information about creating audit specifications, see [CREATE SERVER AUDIT SPECIFICATION](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql) and [CREATE DATABASE AUDIT SPECIFICATION](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql) in the Microsoft SQL Server documentation\.

To avoid errors, don't use `RDS_` as a prefix in the name of the database audit specification or server audit specification\. 

## Viewing Audit Logs<a name="Appendix.SQLServer.Options.Audit.AuditRecords"></a>

Your audit logs are stored in `D:\rdsdbdata\SQLAudit`\.

After SQL Server finishes writing to an audit log file—when the file reaches its size limit—Amazon RDS uploads the file to your S3 bucket\. If retention is enabled, Amazon RDS moves the file into the retention folder: `D:\rdsdbdata\SQLAudit\transmitted`\. 

For information about configuring retention, see [Adding SQL Server Audit to the DB Instance Options](#Appendix.SQLServer.Options.Audit.Adding)\.

Audit records are kept on the DB instance until the audit log file is uploaded\. You can view the audit records by running the following command\.

```
SELECT   * 
	FROM     msdb.dbo.rds_fn_get_audit_file
	             ('D:\rdsdbdata\SQLAudit\*.sqlaudit'
	             , default
	             , default )
```

You can use the same command to view audit records in your retention folder by changing the filter to `D:\rdsdbdata\SQLAudit\transmitted\*.sqlaudit`\.

```
SELECT   * 
	FROM     msdb.dbo.rds_fn_get_audit_file
	             ('D:\rdsdbdata\SQLAudit\transmitted\*.sqlaudit'
	             , default
	             , default )
```

## Using SQL Server Audit with Multi\-AZ Instances<a name="Appendix.SQLServer.Options.Audit.Multi-AZ"></a>

For Multi\-AZ instances, the process for sending audit log files to Amazon S3 is similar to the process for Single\-AZ instances\. However, there are some important differences: 
+ Database audit specification objects are replicated to all nodes\.
+ Server audits and server audit specifications aren't replicated to secondary nodes\. Instead, you have to create or modify them manually\.

To capture server audits or a server audit specification from both nodes:

1. Create a server audit or a server audit specification on the primary node\.

1. Fail over to the secondary node and create a server audit or a server audit specification with the same name and GUID on the secondary node\. Use the `AUDIT_GUID` parameter to specify the GUID\.

## Configuring an S3 Bucket<a name="Appendix.SQLServer.Options.Audit.S3bucket"></a>

The audit log files are automatically uploaded from the DB instance to your S3 bucket\. The following restrictions apply to the S3 bucket that you use as a target for audit files: 
+ It must be in the same AWS Region as the DB instance\.
+ It must not be open to the public\.
+ The bucket owner must also be the IAM role owner\.

The target key that is used to store the data follows this naming schema: `bucket-name/key-prefix/instance-name/audit-name/node_file-name.ext` 

**Note**  
You set both the bucket name and the key prefix values with the \(`S3_BUCKET_ARN` option setting\.

The schema is composed of the following elements:
+ **`bucket-name`** – The name of your S3 bucket\.
+ **`key-prefix`** – The custom key prefix you want to use for audit logs\.
+ **`instance-name`** – The name of your Amazon RDS instance\.
+ **`audit-name`** – The name of the audit\.
+ **`node`** – The identifier of the node that is the source of the audit logs \(`node1` or `node2`\)\. There is one node for a Single\-AZ instance and two replication nodes for a Multi\-AZ instance\. These are not primary and secondary nodes, because the roles of primary and secondary change over time\. Instead, the node identifier is a simple label\. 
  + **`node1`** – The first replication node \(Single\-AZ has one node only\)\.
  + **`node2`** – The second replication node \(Multi\-AZ has two nodes\)\.
+ **`file-name`** – The target file name\. The file name is taken as\-is from SQL Server\.
+ **`ext`** – The extension of the file \(`zip` or `sqlaudit`\):
  + **`zip`** – If compression is enabled \(default\)\.
  + **`sqlaudit`** – If compression is disabled\.

## Manually Creating an IAM Role for SQL Server Audit<a name="Appendix.SQLServer.Options.Audit.IAM"></a>

Typically, when you create a new option, the AWS Management Console creates the IAM role and the IAM trust policy for you\. However, you can manually create a new IAM role to use with SQL Server Audits, so that you can customize it with any additional requirements you might have\. To do this, you create an IAM role and delegate permissions so that the Amazon RDS service can use your Amazon S3 bucket\. When you create this IAM role, you attach trust and permissions policies\. The trust policy allows Amazon RDS to assume this role\. The permission policy defines the actions that this role can do\. For more information, see [Creating a Role to Delegate Permissions to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *AWS Identity and Access Management User Guide*\. 

You can use the examples in this section to create the trust and permissions policies you need\.

The following example shows a trust policy for SQL Server Audit\. The policy uses the *service principal* `rds.amazonaws.com` to allow RDS to write to the S3 bucket\. A *service principal* is an identifier that is used to grant permissions to a service\. Anytime you allow access to `rds.amazonaws.com` in this way, you are allowing RDS to perform an action on your behalf\. For more information about service principals, see [AWS JSON Policy Elements: Principal](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html)\.

```
{
	    "Version": "2012-10-17",
	    "Statement": [
	        {
	            "Effect": "Allow",
	            "Principal": {
	                "Service": "rds.amazonaws.com"
	            },
	            "Action": "sts:AssumeRole"
	        }
	    ]
	}
```

In the following example of a permissions policy for SQL Server Audit, we specify an Amazon Resource Name \(ARN\) for the Amazon S3 bucket\. You can use ARNs to identify a specific account, user, or role that you want grant access to\. For more information about using ARNs, see [ Amazon Resource Names \(ARNs\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html)\. 

```
{
	    "Version": "2012-10-17",
	    "Statement": [
	        {
	            "Effect": "Allow",
	            "Action": "s3:ListAllMyBuckets",
	            "Resource": "*"
	        },
	        {
	            "Effect": "Allow",
	            "Action": [
	                "s3:ListBucket",
	                "s3:GetBucketACL",
	                "s3:GetBucketLocation"
	            ],
	            "Resource": "arn:aws:s3:::bucket_name"
	        },
	        {
	            "Effect": "Allow",
	            "Action": [
	                "s3:PutObject",
	                "s3:ListMultipartUploadParts",
	                "s3:AbortMultipartUpload"
	            ],
	            "Resource": "arn:aws:s3:::bucket_name/key_prefix/*"
	        }
	    ]
	}
```