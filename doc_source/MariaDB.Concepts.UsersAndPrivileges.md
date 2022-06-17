# MariaDB security on Amazon RDS<a name="MariaDB.Concepts.UsersAndPrivileges"></a>

Security for MariaDB DB instances is managed at three levels:
+ AWS Identity and Access Management controls who can perform Amazon RDS management actions on DB instances\. When you connect to AWS using IAM credentials, your IAM account must have IAM policies that grant the permissions required to perform Amazon RDS management operations\. For more information, see [Identity and access management for Amazon RDS](UsingWithRDS.IAM.md)\.
+ When you create a DB instance, you use a VPC security group to control which devices and Amazon EC2 instances can open connections to the endpoint and port of the DB instance\. These connections can be made using Secure Socket Layer \(SSL\) and Transport Layer Security \(TLS\)\. In addition, firewall rules at your company can control whether devices running at your company can open connections to the DB instance\.
+ Once a connection has been opened to a MariaDB DB instance, authentication of the login and permissions are applied the same way as in a stand\-alone instance of MariaDB\. Commands such as `CREATE USER`, `RENAME USER`, `GRANT`, `REVOKE`, and `SET PASSWORD` work just as they do in stand\-alone databases, as does directly modifying database schema tables\.

 When you create an Amazon RDS DB instance, the master user has the following default privileges: 
+  `alter` 
+  `alter routine` 
+  `create` 
+  `create routine` 
+  `create temporary tables` 
+  `create user` 
+  `create view` 
+  `delete` 
+  `drop` 
+  `event` 
+  `execute` 
+  `grant option` 
+  `index` 
+  `insert` 
+  `lock tables` 
+  `process` 
+  `references` 
+  `reload` 

  This privilege is limited on MariaDB DB instances\. It doesn't grant access to the FLUSH LOGS or FLUSH TABLES WITH READ LOCK operations\.
+  `replication client` 
+  `replication slave` 
+  `select` 
+  `show databases` 
+  `show view` 
+  `trigger` 
+  `update` 

For more information about these privileges, see [User account management](http://mariadb.com/kb/en/mariadb/grant/) in the MariaDB documentation\.

**Note**  
Although you can delete the master user on a DB instance, we don't recommend doing so\. To recreate the master user, use the `ModifyDBInstance` API or the `modify-db-instance` AWS CLI and specify a new master user password with the appropriate parameter\. If the master user does not exist in the instance, the master user is created with the specified password\. 

To provide management services for each DB instance, the `rdsadmin` user is created when the DB instance is created\. Attempting to drop, rename, change the password for, or change privileges for the `rdsadmin` account results in an error\.

To allow management of the DB instance, the standard `kill` and `kill_query` commands have been restricted\. The Amazon RDS commands `mysql.rds_kill`, `mysql.rds_kill_query`, and `mysql.rds_kill_query_id` are provided for use in MariaDB and also MySQL so that you can end user sessions or queries on DB instances\. 