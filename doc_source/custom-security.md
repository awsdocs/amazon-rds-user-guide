# Security in Amazon RDS Custom<a name="custom-security"></a>

Familiarize yourself with the security considerations for RDS Custom\.

**Topics**
+ [How RDS Custom securely manages tasks on your behalf](#custom-security.security-tools)
+ [Securing your Amazon S3 bucket against the confused deputy problem](#custom-security.confused-deputy)
+ [Rotating RDS Custom for Oracle credentials for compliance programs](#custom-security.cred-rotation)

## How RDS Custom securely manages tasks on your behalf<a name="custom-security.security-tools"></a>

RDS Custom uses the following tools and techniques to secure your DB instance:

**AWSServiceRoleForRDSCustom service\-linked role**  
A *service\-linked role* is predefined by the service and includes all permissions that the service needs to call other AWS services on your behalf\. For RDS Custom, `AWSServiceRoleForRDSCustom` is a service\-linked role that is defined according to the principle of least privilege\. RDS Custom uses the permissions in `AmazonRDSCustomServiceRolePolicy`, which is the policy attached to this role, to perform most provisioning and all off\-host management tasks\. For more information, see [AmazonRDSCustomServiceRolePolicy](https://docs.aws.amazon.com/aws-managed-policy/latest/reference/AmazonRDSCustomServiceRolePolicy.html)\.  
When it performs tasks on the host, RDS Custom automation uses credentials from the service\-linked role to run commands using AWS Systems Manager\. You can audit the command history through the Systems Manager command history and AWS CloudTrail\. Systems Manager connects to your RDS Custom DB instance using your networking setup\. For more information, see [Step 3: Configure IAM and your Amazon VPC](custom-setup-orcl.md#custom-setup-orcl.iam-vpc)\.

**Temporary IAM credentials**  
When provisioning or deleting resources, RDS Custom sometimes uses temporary credentials derived from the credentials of the calling IAM principal\. These IAM credentials are restricted by the IAM policies attached to that principal and expire after the operation is completed\. To learn about the permissions required for IAM principals who use RDS Custom, see [Step 4: Grant required permissions to your IAM user or role](custom-setup-orcl.md#custom-setup-orcl.iam-user)\.

**Amazon EC2 instance profile**  
An EC2 instance profile is a container for an IAM role that you can use to pass role information to an EC2 instance\. An EC2 instance underlies an RDS Custom DB instance\. You provide an instance profile when you create an RDS Custom DB instance\. RDS Custom uses EC2 instance profile credentials when it performs host\-based management tasks such as backups\. For more information, see [Create your IAM role and instance profile manually](custom-setup-orcl.md#custom-setup-orcl.iam)\.

**SSH key pair**  
When RDS Custom creates the EC2 instance that underlies a DB instance, it creates an SSH key pair on your behalf\. The key uses the naming prefix `do-not-delete-rds-custom-ssh-privatekey-db-`\. AWS Secrets Manager stores this SSH private key as a secret in your AWS account\. Amazon RDS doesn't store, access, or use these credentials\. For more information, see [Amazon EC2 key pairs and Linux instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)\.

## Securing your Amazon S3 bucket against the confused deputy problem<a name="custom-security.confused-deputy"></a>

When you create an Amazon RDS Custom for Oracle custom engine version \(CEV\) or an RDS Custom for SQL Server DB instance, RDS Custom creates an Amazon S3 bucket\. The S3 bucket stores files such as CEV artifacts, redo \(transaction\) logs, configuration items for the support perimeter, and AWS CloudTrail logs\.

You can make these S3 buckets more secure by using the global condition context keys to prevent the *confused deputy problem*\. For more information, see [Preventing cross\-service confused deputy problems](cross-service-confused-deputy-prevention.md)\.

The following RDS Custom for Oracle example shows the use of the `aws:SourceArn` and `aws:SourceAccount` global condition context keys in an S3 bucket policy\. For RDS Custom for Oracle, make sure to include the Amazon Resource Names \(ARNs\) for the CEVs and the DB instances\. For RDS Custom for SQL Server, make sure to include the ARN for the DB instances\.

```
...
{
  "Sid": "AWSRDSCustomForOracleInstancesObjectLevelAccess",
  "Effect": "Allow",
  "Principal": {
     "Service": "custom.rds.amazonaws.com"
  },
  "Action": [
     "s3:GetObject",
     "s3:GetObjectVersion",
     "s3:DeleteObject",
     "s3:DeleteObjectVersion",
     "s3:GetObjectRetention",
     "s3:BypassGovernanceRetention"
  ],
  "Resource": "arn:aws:s3:::do-not-delete-rds-custom-123456789012-us-east-2-c8a6f7/RDSCustomForOracle/Instances/*",
  "Condition": {
     "ArnLike": {
        "aws:SourceArn": [
            "arn:aws:rds:us-east-2:123456789012:db:*",
            "arn:aws:rds:us-east-2:123456789012:cev:*/*"
        ]
     },
     "StringEquals": {
        "aws:SourceAccount": "123456789012"
    }
  }
},
...
```

## Rotating RDS Custom for Oracle credentials for compliance programs<a name="custom-security.cred-rotation"></a>

Some compliance programs require database user credentials to change periodically, for example, every 90 days\. RDS Custom for Oracle automatically rotates credentials for some predefined database users\.

**Topics**
+ [Automatic rotation of credentials for predefined users](#custom-security.cred-rotation.auto)
+ [Guidelines for rotating user credentials](#custom-security.cred-rotation.guidelines)
+ [Rotating user credentials manually](#custom-security.cred-rotation.manual)

### Automatic rotation of credentials for predefined users<a name="custom-security.cred-rotation.auto"></a>

If your RDS Custom for Oracle DB instance is hosted in Amazon RDS, credentials for the following predefined Oracle users rotate every 30 days automatically\. Credentials for the preceding users reside in AWS Secrets Manager\.


**Predefined Oracle users**  

| Database user | Created by | Supported engine versions | Notes | 
| --- | --- | --- | --- | 
| SYS | Oracle | custom\-oracle\-ee and custom\-oracle\-ee\-cdb |  | 
| SYSTEM | Oracle | custom\-oracle\-ee and custom\-oracle\-ee\-cdb |  | 
| RDSADMIN | RDS | custom\-oracle\-ee |  | 
| C\#\#RDSADMIN | RDS | custom\-oracle\-ee\-cdb | Usernames with a C\#\# prefix exist only in CDBs\. For more information about CDBs, see [Overview of Amazon RDS Custom for Oracle architecture](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-creating.html#custom-creating.overview)\. | 
| RDS\_DATAGUARD | RDS | custom\-oracle\-ee | This user exists only in read replicas, source databases for read replicas, and databases that you have physically migrated into RDS Custom using Oracle Data Guard\. | 
| C\#\#RDS\_DATAGUARD | RDS | custom\-oracle\-ee\-cdb | This user exists only in read replicas, source databases for read replicas, and databases that you have physically migrated into RDS Custom using Oracle Data Guard\. Usernames with a C\#\# prefix exist only in CDBs\. For more information about CDBs, see [Overview of Amazon RDS Custom for Oracle architecture](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-creating.html#custom-creating.overview)\. | 

An exception to the automatic credential rotation is an RDS Custom for Oracle DB instance that you have manually configured as a standby database\. RDS only rotates credentials for read replicas that you have created using the `create-db-instance-read-replica` CLI command or `CreateDBInstanceReadReplica` API\.

### Guidelines for rotating user credentials<a name="custom-security.cred-rotation.guidelines"></a>

To make sure that your credentials rotate according to your compliance program, note the following guidelines:
+ If your DB instance rotates credentials automatically, don't manually change or delete a secret, password file, or password for users listed in [Predefined Oracle users](#auto-rotation)\. Otherwise, RDS Custom might place your DB instance outside of the support perimeter, which suspends automatic rotation\.
+ The RDS master user is not predefined, so you are responsible for either changing the password manually or setting up automatic rotation in Secrets Manager\. For more information, see [Rotate AWS Secrets Manager secrets](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets.html)\.

### Rotating user credentials manually<a name="custom-security.cred-rotation.manual"></a>

For the following categories of databases, RDS doesn't automatically rotate the credentials for the users listed in [Predefined Oracle users](#auto-rotation):
+ A database that you configured manually to function as a standby database\.
+ An on\-premises database\.
+ A DB instance that is outside of the support perimeter or in a state in which the RDS Custom automation can't run\. In this case, RDS Custom also doesn't rotate keys\.

If your database is in any of the preceding categories, you must rotate your user credentials manually\.

**To rotate user credentials manually for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In **Databases**, make sure that RDS isn't currently backing up your DB instance or performing operations such configuring high availability\.

1. In the database details page, choose **Configuration** and note the Resource ID for the DB instance\. Or you can use the AWS CLI command `describe-db-instances`\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. In the search box, enter your DB Resource ID and find the secret in the following form:

   ```
   do-not-delete-rds-custom-db-resource-id-numeric-string
   ```

   This secret stores the password for `RDSADMIN`, `SYS`, and `SYSTEM`\. The following sample key is for the DB instance with the DB resource ID `db-ABCDEFG12HIJKLNMNOPQRS3TUVWX`:

   ```
   do-not-delete-rds-custom-db-ABCDEFG12HIJKLNMNOPQRS3TUVWX-123456
   ```
**Important**  
If your DB instance is a read replica and uses the `custom-oracle-ee-cdb` engine, two secrets exist with the suffix `db-resource-id-numeric-string`, one for the master user and the other for `RDSADMIN`, `SYS`, and `SYSTEM`\. To find the correct secret, run the following command on the host:  

   ```
   cat /opt/aws/rdscustomagent/config/database_metadata.json | python3 -c "import sys,json; print(json.load(sys.stdin)['dbMonitoringUserPassword'])"
   ```
The `dbMonitoringUserPassword` attribute indicates the secret for `RDSADMIN`, `SYS`, and `SYSTEM`\.

1. If your DB instance exists in an Oracle Data Guard configuration, find the secret in the following form:

   ```
   do-not-delete-rds-custom-db-resource-id-numeric-string-dg
   ```

   This secret stores the password for `RDS_DATAGUARD`\. The following sample key is for the DB instance with the DB resource ID `db-ABCDEFG12HIJKLNMNOPQRS3TUVWX`:

   ```
   do-not-delete-rds-custom-db-ABCDEFG12HIJKLNMNOPQRS3TUVWX-789012-dg
   ```

1. For all database users listed in [Predefined Oracle users](#auto-rotation), update the passwords by following the instructions in [Modify an AWS Secrets Manager secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_update-secret.html)\.

1. If your database is a standalone database or a source database in an Oracle Data Guard configuration:

   1. Start your Oracle SQL client and log in as `SYS`\.

   1. Run a SQL statement in the following form for each database user listed in [Predefined Oracle users](#auto-rotation):

      ```
      ALTER USER user-name IDENTIFIED BY pwd-from-secrets-manager ACCOUNT UNLOCK;
      ```

      For example, if the new password for `RDSADMIN` stored in Secrets Manager is `pwd-123`, run the following statement:

      ```
      ALTER USER RDSADMIN IDENTIFIED BY pwd-123 ACCOUNT UNLOCK;
      ```

1. If your DB instance runs Oracle Database 12c Release 1 \(12\.1\) and is managed by Oracle Data Guard, manually copy the password file \(`orapw`\) from the primary DB instance to each standby DB instance\. 

   If your DB instance is hosted in Amazon RDS, the password file location is `/rdsdbdata/config/orapw`\. For databases that aren't hosted in Amazon RDS, the default location is `$ORACLE_HOME/dbs/orapw$ORACLE_SID` on Linux and UNIX and `%ORACLE_HOME%\database\PWD%ORACLE_SID%.ora` on Windows\.