# IAM Database Authentication for MySQL and PostgreSQL<a name="UsingWithRDS.IAMDBAuth"></a>

You can authenticate to your DB instance using AWS Identity and Access Management \(IAM\) database authentication\. IAM database authentication works with MySQL and PostgreSQL\. With this authentication method, you don't need to use a password when you connect to a DB instance\. Instead, you use an authentication token\.

An *authentication token* is a unique string of characters that Amazon RDS generates on request\. Authentication tokens are generated using AWS Signature Version 4\. Each token has a lifetime of 15 minutes\. You don't need to store user credentials in the database, because authentication is managed externally using IAM\. You can also still use standard database authentication\.

IAM database authentication provides the following benefits:
+ Network traffic to and from the database is encrypted using Secure Sockets Layer \(SSL\)\.
+ You can use IAM to centrally manage access to your database resources, instead of managing access individually on each DB instance\.
+ For applications running on Amazon EC2, you can use profile credentials specific to your EC2 instance to access your database instead of a password, for greater security\.

**Topics**
+ [Availability for IAM Database Authentication](#UsingWithRDS.IAMDBAuth.Availability)
+ [MySQL Limitations for IAM Database Authentication](#UsingWithRDS.IAMDBAuth.ConnectionsPerSecond)
+ [PostgreSQL Limitations for IAM Database Authentication](#UsingWithRDS.IAMDBAuth.LimitsPostgreSQL)
+ [Enabling and Disabling IAM Database Authentication](UsingWithRDS.IAMDBAuth.Enabling.md)
+ [Creating and Using an IAM Policy for IAM Database Access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)
+ [Creating a Database Account Using IAM Authentication](UsingWithRDS.IAMDBAuth.DBAccounts.md)
+ [Connecting to Your DB Instance Using IAM Authentication](UsingWithRDS.IAMDBAuth.Connecting.md)

## Availability for IAM Database Authentication<a name="UsingWithRDS.IAMDBAuth.Availability"></a>

IAM database authentication is available for the following database engines and DB instance classes:
+ MySQL 5\.6, minor version 5\.6\.34 or higher\. 
+ MySQL 5\.7, minor version 5\.7\.16 or higher\. 
+ MySQL 8\.0, minor version 8\.0\.16 or higher\. 
+ PostgreSQL versions 10\.6 or higher, 9\.6\.11 or higher, and 9\.5\.15 or higher\.

**Note**  
IAM database authentication is not supported for MySQL 5\.5\.

## MySQL Limitations for IAM Database Authentication<a name="UsingWithRDS.IAMDBAuth.ConnectionsPerSecond"></a>

When using IAM database authentication with MySQL, you are limited to a maximum of 200 new connections per second\. If you are using a db\.t2\.micro DB instance class, the limit is 10 connections per second\.

The database engines that work with Amazon RDS don't impose any limits on authentication attempts per second\. However, when you use IAM database authentication, your application must generate an authentication token\. Your application then uses that token to connect to the DB instance\. If you exceed the limit of maximum new connections per second, then the extra overhead of IAM database authentication can cause connection throttling\. The extra overhead can cause even existing connections to drop\.  For information about the maximum total connections for MySQL, see [Maximum MySQL and MariaDB Connections](CHAP_Troubleshooting.md#USER_ConnectToInstance.max_connections)\.   

We recommend the following when using the MySQL engine:
+ Use IAM database authentication as a mechanism for temporary, personal access to databases\.
+ Use IAM database authentication only for workloads that can be easily retried\.
+ Don't use IAM database authentication if your application requires more than 200 new connections per second\.

## PostgreSQL Limitations for IAM Database Authentication<a name="UsingWithRDS.IAMDBAuth.LimitsPostgreSQL"></a>

When using IAM database authentication with PostgreSQL, note the following limitation:
+ The maximum number of connections per second for your database instance may be limited depending on the instance type and your workload\.