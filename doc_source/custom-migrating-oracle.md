# Migrating an on\-premises database to RDS Custom for Oracle<a name="custom-migrating-oracle"></a>

Before you migrate an on\-premises Oracle database to RDS Custom for Oracle, you need to consider the following factors:
+ The amount of downtime the application can afford
+ The size of the source database
+ Network connectivity
+ A requirement for a fallback plan
+ The source and target Oracle database version and DB instance OS types
+ Available replication tools, such as AWS Database Migration Service, Oracle GoldenGate, or third\-party replication tools

Based on these factors, you can choose physical migration, logical migration, or a combination\. If you choose physical migration, you can use the following techniques:

RMAN duplication  
Active database duplication doesn’t require a backup of your source database\. It duplicates the live source database to the destination host by copying database files over the network to the auxiliary instance\. The RMAN `DUPLICATE` command copies the required files as image copies or backup sets\. To learn this technique, see the AWS blog post [Physical migration of Oracle databases to Amazon RDS Custom using RMAN duplication](http://aws.amazon.com/blogs/database/physical-migration-of-oracle-databases-to-amazon-rds-custom-using-rman-duplication/)\.

Oracle Data Guard  
In this technique, you back up a primary on\-premises database and copy the backups to an Amazon S3 bucket\. You then copy the backups to your RDS Custom for Oracle standby DB instance\. After performing the necessary configuration, you manually switch over your primary database to your RDS Custom for Oracle standby database\. To learn this technique, see the AWS blog post [Physical migration of Oracle databases to Amazon RDS Custom using Data Guard](http://aws.amazon.com/blogs/database/physical-migration-of-oracle-databases-to-amazon-rds-custom-using-data-guard/)\.

For general information about logically importing data into RDS for Oracle, see [Importing data into Oracle on Amazon RDS](Oracle.Procedural.Importing.md)\.