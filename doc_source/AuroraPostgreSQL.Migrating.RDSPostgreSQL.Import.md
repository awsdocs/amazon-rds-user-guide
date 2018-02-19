# Migrating an RDS PostgreSQL Snapshot to Aurora<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Import"></a>

You can migrate a DB snapshot of an Amazon RDS PostgreSQL DB instance to create an Amazon Aurora PostgreSQL DB cluster\. The new Amazon Aurora PostgreSQL DB cluster is populated with the data from the original Amazon RDS PostgreSQL DB instance\. The DB snapshot must have been made from an Amazon RDS DB instance running PostgreSQL version 9\.6\.1 or 9\.6\.3\.

You can migrate either a manual or automated DB snapshot\. After the DB cluster is created, you can then create optional Aurora Replicas\.

The general steps you must take are as follows:

1. Determine the amount of space to provision for your Amazon Aurora PostgreSQL DB cluster\.

1. Use the console to create the snapshot in the region where the Amazon RDS PostgreSQL 9\.6\.1 instance is located\. For information about creating a DB snapshot, see [Creating a DB Snapshot](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateSnapshot.html)\.

1. If the DB snapshot is not in the same region as your DB cluster, use the Amazon RDS console to copy the DB snapshot to that region\. For information about copying a DB snapshot, see [Copying a DB Snapshot](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CopySnapshot.html)\.

1. Use the console to migrate the DB snapshot and create an Amazon Aurora PostgreSQL DB cluster with the same databases as the original PostgreSQL DB instance\. 

**Warning**  
Amazon RDS limits each AWS account to one snapshot copy into each AWS Region at a time\.