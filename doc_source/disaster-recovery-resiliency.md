# Resilience in Amazon RDS<a name="disaster-recovery-resiliency"></a>

The AWS global infrastructure is built around AWS Regions and Availability Zones\. AWS Regions provide multiple physically separated and isolated Availability Zones, which are connected with low\-latency, high\-throughput, and highly redundant networking\. With Availability Zones, you can design and operate applications and databases that automatically fail over between Availability Zones without interruption\. Availability Zones are more highly available, fault tolerant, and scalable than traditional single or multiple data center infrastructures\. 

For more information about AWS Regions and Availability Zones, see [AWS Global Infrastructure](https://aws.amazon.com/about-aws/global-infrastructure/)\.

In addition to the AWS global infrastructure, Amazon RDS offers features to help support your data resiliency and backup needs\.

## Backup and Restore<a name="disaster-recovery-resiliency.backup-restore"></a>

Amazon RDS creates and saves automated backups of your DB instance\. Amazon RDS creates a storage volume snapshot of your DB instance, backing up the entire DB instance and not just individual databases\.

Amazon RDS creates automated backups of your DB instance during the backup window of your DB instance\. Amazon RDS saves the automated backups of your DB instance according to the backup retention period that you specify\. If necessary, you can recover your database to any point in time during the backup retention period\. You can also back up your DB instance manually, by manually creating a DB snapshot\.

You can create a DB instance by restoring from this DB snapshot as a disaster recovery solution if the source DB instance fails\.

For more information, see [Backing Up and Restoring an Amazon RDS DB Instance](CHAP_CommonTasks.BackupRestore.md)\.

## Replication<a name="disaster-recovery-resiliency.replication"></a>

Amazon RDS uses the MariaDB, MySQL, Oracle, and PostgreSQL DB engines' built\-in replication functionality to create a special type of DB instance called a read replica from a source DB instance\. Updates made to the source DB instance are asynchronously copied to the read replica\. You can reduce the load on your source DB instance by routing read queries from your applications to the read replica\. Using read replicas, you can elastically scale out beyond the capacity constraints of a single DB instance for read\-heavy database workloads\. You can promote a read replica to a standalone instance as a disaster recovery solution if the source DB instance fails\. For some DB engines, Amazon RDS also supports other replication options\.

For more information, see [Working with Read Replicas](USER_ReadRepl.md)\.

## Failover<a name="disaster-recovery-resiliency.failover"></a>

Amazon RDS provides high availability and failover support for DB instances using Multi\-AZ deployments\. Amazon RDS uses several different technologies to provide failover support\. Multi\-AZ deployments for Oracle, PostgreSQL, MySQL, and MariaDB DB instances use Amazon's failover technology\. SQL Server DB instances use SQL Server Database Mirroring \(DBM\)\.

For more information, see [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)\.