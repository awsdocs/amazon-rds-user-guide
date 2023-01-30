# Amazon RDS for MariaDB<a name="CHAP_MariaDB"></a>

Amazon RDS supports DB instances that run the following versions of MariaDB:
+ MariaDB 10\.6
+ MariaDB 10\.5
+ MariaDB 10\.4
+ MariaDB 10\.3 \(end of life scheduled for October 23, 2023\)

For more information about minor version support, see [MariaDB on Amazon RDS versions](MariaDB.Concepts.VersionMgmt.md)\. 

To create a MariaDB DB instance, use the Amazon RDS management tools or interfaces\. You can then use the Amazon RDS tools to perform management actions for the DB instance\. These include actions such as the following: 
+ Reconfiguring or resizing the DB instance
+ Authorizing connections to the DB instance 
+ Creating and restoring from backups or snapshots
+ Creating Multi\-AZ secondaries
+ Creating read replicas
+ Monitoring the performance of your DB instance

To store and access the data in your DB instance, use standard MariaDB utilities and applications\. 

MariaDB is available in all of the AWS Regions\. For more information about AWS Regions, see [Regions, Availability Zones, and Local Zones](Concepts.RegionsAndAvailabilityZones.md)\. 

You can use Amazon RDS for MariaDB databases to build HIPAA\-compliant applications\. You can store healthcare\-related information, including protected health information \(PHI\), under a Business Associate Agreement \(BAA\) with AWS\. For more information, see [HIPAA compliance](http://aws.amazon.com/compliance/hipaa-compliance/)\. AWS Services in Scope have been fully assessed by a third\-party auditor and result in a certification, attestation of compliance, or Authority to Operate \(ATO\)\. For more information, see [AWS services in scope by compliance program](http://aws.amazon.com/compliance/services-in-scope/)\. 

Before creating a DB instance, complete the steps in [Setting up for Amazon RDS](CHAP_SettingUp.md)\. When you create a DB instance, the RDS master user gets DBA privileges, with some limitations\. Use this account for administrative tasks such as creating additional database accounts\.

You can create the following:
+ DB instances
+ DB snapshots
+ Point\-in\-time restores
+ Automated backups
+ Manual backups

You can use DB instances running MariaDB inside a virtual private cloud \(VPC\) based on Amazon VPC\. You can also add features to your MariaDB DB instance by enabling various options\. Amazon RDS supports Multi\-AZ deployments for MariaDB as a high\-availability, failover solution\.

**Important**  
To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances\. It also restricts access to certain system procedures and tables that need advanced privileges\. You can access your database using standard SQL clients such as the mysql client\. However, you can't access the host directly by using Telnet or Secure Shell \(SSH\)\.

**Topics**
+ [MariaDB feature support on Amazon RDS](MariaDB.Concepts.FeatureSupport.md)
+ [MariaDB on Amazon RDS versions](MariaDB.Concepts.VersionMgmt.md)
+ [Connecting to a DB instance running the MariaDB database engine](USER_ConnectToMariaDBInstance.md)
+ [Securing MariaDB DB instance connections](securing-mariadb-connections.md)
+ [Improving query performance for RDS for MariaDB with Amazon RDS Optimized Reads](rds-optimized-reads-mariadb.md)
+ [Upgrading the MariaDB DB engine](USER_UpgradeDBInstance.MariaDB.md)
+ [Importing data into a MariaDB DB instance](MariaDB.Procedural.Importing.md)
+ [Working with MariaDB replication in Amazon RDS](USER_MariaDB.Replication.md)
+ [Options for MariaDB database engine](Appendix.MariaDB.Options.md)
+ [Parameters for MariaDB](Appendix.MariaDB.Parameters.md)
+ [Migrating data from a MySQL DB snapshot to a MariaDB DB instance](USER_Migrate_MariaDB.md)
+ [MariaDB on Amazon RDS SQL reference](Appendix.MariaDB.SQLRef.md)
+ [Local time zone for MariaDB DB instances](MariaDB.Concepts.LocalTimeZone.md)
+ [RDS for MariaDB limitations](CHAP_MariaDB.Limitations.md)