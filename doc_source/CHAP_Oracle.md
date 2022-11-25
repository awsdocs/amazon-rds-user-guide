# Amazon RDS for Oracle<a name="CHAP_Oracle"></a>

Amazon RDS supports DB instances that run the following versions and editions of Oracle Database:
+ Oracle Database 21c \(21\.0\.0\.0\)
+ Oracle Database 19c \(19\.0\.0\.0\)

**Note**  
Oracle Database 11g, Oracle Database 12c, and Oracle Database 18c are legacy versions that are no longer supported in Amazon RDS\.

Before creating a DB instance, complete the steps in the [Setting up for Amazon RDS](CHAP_SettingUp.md) section of this guide\. When you create a DB instance using your master account, the account gets DBA privileges, with some limitations\. Use this account for administrative tasks such as creating additional database accounts\. You can't use SYS, SYSTEM, or other Oracle\-supplied administrative accounts\.

You can create the following:
+ DB instances
+ DB snapshots
+ Point\-in\-time restores
+ Automated backups
+ Manual backups

You can use DB instances running Oracle inside a VPC\. You can also add features to your Oracle DB instance by enabling various options\. Amazon RDS supports Multi\-AZ deployments for Oracle as a high\-availability, failover solution\.

**Important**  
To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances\. It also restricts access to certain system procedures and tables that need advanced privileges\. You can access your database using standard SQL clients such as Oracle SQL\*Plus\. However, you can't access the host directly by using Telnet or Secure Shell \(SSH\)\.

**Topics**
+ [Overview of Oracle on Amazon RDS](Oracle.Concepts.overview.md)
+ [Connecting to your Oracle DB instance](USER_ConnectToOracleInstance.md)
+ [Securing Oracle DB instance connections](Oracle.Concepts.RestrictedDBAPrivileges.md)
+ [Administering your Oracle DB instance](Appendix.Oracle.CommonDBATasks.md)
+ [Configuring advanced RDS for Oracle features](CHAP_Oracle.advanced-features.md)
+ [Importing data into Oracle on Amazon RDS](Oracle.Procedural.Importing.md)
+ [Working with read replicas for Amazon RDS for Oracle](oracle-read-replicas.md)
+ [Adding options to Oracle DB instances](Appendix.Oracle.Options.md)
+ [Upgrading the RDS for Oracle DB engine](USER_UpgradeDBInstance.Oracle.md)
+ [Using third\-party software with your RDS for Oracle DB instance](Oracle.Resources.md)
+ [Oracle Database engine release notes](USER_Oracle_Releases.md)