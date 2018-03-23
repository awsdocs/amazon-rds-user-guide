# Security with Amazon Aurora MySQL<a name="AuroraMySQL.Security"></a>

Security for Amazon Aurora MySQL is managed at three levels:
+ To control who can perform Amazon RDS management actions on Aurora MySQL DB clusters and DB instances, you use AWS Identity and Access Management \(IAM\)\. When you connect to AWS using IAM credentials, your IAM account must have IAM policies that grant the permissions required to perform Amazon RDS management operations\. For more information, see [Authentication and Access Control for Amazon RDS](UsingWithRDS.IAM.md)\.

  If you are using an IAM account to access the Amazon RDS console, you must first log on to the AWS Management Console with your IAM account\. You then go to the Amazon RDS console at [https://console\.aws\.amazon\.com/rds](https://console.aws.amazon.com/rds)\.
+ Aurora MySQL DB clusters must be created in an Amazon Virtual Private Cloud \(VPC\)\. To control which devices and Amazon EC2 instances can open connections to the endpoint and port of the DB instance for Aurora MySQL DB clusters in a VPC, you use a VPC security group\. These endpoint and port connections can be made using Secure Sockets Layer \(SSL\)\. In addition, firewall rules at your company can control whether devices running at your company can open connections to a DB instance\. For more information on VPCs, see [Amazon Virtual Private Cloud \(VPCs\) and Amazon RDS](USER_VPC.md)\.

  The supported VPC tenancy depends on the instance class used by your Aurora MySQL DB clusters\. With `default` VPC tenancy, the VPC runs on shared hardware\. With `dedicated` VPC tenancy, the VPC runs on a dedicated hardware instance\. Aurora MySQL supports the following VPC tenancy based on the instance class:
  + The db\.r3 instance classes support both `default` and `dedicated` VPC tenancy\.
  + The db\.r4 instance classes support `default` VPC tenancy only\.
  + The db\.t2 instance classes support `default` VPC tenancy only\.

  For more information about instance classes, see [DB Instance Class](Concepts.DBInstanceClass.md)\. For more information about `default` and `dedicated` VPC tenancy, see [Dedicated Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-instance.html) in the *Amazon Elastic Compute Cloud User Guide*\.
+ To authenticate login and permissions for an Amazon Aurora MySQL DB cluster, you can take either of the following approaches, or a combination of them:
  + You can take the same approach as with a standalone instance of MySQL\.

    Commands such as `CREATE USER`, `RENAME USER`, `GRANT`, `REVOKE`, and `SET PASSWORD` work just as they do in on\-premises databases, as does directly modifying database schema tables\. For more information, see [ MySQL User Account Management](http://dev.mysql.com/doc/mysql-security-excerpt/5.6/en/user-account-management.html) in the MySQL documentation\.
  + You can also use IAM database authentication\.

    With IAM database authentication, you authenticate to your DB cluster by using an IAM user or IAM role and an authentication token\. An *authentication token* is a unique value that is generated using the Signature Version 4 signing process\. By using IAM database authentication, you can use the same credentials to control access to your AWS resources and your databases\. For more information, see [IAM Database Authentication for MySQL and Amazon Aurora](UsingWithRDS.IAMDBAuth.md)\.

When you create an Amazon Aurora MySQL DB instance, the master user has the following default privileges:
+  `ALTER` 
+  `ALTER ROUTINE` 
+  `CREATE` 
+  `CREATE ROUTINE` 
+  `CREATE TEMPORARY TABLES` 
+  `CREATE USER` 
+  `CREATE VIEW` 
+  `DELETE` 
+  `DROP` 
+  `EVENT` 
+  `EXECUTE` 
+  `GRANT OPTION` 
+  `INDEX` 
+  `INSERT` 
+  `LOAD FROM S3` 
+  `LOCK TABLES` 
+  `PROCESS` 
+  `REFERENCES` 
+  `RELOAD` 
+  `REPLICATION CLIENT` 
+  `REPLICATION SLAVE` 
+  `SELECT` 
+  `SHOW DATABASES` 
+  `SHOW VIEW` 
+  `TRIGGER` 
+  `UPDATE` 

To provide management services for each DB cluster, the `rdsadmin` user is created when the DB cluster is created\. Attempting to drop, rename, change the password, or change privileges for the `rdsadmin` account results in an error\.

For management of the Aurora MySQL DB cluster, the standard `kill` and `kill_query` commands have been restricted\. Instead, use the Amazon RDS commands `rds_kill` and `rds_kill_query` to terminate user sessions or queries on Aurora MySQL DB instances\. 

## Using SSL with Aurora MySQL DB Clusters<a name="AuroraMySQL.Security.SSL"></a>

Amazon Aurora MySQL DB clusters support Secure Sockets Layer \(SSL\) connections from applications using the same process and public key as Amazon RDS MySQL DB instances\.

Amazon RDS creates an SSL certificate and installs the certificate on the DB instance when Amazon RDS provisions the instance\. These certificates are signed by a certificate authority\. The SSL certificate includes the DB instance endpoint as the Common Name \(CN\) for the SSL certificate to guard against spoofing attacks\. As a result, you can only use the DB cluster endpoint to connect to a DB cluster using SSL if your client supports Subject Alternative Names \(SAN\)\. Otherwise, you must use the endpoint of the primary instance\. 

We recommend the MariaDB Connector/J client as a client that supports SAN with SSL\. For more information, see the [MariaDB Connector/J download](https://downloads.mariadb.org/connector-java/) page\.

The public key is stored at [https://s3\.amazonaws\.com/rds\-downloads/rds\-combined\-ca\-bundle\.pem](https://s3.amazonaws.com/rds-downloads/rds-combined-ca-bundle.pem)\. 

To encrypt connections using the default `mysql` client, launch the mysql client using the `--ssl-ca parameter` to reference the public key, for example: 

For MySQL 5\.7 and later:

```
mysql -h myinstance.c9akciq32.rds-us-east-1.amazonaws.com
--ssl-ca=[full path]rds-combined-ca-bundle.pem --ssl-mode=VERIFY_IDENTITY
```

For MySQL 5\.6 and earlier:

```
mysql -h myinstance.c9akciq32.rds-us-east-1.amazonaws.com
--ssl-ca=[full path]rds-combined-ca-bundle.pem --ssl-verify-server-cert
```

You can require SSL connections for specific users accounts\. For example, you can use one of the following statements, depending on your MySQL version, to require SSL connections on the user account `encrypted_user`\.

For MySQL 5\.7 and later:

```
ALTER USER 'encrypted_user'@'%' REQUIRE SSL;            
```

For MySQL 5\.6 and earlier:

```
GRANT USAGE ON *.* TO 'encrypted_user'@'%' REQUIRE SSL;            
```

**Note**  
For more information on SSL connections with MySQL, see the [MySQL documentation](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html)\.