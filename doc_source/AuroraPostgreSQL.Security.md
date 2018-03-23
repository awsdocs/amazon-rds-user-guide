# Security with Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Security"></a>

Security for Amazon Aurora PostgreSQL is managed at three levels:
+ To control who can perform Amazon RDS management actions on Aurora DB clusters and DB instances, you use AWS Identity and Access Management \(IAM\)\. When you connect to AWS using IAM credentials, your IAM account must have IAM policies that grant the permissions required to perform Amazon RDS management operations\. For more information, see [Authentication and Access Control for Amazon RDS](UsingWithRDS.IAM.md)\.

  If you are using an IAM account to access the Amazon RDS console, you must first log on to the AWS Management Console with your IAM account, and then go to the Amazon RDS console at [https://console\.aws\.amazon\.com/rds](https://console.aws.amazon.com/rds)\.
+ Aurora DB clusters must be created in an Amazon Virtual Private Cloud \(VPC\)\. To control which devices and Amazon EC2 instances can open connections to the endpoint and port of the DB instance for Aurora DB clusters in a VPC, you use a VPC security group\. These endpoint and port connections can be made using Secure Sockets Layer \(SSL\)\. In addition, firewall rules at your company can control whether devices running at your company can open connections to a DB instance\. For more information on VPCs, see [Amazon Virtual Private Cloud \(VPCs\) and Amazon RDS](USER_VPC.md)\.
+ To authenticate login and permissions for an Amazon Aurora DB cluster, you can take the same approach as with a stand\-alone instance of PostgreSQL\.

  Commands such as `CREATE ROLE`, `ALTER ROLE`, `GRANT`, and `REVOKE` work just as they do in on\-premises databases, as does directly modifying database schema tables\. For more information, see [Client Authentication](https://www.postgresql.org/docs/9.6/static/client-authentication.html) in the PostgreSQL documentation\.

When you create an Amazon Aurora PostgreSQL DB instance, the master user has the following default privileges:
+  `LOGIN` 
+  `NOSUPERUSER` 
+  `INHERIT` 
+  `CREATEDB` 
+  `CREATEROLE` 
+  `NOREPLICATION` 
+  `VALID UNTIL 'infinity'` 

To provide management services for each DB cluster, the `rdsadmin` user is created when the DB cluster is created\. Attempting to drop, rename, change the password, or change privileges for the `rdsadmin` account will result in an error\.