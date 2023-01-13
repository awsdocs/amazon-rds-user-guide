# Amazon RDS for MySQL<a name="CHAP_MySQL"></a>

Amazon RDS supports DB instances that run the following versions of MySQL:
+ MySQL 8\.0
+ MySQL 5\.7

For more information about minor version support, see [MySQL on Amazon RDS versions](MySQL.Concepts.VersionMgmt.md)\.

To create an Amazon RDS for MySQL DB instance, use the Amazon RDS management tools or interfaces\. You can then do the following:
+ Resize your DB instance
+ Authorize connections to your DB instance
+ Create and restore from backups or snapshots
+ Create Multi\-AZ secondaries
+ Create read replicas
+ Monitor the performance of your DB instance

To store and access the data in your DB instance, you use standard MySQL utilities and applications\.

Amazon RDS for MySQL is compliant with many industry standards\. For example, you can use RDS for MySQL databases to build HIPAA\-compliant applications\. You can use RDS for MySQL databases to store healthcare related information, including protected health information \(PHI\) under a Business Associate Agreement \(BAA\) with AWS\. Amazon RDS for MySQL also meets Federal Risk and Authorization Management Program \(FedRAMP\) security requirements\. In addition, Amazon RDS for MySQL has received a FedRAMP Joint Authorization Board \(JAB\) Provisional Authority to Operate \(P\-ATO\) at the FedRAMP HIGH Baseline within the AWS GovCloud \(US\) Regions\. For more information on supported compliance standards, see [AWS cloud compliance](http://aws.amazon.com/compliance/)\.

For information about the features in each version of MySQL, see [The main features of MySQL](https://dev.mysql.com/doc/refman/8.0/en/features.html) in the MySQL documentation\.

Before creating a DB instance, complete the steps in [Setting up for Amazon RDS](CHAP_SettingUp.md)\. When you create a DB instance, the RDS master user gets DBA privileges, with some limitations\. Use this account for administrative tasks such as creating additional database accounts\.

You can create the following:
+ DB instances
+ DB snapshots
+ Point\-in\-time restores
+ Automated backups
+ Manual backups

You can use DB instances running MySQL inside a virtual private cloud \(VPC\) based on Amazon VPC\. You can also add features to your MySQL DB instance by turning on various options\. Amazon RDS supports Multi\-AZ deployments for MySQL as a high\-availability, failover solution\.

**Important**  
To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances\. It also restricts access to certain system procedures and tables that need advanced privileges\. You can access your database using standard SQL clients such as the mysql client\. However, you can't access the host directly by using Telnet or Secure Shell \(SSH\)\.

**Topics**
+ [MySQL feature support on Amazon RDS](MySQL.Concepts.FeatureSupport.md)
+ [MySQL on Amazon RDS versions](MySQL.Concepts.VersionMgmt.md)
+ [Connecting to a DB instance running the MySQL database engine](USER_ConnectToInstance.md)
+ [Securing MySQL DB instance connections](securing-mysql-connections.md)
+ [Improving query performance for RDS for MySQL with Amazon RDS Optimized Reads](rds-optimized-reads.md)
+ [Improving write performance with Amazon RDS Optimized Writes](rds-optimized-writes.md)
+ [Upgrading the MySQL DB engine](USER_UpgradeDBInstance.MySQL.md)
+ [Importing data into a MySQL DB instance](MySQL.Procedural.Importing.Other.md)
+ [Working with MySQL replication in Amazon RDS](USER_MySQL.Replication.md)
+ [Exporting data from a MySQL DB instance by using replication](MySQL.Procedural.Exporting.NonRDSRepl.md)
+ [Options for MySQL DB instances](Appendix.MySQL.Options.md)
+ [Parameters for MySQL](Appendix.MySQL.Parameters.md)
+ [Common DBA tasks for MySQL DB instances](Appendix.MySQL.CommonDBATasks.md)
+ [Local time zone for MySQL DB instances](MySQL.Concepts.LocalTimeZone.md)
+ [Known issues and limitations for Amazon RDS for MySQL](MySQL.KnownIssuesAndLimitations.md)
+ [MySQL on Amazon RDS SQL reference](Appendix.MySQL.SQLRef.md)