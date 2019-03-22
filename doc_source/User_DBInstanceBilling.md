# DB Instance Billing for Amazon RDS<a name="User_DBInstanceBilling"></a>

Amazon RDS instances are billed based on the following components:
+ DB instance hours \(per hour\) – Based on the DB instance class of the DB instance \(for example, db\.t2\.small or db\.m4\.large\)\. Partial DB instance hours consumed are billed as full hours\. For more information, see [Choosing the DB Instance Class](Concepts.DBInstanceClass.md)\.
+ Storage \(per GiB per month\) – Storage capacity that you have provisioned to your DB instance\. If you scale your provisioned storage capacity within the month, your bill is pro\-rated\. For more information, see [DB Instance Storage](CHAP_Storage.md)\.
+ I/O requests \(per 1 million requests per month\) – Total number of storage I/O requests that you have made in a billing cycle, for Amazon RDS magnetic storage only\.
+ Provisioned IOPS \(per IOPS per month\) – Provisioned IOPS rate, regardless of IOPS consumed, for Amazon RDS Provisioned IOPS \(SSD\) Storage only\.
+ Backup storage \(per GiB per month\) – *Backup storage *is the storage that is associated with automated database backups and any active database snapshots that you have taken\. Increasing your backup retention period or taking additional database snapshots increases the backup storage consumed by your database\.

  For more information, see [Backing Up and Restoring Amazon RDS DB Instances](CHAP_CommonTasks.BackupRestore.md)\.
+ Data transfer \(per GB\) – Data transfer in and out of your DB instance from or to the internet and other AWS Regions\.

Amazon RDS provides the following purchasing options to enable you to optimize your costs based on your needs:
+ **On\-Demand Instances** – Pay by the hour for the DB instance hours that you use\.
+ **Reserved Instances** – Reserve a DB instance for a one\-year or three\-year term and receive a significant discount compared to the on\-demand DB instance pricing\.

For Amazon RDS pricing information, see the [Amazon RDS product page](https://aws.amazon.com/rds/pricing)\.

**Topics**
+ [On\-Demand DB Instances](USER_OnDemandDBInstances.md)
+ [Reserved DB Instances](USER_WorkingWithReservedDBInstances.md)