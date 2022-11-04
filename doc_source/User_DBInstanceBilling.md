# DB instance billing for Amazon RDS<a name="User_DBInstanceBilling"></a>

Amazon RDS instances are billed based on the following components:
+ DB instance hours \(per hour\) – Based on the DB instance class of the DB instance \(for example, db\.t2\.small or db\.m4\.large\)\. Pricing is listed on a per\-hour basis, but bills are calculated down to the second and show times in decimal form\. RDS usage is billed in 1\-second increments, with a minimum of 10 minutes\. For more information, see [DB instance classes](Concepts.DBInstanceClass.md)\.
+ Storage \(per GiB per month\) – Storage capacity that you have provisioned to your DB instance\. If you scale your provisioned storage capacity within the month, your bill is prorated\. For more information, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.
+ I/O requests \(per 1 million requests per month\) – Total number of storage I/O requests that you have made in a billing cycle, for Amazon RDS magnetic storage only\.
+ Provisioned IOPS \(per IOPS per month\) – Provisioned IOPS rate, regardless of IOPS consumed, for Amazon RDS Provisioned IOPS \(SSD\) and General Purpose \(SSD\) gp3 storage\. Provisioned storage for EBS volumes are billed in 1\-second increments, with a minimum of 10 minutes\.
+ Backup storage \(per GiB per month\) – *Backup storage *is the storage that is associated with automated database backups and any active database snapshots that you have taken\. Increasing your backup retention period or taking additional database snapshots increases the backup storage consumed by your database\. Per second billing doesn't apply to backup storage \(metered in GB\-month\)\.

  For more information, see [Backing up and restoring an Amazon RDS DB instance](CHAP_CommonTasks.BackupRestore.md)\.
+ Data transfer \(per GB\) – Data transfer in and out of your DB instance from or to the internet and other AWS Regions\.

Amazon RDS provides the following purchasing options to enable you to optimize your costs based on your needs:
+ **On\-Demand instances** – Pay by the hour for the DB instance hours that you use\. Pricing is listed on a per\-hour basis, but bills are calculated down to the second and show times in decimal form\. RDS usage is now billed in 1\-second increments, with a minimum of 10 minutes\.
+ **Reserved instances** – Reserve a DB instance for a one\-year or three\-year term and get a significant discount compared to the on\-demand DB instance pricing\. With Reserved Instance usage, you can launch, delete, start, or stop multiple instances within an hour and get the Reserved Instance benefit for all of the instances\.

For Amazon RDS pricing information, see the [Amazon RDS pricing page](https://aws.amazon.com/rds/pricing)\.

**Topics**
+ [On\-Demand DB instances for Amazon RDS](USER_OnDemandDBInstances.md)
+ [Reserved DB instances for Amazon RDS](USER_WorkingWithReservedDBInstances.md)