# Amazon RDS on AWS Outposts support for Amazon RDS features<a name="rds-on-outposts.features"></a>

The following table describes the Amazon RDS features supported by Amazon RDS on AWS Outposts\.


| Feature | Supported | Notes | More information | 
| --- | --- | --- | --- | 
|  DB instance provisioning  |  Yes  |  You can only create DB instances for RDS for SQL Server, RDS for MySQL, and RDS for PostgreSQL DB engines\. The following versions are supported: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-on-outposts.features.html)  |  [Creating DB instances for Amazon RDS on AWS Outposts](rds-on-outposts.creating.md)  | 
|  Connect to a Microsoft SQL Server DB instance with Microsoft SQL Server Management Studio  |  Yes  |  Some TLS versions and encryption ciphers might not be secure\. To turn them off, follow the instructions in [Configuring security protocols and ciphers](SQLServer.Ciphers.md)\.  |  [Connecting to a DB instance running the Microsoft SQL Server database engine](USER_ConnectToMicrosoftSQLServerInstance.md)  | 
|  Modifying the master user password  |  Yes  |  —  |  [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)  | 
|  Renaming a DB instance  |  Yes  |  —  |  [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)  | 
|  Rebooting a DB instance  |  Yes  |  —  |  [Rebooting a DB instance](USER_RebootInstance.md)  | 
|  Stopping a DB instance  |  Yes  |  —  |  [Stopping an Amazon RDS DB instance temporarily](USER_StopInstance.md)  | 
|  Starting a DB instance  |  Yes  |  —  |  [Starting an Amazon RDS DB instance that was previously stopped](USER_StartInstance.md)  | 
|  Multi\-AZ deployments  |  Yes  |  Multi\-AZ deployments are supported on MySQL and PostgreSQL DB instances\.  |  [Creating DB instances for Amazon RDS on AWS Outposts](rds-on-outposts.creating.md)  [Multi\-AZ deployments for high availability](Concepts.MultiAZ.md)  | 
|  DB parameter groups  |  Yes  |  —  |  [Working with parameter groups](USER_WorkingWithParamGroups.md)  | 
|  Read replicas  |  Yes  |  Read replicas are supported for MySQL and PostgreSQL DB instances\.  |  [Creating read replicas for Amazon RDS on AWS Outposts](rds-on-outposts.rr.md)  | 
|  Encryption at rest  |  Yes  |  RDS on Outposts doesn't support unencrypted DB instances\.  |  [Encrypting Amazon RDS resources](Overview.Encryption.md)  | 
|  AWS Identity and Access Management \(IAM\) database authentication  |  No  |  —  |  [IAM database authentication for MariaDB, MySQL, and PostgreSQL](UsingWithRDS.IAMDBAuth.md)  | 
|  Associating an IAM role with a DB instance  |  No  |  —  |  [add\-role\-to\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/add-role-to-db-instance.html) AWS CLI command  [AddRoleToDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_AddRoleToDBInstance.html) RDS API operation  | 
|  Kerberos authentication  |  No  |  —  |  [Kerberos authentication](database-authentication.md#kerberos-authentication)  | 
|  Tagging Amazon RDS resources  |  Yes  |  —  |  [Tagging Amazon RDS resources](USER_Tagging.md)  | 
|  Option groups  |  Yes  |  —  |  [Working with option groups](USER_WorkingWithOptionGroups.md)  | 
|  Modifying the maintenance window  |  Yes  |  —  |  [Maintaining a DB instance](USER_UpgradeDBInstance.Maintenance.md)  | 
|  Automatic minor version upgrade  |  Yes  |  —  |  [Automatically upgrading the minor engine version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.AutoMinorVersionUpgrades)  | 
|  Modifying the backup window  |  Yes  |  —  |  [Working with backups](USER_WorkingWithAutomatedBackups.md) [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)  | 
|  Changing the DB instance class  |  Yes  |  —  |  [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)  | 
|  Changing the allocated storage  |  Yes  |  —  |  [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)  | 
|  Storage autoscaling  |  Yes  |  —  |  [Managing capacity automatically with Amazon RDS storage autoscaling](USER_PIOPS.StorageTypes.md#USER_PIOPS.Autoscaling)  | 
|  Manual and automatic DB instance snapshots  |  Yes  |  You can store automated backups and manual snapshots in your AWS Region\. Or you can store them locally on your Outpost\. Local backups are supported on MySQL and PostgreSQL DB instances\. To store backups on your Outpost, make sure that you have Amazon S3 on Outposts configured\.  |  [Creating DB instances for Amazon RDS on AWS Outposts](rds-on-outposts.creating.md) [Amazon S3 on Outposts](https://aws.amazon.com/s3/outposts/) [Creating a DB snapshot](USER_CreateSnapshot.md)  | 
|  Restoring from a DB snapshot  |  Yes  |  You can store automated backups and manual snapshots for the restored DB instance in the parent AWS Region or locally on your Outpost\.  |  [Considerations for restoring DB instances on Amazon RDS on AWS Outposts](rds-on-outposts.restoring.md) [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)  | 
|  Restoring a DB instance from Amazon S3  |   No   |   —    |    [Restoring a backup into a MySQL DB instance](MySQL.Procedural.Importing.md)  | 
|  Exporting snapshot data to Amazon S3  |   No  |  —  |  [Exporting DB snapshot data to Amazon S3](USER_ExportSnapshot.md)  | 
|  Point\-in\-time recovery  |  Yes  |  You can store automated backups and manual snapshots for the restored DB instance in the parent AWS Region or locally on your Outpost, with one exception\.  |  [Considerations for restoring DB instances on Amazon RDS on AWS Outposts](rds-on-outposts.restoring.md) [Restoring a DB instance to a specified time](USER_PIT.md)  | 
|  Enhanced monitoring  |  No  |  —  |  [Monitoring OS metrics with Enhanced Monitoring](USER_Monitoring.OS.md)  | 
|  Amazon CloudWatch monitoring  |  Yes  |  You can view the same set of metrics that are available for your databases in the AWS Region\.  |  [Monitoring Amazon RDS metrics with Amazon CloudWatch](monitoring-cloudwatch.md)  | 
|  Publishing database engine logs to CloudWatch Logs  |  Yes  |  —  |  [Publishing database logs to Amazon CloudWatch Logs](USER_LogAccess.Procedural.UploadtoCloudWatch.md)  | 
|  Event notification  |  Yes  |  —  |  [Working with Amazon RDS event notification](USER_Events.md)  | 
|  Amazon RDS Performance Insights  |  No  |  —  |  [Monitoring DB load with Performance Insights on Amazon RDS](USER_PerfInsights.md)  | 
|  Viewing or downloading database logs  |  No  |  RDS on Outposts doesn't support viewing database logs using the console or describing database logs using the AWS CLI or RDS API\. RDS on Outposts doesn't support downloading database logs using the console or downloading database logs using the AWS CLI or RDS API\.  |  [Monitoring Amazon RDS log files](USER_LogAccess.md)  | 
|  Amazon RDS Proxy  |  No  |  —  |  [Using Amazon RDS Proxy](rds-proxy.md)  | 
|  Stored procedures for Amazon RDS for MySQL  |  Yes  |  —  |  [MySQL on Amazon RDS SQL reference](Appendix.MySQL.SQLRef.md)  | 
|  Replication with external databases for RDS for MySQL  |  No  |  —  |  [Configuring binary log file position replication with an external source instance](MySQL.Procedural.Importing.External.Repl.md)  | 
|  Native backup and restore for Amazon RDS for Microsoft SQL Server  |  Yes  |  —  |  [Importing and exporting SQL Server databases using native backup and restore](SQLServer.Procedural.Importing.md)  | 