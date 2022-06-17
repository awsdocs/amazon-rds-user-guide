# MySQL security on Amazon RDS<a name="MysQL.Concepts.UsersAndPrivileges"></a>

Security for MySQL DB instances is managed at three levels:
+ AWS Identity and Access Management controls who can perform Amazon RDS management actions on DB instances\. When you connect to AWS using IAM credentials, your IAM account must have IAM policies that grant the permissions required to perform Amazon RDS management operations\. For more information, see [Identity and access management for Amazon RDS](UsingWithRDS.IAM.md)\. 
+ When you create a DB instance, you use a VPC security group to control which devices and Amazon EC2 instances can open connections to the endpoint and port of the DB instance\. These connections can be made using Secure Sockets Layer \(SSL\) and Transport Layer Security \(TLS\)\. In addition, firewall rules at your company can control whether devices running at your company can open connections to the DB instance\. 
+ To authenticate login and permissions for a MySQL DB instance, you can take either of the following approaches, or a combination of them\. 

  You can take the same approach as with a stand\-alone instance of MySQL\. Commands such as `CREATE USER`, `RENAME USER`, `GRANT`, `REVOKE`, and `SET PASSWORD` work just as they do in on\-premises databases, as does directly modifying database schema tables\. For information, see [ Access control and account management](https://dev.mysql.com/doc/refman/8.0/en/access-control.html) in the MySQL documentation\. 

  You can also use IAM database authentication\. With IAM database authentication, you authenticate to your DB instance by using an IAM user or IAM role and an authentication token\. An *authentication token* is a unique value that is generated using the Signature Version 4 signing process\. By using IAM database authentication, you can use the same credentials to control access to your AWS resources and your databases\. For more information, see [IAM database authentication for MariaDB, MySQL, and PostgreSQL](UsingWithRDS.IAMDBAuth.md)\. 

  Another option is Kerberos authentication for RDS for MySQL\. The DB instance works with AWS Directory Service for Microsoft Active Directory \(AWS Managed Microsoft AD\) to enable Kerberos authentication\. When users authenticate with a MySQL DB instance joined to the trusting domain, authentication requests are forwarded\. Forwarded requests go to the domain directory that you create with AWS Directory Service\. For more information, see [Using Kerberos authentication for MySQL](mysql-kerberos.md)\.

 When you create an Amazon RDS DB instance, the master user has the following default privileges: 
+ `alter`
+ `alter routine`
+ `create`
+ `create routine`
+ `create temporary tables`
+ `create user`
+ `create view`
+ `delete`
+ `drop`
+ `event`
+ `execute`
+ `grant option`
+ `index`
+ `insert`
+ `lock tables`
+ `process`
+ `references`
+ `replication client`
+ `replication slave`
+ `select`
+ `show databases`
+ `show view`
+ `trigger`
+ `update`

**Note**  
Although it is possible to delete the master user on the DB instance, it is not recommended\. To recreate the master user, use the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) RDS API operation or the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) AWS CLI command and specify a new master user password with the appropriate parameter\. If the master user does not exist in the instance, the master user is created with the specified password\. 

To provide management services for each DB instance, the `rdsadmin` user is created when the DB instance is created\. Attempting to drop, rename, change the password, or change privileges for the `rdsadmin` account will result in an error\. 

To allow management of the DB instance, the standard `kill` and `kill_query` commands have been restricted\. The Amazon RDS commands `rds_kill` and `rds_kill_query` are provided to allow you to end user sessions or queries on DB instances\. 